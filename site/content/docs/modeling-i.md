---
title: 'Modeling I'

weight: 2
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA — M1: Modeling I (Commit Type Classification)

**Mode:** Test-driven assignment — [**tests are provided**](/code/test_m1_modeling.py) to define expected behavior.

**Builds on:** DC0/DC1/DC2 (Git mining) + DI1 (text normalization) + DA1 (identifier extraction) + DA2 (vocabulary clustering). M1 uses the cluster assignments and vocabulary analysis from DA2 as input features for a supervised classification model.

---

## Goal

Implement a supervised learning pipeline that predicts **commit type** (fix, feature, refactor, test, docs, or other) from vocabulary features derived from your DA2 clustering results.

The central research question: **Can we predict what kind of change a commit introduces, just from its vocabulary cluster membership?**

By the end of M1 you will:
- Engineer numerical features from the cluster assignments produced by DA2
- Train a decision tree (and optionally a random forest)
- Evaluate the model with accuracy, per-class F1, and a confusion matrix
- Use **feature importance** to discover which vocabulary clusters are most predictive

Feature importance represents the assignment's key learning objective: the bar chart will show cluster names like `cluster_0_frac` and `cluster_2_frac`. If you labeled those clusters "bug-fix vocabulary" and "auth vocabulary" in your DA2 alignment report, you can now say which of your semantic clusters best predicts commit type. This bridges the unsupervised exploration (DA2) and supervised prediction (M1) assignments, highlighting how clustering can be used to produce features for predictive modeling.

---

## Learning Outcomes

1. Apply heuristic labeling to create a training dataset from unlabeled text
2. Engineer numerical features from cluster membership and vocabulary overlap
3. Build a feature matrix from a corpus of commit messages
4. Perform a stratified train/test split to handle imbalanced class distributions
5. Train and evaluate a decision tree classifier using sklearn
6. Interpret feature importances and confusion matrices in an MSR context

---

## What You Will Implement

You must implement these functions in `src/m1_modeling.py`:

**Required for all students:**
- `label_commit()` — heuristic commit type labeler
- `build_commit_features()` — feature vector for one commit
- `build_feature_matrix()` — feature matrix + labels for all commits
- `split_dataset()` — stratified train/test split
- `train_classifier()` — fit a decision tree or random forest
- `evaluate_model()` — accuracy, per-class F1, confusion matrix

**Provided (no implementation required):**
- `plot_feature_importance()` — sorted bar chart of feature importances
- `plot_confusion_matrix()` — annotated heatmap of true vs. predicted labels
- `load_commit_data()` — loads commit records from the DB

---

### Function Signatures & Docstrings

```python
from typing import Any, Dict, List, Optional, Tuple
import numpy as np

# ---------------------------------------------------------------------------
# Commit type classification constants
# ---------------------------------------------------------------------------

COMMIT_TYPE_KEYWORDS: Dict[str, List[str]] = {
    "fix":      ["fix", "bug", "patch", "hotfix", "error", "repair",
                 "correct", "typo", "broke", "broken", "revert", "crash", "fail"],
    "feature":  ["feat", "add", "new", "implement", "introduce", "create",
                 "support", "feature", "initial", "allow", "enable"],
    "refactor": ["refactor", "cleanup", "clean", "reorganize", "rename",
                 "restructure", "move", "extract", "simplify", "rewrite", "split"],
    "test":     ["test", "tests", "spec", "specs", "assert", "coverage",
                 "mock", "pytest", "unittest"],
    "docs":     ["doc", "docs", "readme", "changelog", "documentation",
                 "guide", "docstring"],
}

_TYPE_PRIORITY = ["fix", "feature", "refactor", "test", "docs"]


def label_commit(message: str) -> str:
    """Classify a commit message into one of six commit types.

    Why this matters for MSR:
    - Commit type is a widely studied metadata attribute in empirical SE
    - Heuristic labeling enables supervised learning without manual annotation
    - Imperfect labels motivate precision/recall analysis vs. keyword matching
    - Studying label quality is itself a valid research contribution

    Parameters:
    - message: raw commit message string

    Returns:
    - One of: "fix", "feature", "refactor", "test", "docs", "other"

    Behavior:
    - Lowercase the message before matching
    - Check types in priority order: fix > feature > refactor > test > docs
    - Use whole-word matching (\b...\b) to avoid false positives -- check regex docs if you don't understand \b
    - Return "other" if no keywords match or message is empty/whitespace

    Examples:
    >>> label_commit("Fix null pointer bug in authentication")
    'fix'
    >>> label_commit("Add new user registration endpoint")
    'feature'
    >>> label_commit("Update README with installation instructions")
    'docs'
    >>> label_commit("")
    'other'

    Implementation hints:
    - message.lower() for case normalization
    - re.search(r'\b' + re.escape(kw) + r'\b', text) for whole-word matching
    - Iterate _TYPE_PRIORITY; return the first matching label. That is, if a commit has multiple labels, we pick the first that matches.
    - COMMIT_TYPE_KEYWORDS[label] gives the keyword list for each label
    """
    # TODO: Implement
    pass


def build_commit_features(
    tokens: List[str],
    token_to_cluster: Dict[str, int],
    k: int,
    identifier_tokens: Optional[List[str]] = None,
    comment_tokens: Optional[List[str]] = None,
) -> np.ndarray:
    """Build a numerical feature vector for a single commit.

    Why this matters for MSR:
    - Transforms raw text into structured numerical features for sklearn
    - Cluster membership captures semantic themes (e.g., 'bug-fix vocabulary')
    - Cross-source overlap reveals how closely commits relate to code artifacts
    - Type-token ratio captures lexical richness of commit messages

    Parameters:
    - tokens: normalized tokens for this commit (from extract_vocabulary)
    - token_to_cluster: dict mapping token -> cluster_id (from DA2 clustering)
    - k: number of clusters (must match DA2 k)
    - identifier_tokens: vocabulary from code identifiers (for overlap feature)
    - comment_tokens: vocabulary from code comments (for overlap feature)

    Returns:
    - np.ndarray of shape (k + 4,) with these features in order:
        [cluster_0_frac, cluster_1_frac, ..., cluster_{k-1}_frac,
         log_token_count, type_token_ratio, id_overlap, comment_overlap]

    Feature descriptions:
    - cluster_i_frac: fraction of *all* commit tokens assigned to cluster i.
        Tokens not in token_to_cluster are not assigned to any cluster, so
        they lower the cluster fractions without contributing to any numerator.
        Fracs sum to 1.0 only when every token is in token_to_cluster,
        and to less than 1.0 when any tokens are out-of-vocabulary.
    - log_token_count: log(1 + len(tokens)), captures message verbosity.
        np.log1p handles the empty-list case (returns 0.0).
    - type_token_ratio: len(unique tokens) / len(tokens), lexical diversity.
        0.0 for an empty token list.
    - id_overlap: Jaccard similarity of commit token set vs. identifier token set.
        |set(tokens) ∩ set(id_tokens)| / |set(tokens) ∪ set(id_tokens)|
        0.0 if both sets are empty.
    - comment_overlap: same calculation but vs. comment tokens.

    Examples:
    >>> token_to_cluster = {"fix": 0, "bug": 0, "auth": 1}
    >>> tokens = ["fix", "bug", "auth"]
    >>> features = build_commit_features(tokens, token_to_cluster, k=2)
    >>> features.shape
    (6,)
    >>> round(features[0], 4)   # cluster_0_frac: 2 of 3 tokens in cluster 0
    0.6667
    >>> round(features[1], 4)   # cluster_1_frac: 1 of 3 tokens in cluster 1
    0.3333

    Implementation hints:
    - np.zeros(k + 4, dtype=float) to initialise the output
    - Use collections.Counter or a loop to count tokens per cluster
    - Only increment a cluster's counter if the token is in token_to_cluster
    - Divide cluster counts by len(tokens) (total tokens, not just matched ones)
    - np.log1p(n) == log(1 + n)
    - For Jaccard: use Python set operations (&, |)
    - Return zeros for empty token list (don't divide by zero)
    """
    # TODO: Implement
    pass


def build_feature_matrix(
    commit_records: List[Dict[str, str]],
    k: int = 5,
    token_to_cluster: Optional[Dict[str, int]] = None,
    identifier_tokens: Optional[List[str]] = None,
    comment_tokens: Optional[List[str]] = None,
) -> Tuple[np.ndarray, List[str], List[str]]:
    """Build feature matrix and label vector for all commits.

    Why this matters for MSR:
    - Translates the entire commit corpus into a form usable by sklearn
    - Connects DA2 vocabulary analysis to supervised classification
    - Enables reproducible feature engineering pipelines across repos

    Parameters:
    - commit_records: list of dicts, each with at least a "message" key.
        Use load_commit_data() (provided) to obtain this from the DB.
    - k: number of vocabulary clusters — should match the k used in DA2
    - token_to_cluster: pre-built token->cluster mapping from DA2 (you pass your DA2 data into here).
        If None, re-run your DA2 clustering via `extract_vocabulary` and `cluster_vocabulary` inline with this function
    - identifier_tokens: full identifier token list from DB (for overlap)
    - comment_tokens: full comment token list from DB (for overlap)

    Returns:
    - X: np.ndarray of shape (n_commits, k + 4) — feature matrix
    - y: List[str] of length n_commits — commit type labels
    - feature_names: List[str] of length k + 4 — column names for X

    Feature names:
    ["cluster_0_frac", ..., "cluster_{k-1}_frac",
     "log_token_count", "type_token_ratio", "id_overlap", "comment_overlap"]

    Connecting to DA2:
    If you cannot easily pass token_to_cluster into this function from main, you can just re-run da2's code here.
    Build token_to_cluster from your DA2 cluster_vocabulary() results:

        commit_tokens = da2_vocabulary.extract_vocabulary(commit_messages)
        labels, vectors, model = da2_vocabulary.cluster_vocabulary(commit_tokens, k=5)
        token_to_cluster = {t: int(l) for t, l in zip(commit_tokens, labels)}

    Then pass it here so your M1 features directly reflect your DA2 clusters.

    Examples:
    >>> records = [{"message": "fix authentication bug"},
    ...            {"message": "add user registration"}]
    >>> t2c = {"fix": 0, "bug": 0, "auth": 0, "add": 1, "user": 1}
    >>> X, y, names = build_feature_matrix(records, k=2, token_to_cluster=t2c)
    >>> X.shape
    (2, 6)
    >>> y
    ['fix', 'feature']

    Implementation hints:
    - if you are not passing DA2's data in as a parameter
        - from src.da2_vocabulary import extract_vocabulary, cluster_vocabulary
        - Call extract_vocabulary([msg]) for each commit individually (not pooled)
        - If token_to_cluster is None: pool all tokens, cluster them, build dict
    - Stack build_commit_features() results with np.array([...]) -- that is, you can build an np.array with the results of build_commit_features i.e., via looping.
    - Use label_commit(msg) for each message to get y
    - Return np.zeros((0, k + 4)), [], feature_names for empty input
    """
    # TODO: Implement
    pass


def split_dataset(
    X: np.ndarray,
    y: List[str],
    test_size: float = 0.2,
    random_state: int = 42,
) -> Tuple[np.ndarray, np.ndarray, List[str], List[str]]:
    """Split features and labels into stratified training and test sets.

    Why this matters for MSR:
    - Held-out test data prevents overfitting evaluation (information leakage)
    - Stratification preserves class proportions — critical for imbalanced data
    - Commit type distributions are typically skewed ('fix' >> 'docs')
    - Reproducible splits (random_state) allow fair comparison of models

    Parameters:
    - X: feature matrix of shape (n_samples, n_features)
    - y: label list of length n_samples
    - test_size: fraction of data for testing (default 0.2 = 20%)
    - random_state: random seed for reproducibility (default 42)

    Returns:
    - (X_train, X_test, y_train, y_test)

    Behavior:
    - Attempt stratified split (preserves class proportions)
    - If any class has < 2 samples, fall back to non-stratified and print a warning

    Examples:
    >>> X = np.random.rand(100, 9)
    >>> y = ["fix"] * 50 + ["feature"] * 30 + ["other"] * 20
    >>> X_train, X_test, y_train, y_test = split_dataset(X, y)
    >>> len(X_train) + len(X_test)
    100

    Implementation hints:
    - from sklearn.model_selection import train_test_split
    - from collections import Counter
    - Check: all(c >= 2 for c in Counter(y).values())
    - If check fails: call train_test_split with stratify=None and print warning
    - Return (X_train, X_test, y_train, y_test) in that order
    """
    # TODO: Implement
    pass


def train_classifier(
    X_train: np.ndarray,
    y_train: List[str],
    model_type: str = "decision_tree",
    max_depth: Optional[int] = None,
) -> Any:
    """Train a decision tree or random forest classifier.

    Why this matters for MSR:
    - Decision trees are interpretable: each split is a human-readable rule
    - Random forests average many trees, trading interpretability for accuracy
    - Feature importances from either model connect predictions to DA2 clusters by showing which was most helpful
    - sklearn's consistent fit/predict API makes swapping models trivial

    Parameters:
    - X_train: training feature matrix of shape (n_samples, n_features)
    - y_train: training labels of length n_samples
    - model_type: "decision_tree" or "random_forest"
    - max_depth: maximum tree depth (None = unlimited; try 5 to limit overfitting)

    Returns:
    - Fitted sklearn classifier (has .predict() and .feature_importances_)

    Raises:
    - ValueError if model_type is not "decision_tree" or "random_forest"

    Examples:
    >>> model = train_classifier(X_train, y_train, model_type="decision_tree")
    >>> model.predict(X_test[:3])
    array(['fix', 'feature', 'other'], dtype=object)

    Implementation hints:
    - from sklearn.tree import DecisionTreeClassifier
    - from sklearn.ensemble import RandomForestClassifier
    - Use random_state=42 for reproducibility in both models
    - RandomForestClassifier: n_estimators=100 is a good default
    - Call model.fit(X_train, y_train) before returning
    - Raise ValueError with a helpful message for unknown model_type
    """
    # TODO: Implement
    pass


def evaluate_model(
    model: Any,
    X_test: np.ndarray,
    y_test: List[str],
) -> Dict[str, Any]:
    """Evaluate a trained classifier and return standard metrics.

    Why this matters for MSR:
    - Accuracy alone is misleading when classes are imbalanced
    - Per-class precision/recall/F1 reveals which commit types are hardest to predict
    - The confusion matrix shows which types the model most often confuses
    - These metrics are standard in empirical software engineering papers

    Parameters:
    - model: fitted sklearn classifier with a .predict() method
    - X_test: test feature matrix
    - y_test: true test labels

    Returns:
    - dict with keys:
        - "accuracy": float — overall fraction of correct predictions
        - "classification_report": dict — per-class precision/recall/F1
            (sklearn classification_report output with output_dict=True)
        - "confusion_matrix": np.ndarray — shape (n_classes, n_classes)
        - "class_names": List[str] — sorted unique labels from y_test
        - "y_pred": np.ndarray — model predictions on X_test

    Examples:
    >>> results = evaluate_model(model, X_test, y_test)
    >>> results["accuracy"]
    0.73
    >>> results["classification_report"]["fix"]["f1-score"]
    0.81

    Implementation hints:
    - from sklearn.metrics import accuracy_score, classification_report, confusion_matrix
    - y_pred = model.predict(X_test)
    - class_names = sorted(set(y_test))
    - classification_report(..., output_dict=True, zero_division=0)
    - confusion_matrix(..., labels=class_names)
    """
    # TODO: Implement
    pass
```

---

## Provided Functions

Copy these into your `src/m1_modeling.py` without modification:

```python
def plot_feature_importance(
    model: Any,
    feature_names: List[str],
    output_path: Optional[str] = None,
) -> None:
    """Bar chart of feature importances, sorted descending.

    **Provided — copy this into your m1_modeling.py as-is.**
    """
    importances = model.feature_importances_
    indices = np.argsort(importances)[::-1]

    plt.figure(figsize=(10, 6))
    plt.bar(
        range(len(importances)),
        importances[indices],
        color='steelblue',
        edgecolor='black',
        linewidth=0.5,
    )
    plt.xticks(
        range(len(importances)),
        [feature_names[i] for i in indices],
        rotation=45,
        ha='right',
        fontsize=9,
    )
    plt.xlabel("Feature", fontsize=12)
    plt.ylabel("Importance", fontsize=12)
    plt.title("Feature Importances", fontsize=14, fontweight='bold')
    plt.grid(axis='y', alpha=0.3)
    plt.tight_layout()

    if output_path:
        plt.savefig(output_path, dpi=300, bbox_inches='tight')
        plt.close()
    else:
        plt.show()


def plot_confusion_matrix(
    y_true: List[str],
    y_pred: Any,
    class_names: List[str],
    output_path: Optional[str] = None,
) -> None:
    """Annotated heatmap of predicted vs. true commit type labels.

    **Provided — copy this into your m1_modeling.py as-is.**
    """
    from sklearn.metrics import confusion_matrix as sk_cm

    cm = sk_cm(y_true, y_pred, labels=class_names)

    plt.figure(figsize=(8, 6))
    im = plt.imshow(cm, interpolation='nearest', cmap='Blues')
    plt.colorbar(im, label='Count')
    plt.title('Confusion Matrix', fontsize=14, fontweight='bold')
    plt.xlabel('Predicted Label', fontsize=12)
    plt.ylabel('True Label', fontsize=12)

    tick_marks = np.arange(len(class_names))
    plt.xticks(tick_marks, class_names, rotation=45, ha='right', fontsize=9)
    plt.yticks(tick_marks, class_names, fontsize=9)

    thresh = cm.max() / 2.0
    for i in range(cm.shape[0]):
        for j in range(cm.shape[1]):
            plt.text(
                j, i, str(cm[i, j]),
                ha='center', va='center',
                color='white' if cm[i, j] > thresh else 'black',
                fontsize=10,
            )

    plt.tight_layout()

    if output_path:
        plt.savefig(output_path, dpi=300, bbox_inches='tight')
        plt.close()
    else:
        plt.show()


def load_commit_data(commit_limit: Optional[int] = None) -> List[Dict[str, str]]:
    """Load commit records from the database.

    **Provided — copy this into your m1_modeling.py as-is.**

    Returns a list of {"message": str} dicts, ready for build_feature_matrix().
    """
    from src import db_utils

    query = (
        "SELECT message FROM commits "
        "WHERE message IS NOT NULL AND message <> ''"
    )
    if commit_limit:
        query += f" LIMIT {int(commit_limit)}"
    query += ";"

    try:
        rows = db_utils.exec_get_all(query)
    except Exception as exc:
        print(f"[load_commit_data] DB query failed: {exc}")
        return []

    return [
        {"message": str(row[0])}
        for row in rows
        if row[0] and str(row[0]).strip()
    ]
```

---

## Connecting M1 to DA2

Your DA2 alignment report included cluster theme labels like "bug-fix cluster" or "auth cluster". M1 uses those clusters as features.

Here's how to build `token_to_cluster` from your DA2 results:

```python
from src import da2_vocabulary

# Re-run (or reload) your DA2 clustering
commit_rows = db_utils.exec_get_all("SELECT message FROM commits LIMIT 500;")
commit_messages = [row[0] for row in commit_rows if row[0]]
commit_tokens = da2_vocabulary.extract_vocabulary(commit_messages)
labels, vectors, model = da2_vocabulary.cluster_vocabulary(commit_tokens, k=5)

# Build the lookup used by build_commit_features
token_to_cluster = {t: int(l) for t, l in zip(commit_tokens, labels)}

```

Then pass `token_to_cluster` to `build_feature_matrix`:

```python
from src import m1_modeling, db_utils

# Load per-commit records
commit_records = m1_modeling.load_commit_data()

# Load identifier and comment tokens for overlap features (optional but recommended)
id_rows = db_utils.exec_get_all("SELECT name FROM code_identifiers;")
identifier_tokens = da2_vocabulary.extract_vocabulary([r[0] for r in id_rows if r[0]])

cm_rows = db_utils.exec_get_all("SELECT comment_text FROM code_comments;")
comment_tokens = da2_vocabulary.extract_vocabulary([r[0] for r in cm_rows if r[0]])

# Build the feature matrix — cluster features directly reflect DA2 results
X, y, feature_names = m1_modeling.build_feature_matrix(
    commit_records,
    k=5,
    token_to_cluster=token_to_cluster,
    identifier_tokens=identifier_tokens,
    comment_tokens=comment_tokens,
)
```

If `token_to_cluster` is `None`, `build_feature_matrix` will cluster the commit corpus automatically and the feature columns will still correspond to vocabulary clusters — just likely not the exact same ones you labeled in DA2.

---

## Example Workflow

### Via the pipeline (recommended)

```bash
# Stage 1 — mine data (already done from DA2)
python main.py mine pallets/flask --token $TOKEN --ingest --depth 2 # Small depth, we don't need to mine the whole history

# Stage 2 — DA2 vocabulary clustering
python main.py analyze --output-dir output/ --clusters 5

# Stage 3 — M1 classification -- unless you group predict with analyze, in which case you'd just add --model-type to stage 2. Use decision_tree as a default.
python main.py predict --output-dir output/ --clusters 5
python main.py predict --model-type random_forest --max-depth 5
```

### Manual (useful for debugging individual functions)

```python
from src import m1_modeling, da2_vocabulary, db_utils

# 1. Load commits
commit_records = m1_modeling.load_commit_data(commit_limit=500)
print(f"Loaded {len(commit_records)} commits")

# 2. Check label distribution
from collections import Counter
labels_all = [m1_modeling.label_commit(r["message"]) for r in commit_records]
print(Counter(labels_all))

# 3. Build feature matrix (passing token_to_cluster from DA2 is optional)
X, y, feature_names = m1_modeling.build_feature_matrix(commit_records, k=5)
print(f"Feature matrix: {X.shape}")
print(f"Feature names: {feature_names}")

# 4. Split
X_train, X_test, y_train, y_test = m1_modeling.split_dataset(X, y, test_size=0.2)
print(f"Train: {len(X_train)}, Test: {len(X_test)}")

# 5. Train a decision tree
model = m1_modeling.train_classifier(X_train, y_train,
                                     model_type="decision_tree", max_depth=5)

# 6. Evaluate
results = m1_modeling.evaluate_model(model, X_test, y_test)
print(f"Accuracy: {results['accuracy']:.1%}")
print(results["classification_report"])

# 7. Visualize
m1_modeling.plot_feature_importance(
    model, feature_names, output_path="output/feature_importance.png"
)
m1_modeling.plot_confusion_matrix(
    y_test, results["y_pred"], results["class_names"],
    output_path="output/confusion_matrix.png"
)
```

---

## Adding a `predict` Command to `main.py`

Add the following to your `main.py` so you can run the full pipeline from the command line. This follows the same pattern as `cmd_analyze`.

It is perhaps better to just combine this behavior *into* analyze, but you are provided this function to make sure you understand the full workflow

Whether you seperate analyze from predict or combine them into one is a design decision that you will need to make.

```python
def cmd_predict(args) -> None:
    """Train a commit-type classifier from DB data and write evaluation outputs.

    No network calls.  Reads commits, identifiers, and comments from the DB
    (populated by 'mine'), then runs the full M1 pipeline.

    Produces:
      <output_dir>/feature_importance.png   — which features matter most
      <output_dir>/confusion_matrix.png     — where the model gets confused
      <output_dir>/model_report.txt         — accuracy, per-class F1, interpretation

    Usage:
        python main.py predict
        python main.py predict --output-dir output/ --clusters 5
        python main.py predict --model-type random_forest --max-depth 5
    """
    from src import m1_modeling, da2_vocabulary

    out = args.output_dir
    os.makedirs(out, exist_ok=True)
    k = args.clusters

    # 1. Load commit records from DB
    print("Loading commit data from DB...")
    commit_records = m1_modeling.load_commit_data(
        commit_limit=getattr(args, "commit_limit", None)
    )
    print(f"  {len(commit_records)} commits loaded")

    if not commit_records:
        print("No commits found. Run 'mine' first.")
        return

    # 2. (Optional) Load identifier and comment tokens for overlap features
    try:
        id_rows = db_utils.exec_get_all("SELECT name FROM code_identifiers;")
        identifier_tokens = da2_vocabulary.extract_vocabulary(
            [r[0] for r in id_rows if r[0]]
        )
        cm_rows = db_utils.exec_get_all("SELECT comment_text FROM code_comments;")
        comment_tokens = da2_vocabulary.extract_vocabulary(
            [r[0] for r in cm_rows if r[0]]
        )
    except Exception as e:
        print(f"  Warning: could not load identifier/comment tokens: {e}")
        identifier_tokens = []
        comment_tokens = []

    # 3. Build feature matrix
    print(f"Building feature matrix (k={k})...")
    X, y, feature_names = m1_modeling.build_feature_matrix(
        commit_records,
        k=k,
        identifier_tokens=identifier_tokens,
        comment_tokens=comment_tokens,
    )
    print(f"  X shape: {X.shape}")
    print(f"  Label distribution: {dict(Counter(y))}")

    if len(set(y)) < 2:
        print("Only one label class found — model cannot be trained.")
        return

    # 4. Train/test split
    X_train, X_test, y_train, y_test = m1_modeling.split_dataset(
        X, y, test_size=0.2
    )

    # 5. Train
    model_type = getattr(args, "model_type", "decision_tree")
    print(f"Training {model_type}...")
    model = m1_modeling.train_classifier(X_train, y_train, model_type=model_type)

    # 6. Evaluate
    from sklearn.metrics import classification_report as sk_clf_report

    results = m1_modeling.evaluate_model(model, X_test, y_test)
    clf_report_text = sk_clf_report(
        y_test, results["y_pred"],
        labels=results["class_names"],
        zero_division=0,
    )
    print(f"  Accuracy: {results['accuracy']:.1%}")
    print()
    print("Classification report:")
    print(clf_report_text)

    # 7. Plots — wrapped in try/except so one failure doesn't block the report
    fi_path = os.path.join(out, "feature_importance.png")
    cm_path = os.path.join(out, "confusion_matrix.png")

    try:
        m1_modeling.plot_feature_importance(model, feature_names, output_path=fi_path)
        print(f"  -> {fi_path}")
    except Exception as exc:
        print(f"  Warning: could not save feature_importance.png: {exc}")

    try:
        m1_modeling.plot_confusion_matrix(
            y_test, results["y_pred"], results["class_names"], output_path=cm_path
        )
        print(f"  -> {cm_path}")
    except Exception as exc:
        print(f"  Warning: could not save confusion_matrix.png: {exc}")

    # 8. Write model report
    report_lines = [
        "M1 MODEL REPORT",
        "=" * 40,
        "",
        f"Model type:  {model_type}",
        f"Features:    {len(feature_names)}",
        f"Train size:  {len(X_train)}",
        f"Test size:   {len(X_test)}",
        f"Accuracy:    {results['accuracy']:.1%}",
        "",
        "Classification report (test set):",
        "-" * 36,
        clf_report_text,
        "Feature importances (descending):",
        "-" * 36,
    ]
    importances = model.feature_importances_
    ranked = sorted(zip(feature_names, importances), key=lambda x: -x[1])
    for name, imp in ranked:
        report_lines.append(f"  {name:<25} {imp:.4f}")
    report_lines += [
        "",
        "Interpretation:",
        "  [TODO: Write 2-3 sentences interpreting your results here]",
        "  Which clusters are most predictive? What does the confusion",
        "  matrix tell you about which commit types are hardest to classify?",
    ]

    report_path = os.path.join(out, "model_report.txt")
    with open(report_path, "w", encoding="utf-8") as f:
        f.write("\n".join(report_lines))
    for line in report_lines:
        print(line)
    print(f"  -> {report_path}")
```

Then register the subcommand in your `main()`:

```python
# In the sub.add_parser block:
pp = sub.add_parser("predict", help="Train commit-type classifier and write evaluation outputs (M1)")
pp.add_argument("--output-dir",   default="output")
pp.add_argument("--clusters", "-k", type=int, default=5)
pp.add_argument("--model-type",   default="decision_tree",
                choices=["decision_tree", "random_forest"])
pp.add_argument("--max-depth",    type=int, default=None)
pp.add_argument("--commit-limit", type=int, default=None)
pp.set_defaults(func=cmd_predict)
```

You will also need to add `from collections import Counter` to your imports if it's not already there.

---

## Provided Tests

Tests are provided in [`test_m1_modeling.py`](/code/test_m1_modeling.py) covering:

**TestLabelCommit (10 tests):**
- Fix keywords: "fix", "bug", "patch"
- Feature keywords: "add", "implement", "feat"
- Refactor, test, docs keywords
- Empty string -> "other"
- Case insensitivity

**TestBuildCommitFeatures (10 tests):**
- Returns np.ndarray of shape (k + 4,)
- Shape adapts when k changes
- Cluster fractions sum to 1.0 when all tokens are known
- Cluster fractions < 1.0 when unknown tokens are present
- Empty tokens -> all zeros
- log_token_count > 0 for non-empty commits
- type_token_ratio in [0.0, 1.0]
- id_overlap > 0 when overlap exists
- All values are finite

**TestBuildFeatureMatrix (8 tests):**
- Returns (X, y, feature_names) tuple
- X is ndarray of shape (n_commits, k + 4)
- y and feature_names have correct lengths
- y contains only valid label strings
- Empty input -> (0, k+4) shaped X
- All X values are finite

**TestSplitDataset (6 tests):**
- Returns (X_train, X_test, y_train, y_test)
- Test size approximately correct
- Stratified: all classes appear in test set
- Reproducible with same random_state
- Train + test = full dataset

**TestTrainClassifier (8 tests):**
- "decision_tree" and "random_forest" return fitted models
- Unknown model_type raises ValueError
- .predict() returns correct-length predictions of valid labels
- max_depth parameter respected
- Model exposes .feature_importances_

**TestEvaluateModel (6 tests):**
- Returns dict with "accuracy", "confusion_matrix", "class_names", "y_pred"
- Accuracy is in [0.0, 1.0]
- Confusion matrix is 2D array
- Perfect classifier -> accuracy 1.0

**Total: 48 tests**

Run with:

```bash
pytest test/test_m1_modeling.py -v
```

---

## Required Dependencies

These should already be in your `requirements.txt` from DA2:

```
scikit-learn>=1.0.0
matplotlib>=3.5.0
numpy>=1.21.0
```

No new dependencies are required for M1.

---

## Deliverables

1. **Implementation** of all required functions in `src/m1_modeling.py`
2. **All 48 tests passing** in `test/test_m1_modeling.py`
3. **`output/feature_importance.png`** — bar chart showing which features matter most
4. **`output/confusion_matrix.png`** — heatmap of true vs. predicted commit types
5. **`output/model_report.txt`** containing:
   - Model type, feature count, train/test sizes
   - Overall accuracy
   - Per-class precision, recall, F1, and support
   - Feature importances ranked by value
   - **2–3 sentence interpretation** (see Research Questions below)

---

## Research Questions to Explore

Your model report interpretation should address at least two of the following:

1. **Which vocabulary clusters are most predictive of commit type?**
   - Look at your feature importance plot
   - Name the high-importance cluster features using your DA2 theme labels
   - e.g., "cluster_2_frac (refactor vocabulary) was the most predictive feature"

2. **Which commit types are hardest to classify?**
   - Examine the confusion matrix off-diagonal cells
   - Are "feature" and "refactor" frequently confused? Why might that be?
   - Does low support (few training examples) explain poor recall on rare types?

3. **How does vocabulary overlap with code affect classification?**
   - Compare `id_overlap` and `comment_overlap` importances
   - Are commits that use more code-like vocabulary easier to type-classify?

4. **What does labeling quality tell you?**
   - The heuristic labeler is intentionally simple — how might labeling errors
     affect the model's F1 scores?
   - Could you improve the labeler? What would you change?

---

## Grading Outline

| Component | Weight |
|---|---|
| Commit type labeling (`label_commit`) | 10% |
| Feature engineering (`build_commit_features`) | 15% |
| Feature matrix construction (`build_feature_matrix`) | 15% |
| Train/test split (`split_dataset`) | 10% |
| Model training (`train_classifier`) | 15% |
| Model evaluation (`evaluate_model`) | 10% |
| Deliverable outputs + model report interpretation | 25% |

---

## Suggested File Workflow

1. **Phase 1: Commit type labeling**
   - Implement `label_commit`
   - Run `TestLabelCommit` tests
   - Print label distribution on your mined commits to sanity-check:
     ```python
     from collections import Counter
     records = m1_modeling.load_commit_data()
     print(Counter(m1_modeling.label_commit(r["message"]) for r in records))
     ```

2. **Phase 2: Feature engineering**
   - Implement `build_commit_features`
   - Run `TestBuildCommitFeatures` tests
   - Try it manually with a hand-crafted `token_to_cluster` to verify feature values

3. **Phase 3: Feature matrix**
   - Implement `build_feature_matrix`
   - Run `TestBuildFeatureMatrix` tests
   - Print `X.shape` and `feature_names` to verify the output

4. **Phase 4: Train/test split**
   - Implement `split_dataset`
   - Run `TestSplitDataset` tests
   - Print class counts in `y_train` and `y_test` to confirm stratification

5. **Phase 5: Model training**
   - Implement `train_classifier`
   - Run `TestTrainClassifier` tests
   - Start with `model_type="decision_tree"` and `max_depth=5`

6. **Phase 6: Evaluation**
   - Implement `evaluate_model`
   - Run `TestEvaluateModel` tests
   - Copy `plot_feature_importance` and `plot_confusion_matrix` into your file

7. **Phase 7: Integration**
   - Add `cmd_predict` to `main.py` by combining `predict` with `analyze` or creating a separate function for `predict` (see example above)
   - Run `python main.py predict --output-dir output/ --clusters 5`
   - Confirm `feature_importance.png`, `confusion_matrix.png`, and
     `model_report.txt` are written to `output/`

8. **Phase 8: Interpret and write the model report**
   - Fill in the "Interpretation" section of `output/model_report.txt`
   - Name the high-importance features using your DA2 cluster theme labels
   - Address at least two of the Research Questions above

---

## Hints

- **Start with label_commit:** It's the simplest function and gives you immediate feedback on your data
- **Check label distribution early:** If >80% of your commits are "other", your keyword list may be too narrow — add more terms to `COMMIT_TYPE_KEYWORDS`
- **Feature fractions may not sum to 1.0:** Tokens not in `token_to_cluster` are silently skipped; this is expected
- **Small datasets:** With < 100 labeled commits, accuracy is noisy — focus on relative feature importances rather than absolute accuracy
- **max_depth matters:** An unlimited tree (`max_depth=None`) will overfit; `max_depth=5` is a good starting point
- **Random forest takes longer:** Use `decision_tree` during development and switch to `random_forest` for your final report
- **"other" class usually has low recall:** Keyword labeling creates a residual "other" bucket that's hard to predict — acknowledge this in your interpretation
- **Feature names carry over from DA2:** If you rename clusters in your DA2 report (e.g., cluster 0 -> "bug-fix vocabulary"), note which cluster index that corresponds to when reading feature importances

---
