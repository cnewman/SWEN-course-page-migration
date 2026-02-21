---
title: 'Data Analysis II'

weight: 2
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA - DA2: Data Analysis II - Vocabulary Clustering, Alignment & Feature Engineering

**Mode:** Test-driven assignment - [**tests are provided**](/code/test_da2_vocabulary.py) to define expected behavior.

**Builds on:** DC0/DC1/DC2 (Git mining) + DI1 (text normalization) + DA1 (identifier extraction). This assignment integrates multiple data sources through clustering and visualization, and produces the cluster analysis that feeds into our next assignment, **M1 (Modeling I)**.

---

## Goal

Implement clustering and visualization functions that compare **three vocabulary sources** from software repositories:
1. **Commit messages** - what developers write about their changes
2. **Identifier names** - what the code is actually called
3. **Code comments** - what developers document in-line

By clustering these vocabularies and measuring their alignment, you'll learn whether developers' natural language descriptions match the terminology used in the code itself.

This week reinforces research methods through implementation:
- Extract and normalize text from multiple sources
- Apply unsupervised learning (clustering) to discover patterns
- Visualize high-dimensional data for human interpretation
- Quantify alignment between data sources with metrics

---

## Learning Outcomes

1. Extract comments from srcML XML documents
2. Preprocess text (tokenization, stopwords, stemming) for NLP analysis
3. Apply k-means clustering to vocabulary data
4. Reduce dimensionality (PCA/t-SNE) for 2D visualization
5. Measure vocabulary alignment across data sources
6. Produce publication-quality visualizations for exploratory data analysis (EDA)

---

## What You Will Implement

You must implement these functions in `src/da2_vocabulary.py`:

**Required for all students:**
- `extract_comments_from_srcml()` - parse comment text from srcML
- `extract_vocabulary()` - tokenize and normalize text
- `cluster_vocabulary()` - perform k-means clustering (token-level EDA)
- `reduce_dimensions()` - apply PCA or t-SNE
- `measure_alignment()` - compute overlap metrics

**Provided (no implementation required):**
- `visualize_clusters()` - create k-means scatter plots (matplotlib boilerplate)
- `inspect_clusters()` - print top tokens per cluster to guide your alignment report

---

### Function Signatures & Docstrings

```python
from typing import Any, Dict, List, Tuple, Optional
import numpy as np

def extract_comments_from_srcml(xml_str: str) -> List[str]:
    """Extract all comment text from srcML XML.
    
    Why this matters for MSR:
    - Comments are natural language documentation written by developers
    - Comparing comment vocabulary to code vocabulary reveals documentation quality
    - Inline comments often explain intent that's not obvious from identifiers
    - srcML preserves comments as <comment> tags with type="line" or type="block"
    
    Parameters:
    - xml_str: srcML XML document as string
    
    Returns:
    - List of comment strings (one per <comment> element found)
    - Strip comment markers (// /* */ #) from each comment
    - Preserve multi-line comments as single strings
    
    Examples:
    >>> xml = '<unit><comment type="line">// TODO: refactor this</comment></unit>'
    >>> extract_comments_from_srcml(xml)
    ['TODO: refactor this']
    
    >>> xml = '<unit><comment type="block">/* Process user input */</comment></unit>'
    >>> extract_comments_from_srcml(xml)
    ['Process user input']
    
    Implementation hints:
    - Use same XML parsing approach from DA1 (DOM or SAX)
    - Find all <comment> elements regardless of type attribute
    - Strip leading/trailing whitespace
    - Remove comment syntax: //, /* */, #, etc.
    - Return empty list if no comments found
    """
    # TODO: Implement
    pass


def extract_vocabulary(
    text_list: List[str],
    min_length: int = 3,
    remove_stopwords: bool = True,
    stem: bool = True
) -> List[str]:
    """Tokenize and normalize text into vocabulary tokens.
    
    Why this matters for MSR:
    - Raw text contains noise (stopwords, punctuation, inconsistent casing)
    - Normalization enables meaningful comparison across sources
    - Stemming groups related words (e.g., "process", "processing", "processed")
    - Token filtering reduces dimensionality for clustering
    
    Parameters:
    - text_list: list of text strings to process
    - min_length: minimum token length to keep (default 3)
    - remove_stopwords: filter common English words (default True)
    - stem: apply Porter stemming (default True)
    
    Returns:
    - Flat list of normalized tokens (all lowercase)
    - Tokens appear in order they were encountered
    - Duplicates are preserved (frequency matters for later analysis)
    
    Behavior:
    - Tokenize on word boundaries (alphanumeric sequences)
    - Convert to lowercase
    - Remove tokens shorter than min_length
    - Remove stopwords if enabled (use NLTK or custom list)
    - Apply stemming if enabled (use NLTK PorterStemmer)
    - Filter out pure numbers
    
    Examples:
    >>> extract_vocabulary(["Fix the authentication bug"], stem=False)
    ['fix', 'authentication', 'bug']
    
    >>> extract_vocabulary(["Processing user data"], stem=True)
    ['process', 'user', 'data']
    
    Implementation hints:
    - Use re.findall(r'\b\w+\b', text.lower()) for tokenization
    - Common stopwords: the, a, an, is, are, was, were, this, that, etc.
    - nltk.stem.PorterStemmer for stemming
    - Keep it simple: basic preprocessing is sufficient
    """
    # TODO: Implement
    pass


def cluster_vocabulary(
    tokens: List[str],
    k: int = 5,
    vectorizer_params: Optional[Dict[str, Any]] = None
) -> Tuple[np.ndarray, np.ndarray, Any]:
    """Perform k-means clustering on vocabulary tokens.
    
    Why this matters for MSR:
    - Clustering reveals semantic groupings without supervision
    - k-means is simple, fast, and interpretable
    - Pre-trained word embeddings encode distributional semantics (words that appear
      in similar contexts have similar vectors)
    - Cluster assignments enable downstream alignment analysis

    Vectorization note:
    - We use **pre-trained word embeddings** from spaCy's `en_core_web_md` model
      (300-dimensional GloVe-style vectors)
    - Embeddings capture semantic relationships: 'fix' and 'bug' cluster together
      because they appear in similar contexts, even though they share no characters.
      This produces thematic clusters ('auth/login/session', 'fix/bug/error') that
      are directly useful as features for M1 prediction.
    - Out-of-vocabulary tokens receive a zero vector and will cluster together.

    Parameters:
    - tokens: flat list of vocabulary tokens
    - k: number of clusters (default 5)
    - vectorizer_params: accepted for API compatibility; not used in embedding mode

    Returns:
    - (labels, vectors, model) tuple where:
      - labels: np.ndarray of cluster assignments (shape: n_tokens,)
      - vectors: embedding matrix (shape: n_tokens × 300)
      - model: fitted KMeans object (or None for k=1)

    Behavior:
    - Look up each token in spaCy's en_core_web_md vocabulary to get a 300-dim vector
    - Stack vectors into a matrix
    - Fit KMeans with n_clusters=k, random_state=42 (for reproducibility)
    - Return cluster labels corresponding to each input token

    Examples:
    >>> tokens = ['user', 'authentication', 'login', 'password', 'database', 'query']
    >>> labels, vectors, model = cluster_vocabulary(tokens, k=2)
    >>> len(labels)
    6
    >>> labels.shape
    (6,)
    >>> vectors.shape[1]  # embedding dimension
    300

    Implementation hints:
    - import spacy; nlp = spacy.load('en_core_web_md')
    - vectors = np.array([nlp(token).vector for token in tokens])
    - from sklearn.cluster import KMeans
    - If tokens list is very short, reduce k to avoid empty clusters
    - Use random_state=42 for reproducible results
    """
    # TODO: Implement
    pass


def reduce_dimensions(
    vectors: np.ndarray,
    n_components: int = 2,
    method: str = "pca"
) -> np.ndarray:
    """Reduce high-dimensional vectors to 2D for visualization.
    
    Why this matters for MSR:
    - Human interpretation requires 2D or 3D projections
    - 300-dimensional embedding vectors cannot be read directly from a scatter plot
    - PCA preserves global structure (fast, deterministic)
    - t-SNE preserves local structure (slower, better visual separation of clusters)
    
    Parameters:
    - vectors: np.ndarray of shape (n_samples, n_features)
    - n_components: target dimensionality (default 2 for scatter plots)
    - method: either "pca" or "tsne" (default "pca")
    
    Returns:
    - np.ndarray of shape (n_samples, n_components)
    
    Behavior:
    - For "pca": use sklearn.decomposition.PCA
    - For "tsne": use sklearn.manifold.TSNE with random_state=42
    - Raise ValueError if method is not recognized
    
    Examples:
    >>> vectors = np.random.rand(100, 500)  # 100 samples, 500 features
    >>> reduced = reduce_dimensions(vectors, n_components=2, method="pca")
    >>> reduced.shape
    (100, 2)
    
    Implementation hints:
    - from sklearn.decomposition import PCA
    - from sklearn.manifold import TSNE
    - PCA is faster and stable (good default)
    - t-SNE takes longer but often reveals better clusters visually
    - Always set random_state=42 for TSNE reproducibility
    """
    # TODO: Implement
    pass


def visualize_clusters(
    coords_2d: np.ndarray,
    labels: np.ndarray,
    tokens: List[str],
    title: str = "Vocabulary Clusters",
    output_path: Optional[str] = None
) -> None:
    """Create scatter plot of clustered vocabulary in 2D space.

    **Provided - copy this into your da2_vocabulary.py as-is.**
    """
    if coords_2d.shape[0] == 0:
        plt.figure(figsize=(10, 8))
        plt.title(title)
        plt.xlabel("Dimension 1")
        plt.ylabel("Dimension 2")
        if output_path:
            plt.savefig(output_path, dpi=300, bbox_inches='tight')
            plt.close()
        return

    plt.figure(figsize=(10, 8))
    scatter = plt.scatter(
        coords_2d[:, 0],
        coords_2d[:, 1],
        c=labels,
        cmap='tab10',
        alpha=0.6,
        s=50,
        edgecolors='black',
        linewidth=0.5
    )

    n_clusters = len(np.unique(labels))
    if n_clusters <= 10:
        plt.colorbar(scatter, label='Cluster ID', ticks=range(n_clusters))
    else:
        plt.colorbar(scatter, label='Cluster ID')

    # Annotate the token closest to each cluster centroid
    for cluster_id in np.unique(labels):
        cluster_mask = labels == cluster_id
        cluster_indices = np.where(cluster_mask)[0]
        if len(cluster_indices) > 0:
            cluster_coords = coords_2d[cluster_mask]
            centroid = cluster_coords.mean(axis=0)
            distances = np.linalg.norm(cluster_coords - centroid, axis=1)
            rep_idx = cluster_indices[np.argmin(distances)]
            if rep_idx < len(tokens):
                plt.annotate(
                    tokens[rep_idx],
                    xy=(coords_2d[rep_idx, 0], coords_2d[rep_idx, 1]),
                    xytext=(5, 5),
                    textcoords='offset points',
                    fontsize=8,
                    alpha=0.7
                )

    plt.title(title, fontsize=14, fontweight='bold')
    plt.xlabel("Dimension 1", fontsize=12)
    plt.ylabel("Dimension 2", fontsize=12)
    plt.grid(True, alpha=0.3)
    plt.tight_layout()

    if output_path:
        plt.savefig(output_path, dpi=300, bbox_inches='tight')
        plt.close()
    else:
        plt.show()


def measure_alignment(
    tokens_a: List[str],
    tokens_b: List[str],
    labels_a: np.ndarray,
    labels_b: np.ndarray
) -> Dict[str, float]:
    """Compute alignment metrics between two clustered vocabularies.
    
    Why this matters for MSR:
    - Measures whether commit messages and code use the same terminology
    - Quantifies documentation drift (misalignment = poor docs)
    - Enables hypothesis testing (are comments closer to code than commits?)
    - Supports longitudinal studies (does alignment improve over time?)
    
    Parameters:
    - tokens_a: vocabulary from source A (e.g., commit messages)
    - tokens_b: vocabulary from source B (e.g., identifier names)
    - labels_a: cluster assignments for tokens_a
    - labels_b: cluster assignments for tokens_b
    
    Returns:
    - dict with keys:
      - vocab_overlap (float): Jaccard similarity of unique tokens
      - shared_vocab_size (int): number of tokens appearing in both
      - cluster_similarity (float): adjusted Rand index of cluster assignments for shared tokens
    
    Behavior:
    - vocab_overlap = |A ∩ B| / |A ∪ B| (Jaccard coefficient)
    - shared_vocab_size = |A ∩ B|
    - For cluster_similarity:
      - Find tokens appearing in both A and B
      - Compare their cluster assignments using adjusted Rand index
      - Returns 1.0 if perfectly aligned, 0.0 if random, negative if worse than random
    
    Examples:
    >>> tokens_a = ['user', 'login', 'auth', 'password']
    >>> tokens_b = ['user', 'login', 'database', 'query']
    >>> labels_a = np.array([0, 0, 0, 0])
    >>> labels_b = np.array([1, 1, 2, 2])
    >>> alignment = measure_alignment(tokens_a, tokens_b, labels_a, labels_b)
    >>> alignment['vocab_overlap']
    0.33...  # 2 shared / 6 total unique
    >>> alignment['shared_vocab_size']
    2
    
    Implementation hints:
    - Convert token lists to sets for overlap calculation
    - Jaccard = len(set_a & set_b) / len(set_a | set_b)
    - from sklearn.metrics import adjusted_rand_score
    - For cluster comparison, align tokens by building shared subset
    - Handle edge case: if no shared vocabulary, cluster_similarity = 0.0
    """
    # TODO: Implement
    pass


def build_vocabulary_dataset(
    repo_path: str = ".",
    commit_limit: Optional[int] = None,
    file_limit: Optional[int] = None
) -> Dict[str, Any]:
    """Build complete vocabulary dataset from repository.
    
    Why this matters for MSR:
    - Integrates multiple data sources (commits, identifiers, comments)
    - Provides end-to-end pipeline from raw repo to analysis-ready data
    - Enables reproducible cross-repo studies
    
    Parameters:
    - repo_path: path to git repository (default ".")
    - commit_limit: max commits to process (default None = all)
    - file_limit: max source files to analyze (default None = all)
    
    Returns:
    - dict with keys:
      - commit_tokens: vocabulary from commit messages
      - identifier_tokens: vocabulary from code identifiers
      - comment_tokens: vocabulary from code comments
      - commit_labels: cluster assignments for commits
      - identifier_labels: cluster assignments for identifiers
      - comment_labels: cluster assignments for comments
      - alignment: dict of alignment metrics (commits vs identifiers, etc.)
    
    Behavior:
    - Query commits table for messages (use DI1 text normalization)
    - Extract identifiers from source files (reuse DA1 functions)
    - Extract comments from source files (use extract_comments_from_srcml)
    - Cluster each vocabulary separately
    - Measure pairwise alignment (commits-identifiers, commits-comments, identifiers-comments)
    
    Implementation hints:
    - Use db_utils to query commits table
    - Use srcml_runner and DA1 functions for code analysis
    - Filter to .py, .java, .cpp, .c, .js files only
    - Call extract_vocabulary on each text source
    - Call cluster_vocabulary(tokens, k=5) for each
    - Compute all pairwise alignments
    """
    # TODO: Implement
    pass

```

---

## Support Code: srcML Comment Extraction

Comments appear in srcML as `<comment>` elements with attributes `type="line"` or `type="block"`:

```xml
<comment type="line">// Initialize the connection pool</comment>
<comment type="block">/* 
 * Process incoming requests
 * and route to handlers
 */</comment>
```

Your `extract_comments_from_srcml` function should:
1. Find all `<comment>` elements
2. Extract text content
3. Strip comment syntax (`//`, `/*`, `*/`, `#`, etc.)
4. Return list of cleaned comment strings

---

## Provided Tests

Tests are provided in `test/test_da2_vocabulary.py` covering:

**TestCommentExtraction (10 tests):**
- Extract line comments (`// ...`)
- Extract block comments (`/* ... */`)
- Handle Python comments (`# ...`)
- Strip comment markers
- Handle empty/missing comments

**TestVocabularyExtraction (12 tests):**
- Tokenization correctness
- Stopword removal
- Stemming behavior
- Minimum length filtering
- Number filtering
- Case normalization

**TestClustering (8 tests):**
- k-means produces correct number of clusters
- Cluster labels match input size
- Embedding vectors have correct shape (n_tokens × 300)
- Reproducibility (random_state)

**TestDimensionalityReduction (6 tests):**
- PCA reduces to 2D
- t-SNE reduces to 2D
- Invalid method raises ValueError
- Output shape matches input sample count

**TestVisualization (4 tests):**
- Creates plot without errors
- Saves to file when output_path provided
- Handles single cluster edge case

**TestAlignment (8 tests):**
- Jaccard similarity computed correctly
- Shared vocabulary size correct
- Adjusted Rand index for cluster similarity
- Handles disjoint vocabularies (no overlap)

**Total: 48 tests**

Run with:

```bash
pytest test/test_da2_vocabulary.py -v
```

---

## Required Dependencies

Add these to your `requirements.txt`:

```
scikit-learn>=1.0.0
nltk>=3.6.0
matplotlib>=3.5.0
numpy>=1.21.0
pandas>=1.3.0
spacy>=3.0.0
```

After installing spaCy, download the medium English model (~33 MB, 300-dim vectors):

```bash
python -m spacy download en_core_web_md
```

For NLTK stopwords and stemming, you'll need to download data:

```python
import nltk
nltk.download('stopwords')
nltk.download('punkt')
```

(Your tests can handle this automatically or you can do it once in setup.)

---

## Example Workflow

> **Recommended:** use the pipeline commands below to run the full workflow.  
> The manual snippet underneath shows how individual functions connect for debugging.

### Via the pipeline (recommended)

```bash
# Stage 1 - mine data into the DB (do this once per repo)
python main.py mine pallets/flask --token $TOKEN --ingest --depth 200 --file-limit 100

# Stage 2 - analyze and produce all outputs
python main.py analyze --output-dir output/ --clusters 5
```

### Manual (useful for debugging individual functions)

```python
from src import da2_vocabulary, db_utils

# 1. Extract vocabularies directly from the DB
commit_rows = db_utils.exec_get_all("SELECT message FROM commits LIMIT 100")
commit_tokens = da2_vocabulary.extract_vocabulary([r[0] for r in commit_rows if r[0]])

comment_rows = db_utils.exec_get_all("SELECT comment_text FROM code_comments")
comment_tokens = da2_vocabulary.extract_vocabulary([r[0] for r in comment_rows if r[0]])

# 2a. k-means clustering (token-level EDA + scatter plot)
commit_labels, commit_vecs, _ = da2_vocabulary.cluster_vocabulary(commit_tokens, k=5)
comment_labels, comment_vecs, _ = da2_vocabulary.cluster_vocabulary(comment_tokens, k=5)

# 2b. k-means scatter plot (PCA/t-SNE)
commit_2d = da2_vocabulary.reduce_dimensions(commit_vecs, method='tsne')
da2_vocabulary.visualize_clusters(
    commit_2d,
    commit_labels,
    commit_tokens,
    title="Commit Message Clusters",
    output_path="output/commits_clusters.png"
)

# 3. Measure alignment
alignment = da2_vocabulary.measure_alignment(
    commit_tokens,
    comment_tokens,
    commit_labels,
    comment_labels
)
print(f"Vocabulary overlap: {alignment['vocab_overlap']:.2%}")
print(f"Cluster similarity: {alignment['cluster_similarity']:.3f}")

# 4. Inspect clusters - record top tokens in your alignment report
da2_vocabulary.inspect_clusters(commit_tokens, commit_labels, top_n=10)
```

---
## New DB support Updates
### New Schema
You should add the following tables to your current Schema

```sql

CREATE TABLE IF NOT EXISTS code_identifiers (
    id          SERIAL PRIMARY KEY,
    file_path   TEXT NOT NULL,
    name        TEXT NOT NULL,          -- raw identifier (e.g. "getUserData")
    kind        TEXT NOT NULL           -- function | class | variable | parameter
);

CREATE TABLE IF NOT EXISTS code_comments (
    id           SERIAL PRIMARY KEY,
    file_path    TEXT NOT NULL,
    comment_text TEXT NOT NULL          -- cleaned comment text (markers stripped)
);
```
### New db_utils function
```py
def exec_many(sql, args_list):
    """Execute a parameterised statement once per row using executemany().

    Much faster than calling exec_commit() in a loop because it reuses a
    single connection and cursor for the entire batch.

    Parameters:
    - sql: parameterised SQL with %(name)s placeholders
    - args_list: iterable of dicts, one per row
    """
    items = list(args_list)
    if not items:
        return
    conn = connect()
    cur = conn.cursor()
    cur.executemany(sql, items)
    conn.commit()
    conn.close()
```
---
## New srcml_runner code

Add this helper method to your srcml_runner

```py
def run_srcml_on_directory(dir_path: str, srcml_path: Optional[str] = None) -> str:
    """Run srcML on an entire directory and return the combined XML as a string.

    srcML processes all recognised source files (.py, .java, .cpp,
    etc.) and returns a multi-unit document where the outer <unit> element
    contains one child <unit filename="..."> per source file.  File types that
    srcML does not recognise are silently skipped.

    Raises RuntimeError if the srcML binary is not found.
    """
    srcml = srcml_path or find_srcml_executable()
    fd_out, out_path = tempfile.mkstemp(suffix=".srcml")
    os.close(fd_out)
    try:
        subprocess.run([srcml, dir_path, "-o", out_path], check=True)
        with open(out_path, "r", encoding="utf-8") as f:
            return f.read()
    finally:
        try:
            os.remove(out_path)
        except Exception:
            pass
```

---

## The mine / analyze Pipeline

DA2 requires two stages: first **mine** data from a real repo into the DB, then **analyze** it.  Your `main.py` has been evolving since DC0 and may look quite different from anyone else's - that's fine.  The two changes below apply regardless of whether you use subcommands, a flat `main()`, or a separate script.

### Stage 1 - Extend your mine flow to collect code artifacts

Your existing mine flow already clones a repo into a temp directory, calls `git_miner.mine_history`, and then cleans up with `shutil.rmtree`.  You need to call a code-artifact function **between** the clone and the cleanup - while the repo is still on disk:

```python
print("Cloning ...")
Repo.clone_from(clone_url, tmp_repo_dir)     # your existing clone (whatever form it takes)

try:
    git_miner.mine_history(tmp_repo_dir)     # ← keep in its own try/except
except Exception as e:                       #   shallow clones can raise here; don't let it
    print(f"Warning: {e}")                   #   block the step below

_mine_code_artifacts(tmp_repo_dir)           # ← ADD THIS before rmtree
# ...
shutil.rmtree(tmp_repo_dir)                  # now safe to clean up
```

> **Why its own `try/except`?**  If you use a shallow clone (`--depth N`), `mine_history` may raise a `fatal: bad object` error when diffing commits - the parent objects don't exist.  Without the try/except, the exception would skip `_mine_code_artifacts` entirely and leave `code_identifiers` / `code_comments` empty.

> **Multi-repo studies:** If you want to compare or pool data across several repositories, wrap the block above in a loop over a list of `(clone_url, repo_name)` pairs.  All three operations - `clone_from`, `mine_history`, and `_mine_code_artifacts` - need to run per repo because each one needs the repo on disk.  The `analyze` step does **not** need to change: it reads everything from the DB, so it automatically sees the combined data from every repo you've mined.

> **Clone performance tip:** `Repo.clone_from()` (GitPython) can be slow on large repositories because it uses a pure-Python transport layer.  If cloning is a bottleneck, replace it with a direct `git clone` subprocess call - the rest of the pipeline is identical:
>
> ```python
> import subprocess
> subprocess.run(
>     ["git", "clone", "--depth", str(depth), clone_url, tmp_repo_dir],
>     check=True,
> )
> ```
>
> `check=True` raises `subprocess.CalledProcessError` on failure, giving you the same error-handling behaviour as `Repo.clone_from`.  For very large repos, adding `--filter=blob:none` (a partial/blobless clone) reduces download size further while still giving `mine_history` access to all commit metadata.

`_mine_code_artifacts` is a helper function you should add to `main.py` if you do not already have something like it. It looks like what you see below. The main point of it is to run your srcml on a directory, then run your srcml xml analysis code (most of which you have implemented by now). You do not need to add this to your code if you already have something like it, but you should read and understand what it's doing; integrate useful parts of it into your code. It assumes many things, so it might not integrate cleanly if you just copy/paste

```py
def _mine_code_artifacts(repo_path: str, file_limit: int = None) -> None:
    """Run srcML on the entire cloned repo directory, then store identifiers
    and comments to the DB.

    Must be called *before* the temp directory is cleaned up.  Clears existing
    rows first so re-running mine is idempotent.

    Silently exits if srcML is not installed rather than failing the whole
    mine stage.
    """
    from src import db_utils, srcml_runner
    from src import da1_identifiers
    from src import da2_vocabulary

    # Ensure new tables exist (idempotent)
    db_utils.exec_sql_file('data/schema.sql')

    # Clear previous run's data -- REMOVE IF YOU DO NOT WANT IT
    db_utils.exec_commit("TRUNCATE code_identifiers, code_comments;")

    # Single srcML call on the whole directory
    print("  Running srcML on repository directory...")
    try:
        dir_xml = srcml_runner.run_srcml_on_directory(repo_path)
    except RuntimeError as exc:
        print(f"  Warning: srcML unavailable - skipping code artifact mining ({exc})",
              file=sys.stderr)
        return
    except Exception as exc:
        print(f"  Warning: srcML failed - {exc}", file=sys.stderr)
        return

    # Parse the multi-unit document; each child <unit> is one source file
    try:
        root = ET.fromstring(dir_xml.encode("utf-8"))
    except ET.ParseError as exc:
        print(f"  Warning: could not parse srcML output - {exc}", file=sys.stderr)
        return

    units = [c for c in root if c.tag.split('}')[-1] == 'unit']
    if file_limit:
        units = units[:file_limit]

    print(f"  Processing {len(units)} source files...")

    id_rows = []
    cm_rows = []

    for unit in units:
        rel_path = unit.get('filename', '')
        unit_xml = ET.tostring(unit, encoding='unicode')

        # DA1 - identifiers
        try:
            for row in da1_identifiers.extract_identifiers_dom(unit_xml):
                id_rows.append({"fp": rel_path, "name": row["name"], "kind": row["kind"]})
        except Exception:
            pass

        # DA2 - comments
        try:
            for text in da2_vocabulary.extract_comments_from_srcml(unit_xml):
                if text.strip():
                    cm_rows.append({"fp": rel_path, "ct": text})
        except Exception:
            pass

    # Batch insert
    db_utils.exec_many(
        "INSERT INTO code_identifiers (file_path, name, kind) VALUES (%(fp)s, %(name)s, %(kind)s);",
        id_rows,
    )
    db_utils.exec_many(
        "INSERT INTO code_comments (file_path, comment_text) VALUES (%(fp)s, %(ct)s);",
        cm_rows,
    )

    print(f"    -> {len(id_rows)} identifiers, {len(cm_rows)} comments from {len(units)} files")
```

### Stage 2 - Add an analyze entry point

DA2 analysis reads **only from the DB** - no network, no cloned repo.  The `cmd_analyze` function below is a reference implementation.  It likely works without many changes once you have implemented the required DA2 functions; you may need to adjust argument names to match your `argparse` setup. **Importantly** it helps you understand how everything fits together, so make sure you understand it, and integrate it into your code appropriately. It might need adjusting-- feel free to adjust it if required.

```python
def cmd_analyze(args) -> None:
    """Run DA2 vocabulary analysis from DB. No network calls.

    Produces:
      <output_dir>/commit_clusters.png      k-means scatter (commit vocab)
      <output_dir>/identifier_clusters.png  k-means scatter (identifier vocab)
      <output_dir>/comment_clusters.png     k-means scatter (comment vocab)
      <output_dir>/alignment_report.txt     cluster inspection + alignment metrics
    """
    from src import da2_vocabulary
    import os

    out = args.output_dir
    os.makedirs(out, exist_ok=True)
    k = args.clusters

    # 1. Build vocabulary dataset from DB
    print("Building vocabulary dataset from DB...")
    dataset = da2_vocabulary.build_vocabulary_dataset(
        commit_limit=args.commit_limit,
        file_limit=args.file_limit,
    )

    commit_tokens     = dataset["commit_tokens"]
    identifier_tokens = dataset["identifier_tokens"]
    comment_tokens    = dataset["comment_tokens"]

    print(f"  commit tokens:     {len(commit_tokens)}")
    print(f"  identifier tokens: {len(identifier_tokens)}")
    print(f"  comment tokens:    {len(comment_tokens)}")

    # 2. k-means clustering + scatter plots
    sources = [
        ("commit",     commit_tokens,     "Commit Message Vocabulary"),
        ("identifier", identifier_tokens, "Code Identifier Vocabulary"),
        ("comment",    comment_tokens,    "Code Comment Vocabulary"),
    ]

    kmeans_labels = {}
    cluster_inspection = {}  # name -> {cluster_id: [top tokens]}
    for name, tokens, title in sources:
        if not tokens:
            print(f"  [{name}] no tokens – skipping k-means")
            kmeans_labels[name] = None
            cluster_inspection[name] = {}
            continue
        print(f"  Clustering {name} tokens (k={k})...")
        labels, vectors, _ = da2_vocabulary.cluster_vocabulary(tokens, k=k)
        kmeans_labels[name] = labels
        cluster_inspection[name] = da2_vocabulary.inspect_clusters(tokens, labels, top_n=10)
        coords = da2_vocabulary.reduce_dimensions(vectors, method="pca")
        da2_vocabulary.visualize_clusters(
            coords, labels, tokens,
            title=f"{title} – k-means (k={k})",
            output_path=os.path.join(out, f"{name}_clusters.png"),
        )
        print(f"    → {out}/{name}_clusters.png")

    # 3. Alignment metrics + report
    alignment = dataset.get("alignment", {})

    report_lines = ["VOCABULARY ALIGNMENT REPORT", "=" * 40, ""]

    source_titles = {
        "commit":     "Commit Message Vocabulary",
        "identifier": "Code Identifier Vocabulary",
        "comment":    "Code Comment Vocabulary",
    }
    for name, title in source_titles.items():
        clusters = cluster_inspection.get(name, {})
        if not clusters:
            continue
        report_lines += [f"{title} Clusters", "-" * 36]
        for cluster_id, top_tokens in sorted(clusters.items()):
            report_lines.append(f"  Cluster {cluster_id}: {', '.join(top_tokens)}")
        report_lines.append("")

    report_lines += ["Alignment Metrics", "=" * 40, ""]

    pair_names = {
        "commits_identifiers":  ("commit",     "identifier"),
        "commits_comments":     ("commit",     "comment"),
        "identifiers_comments": ("identifier", "comment"),
    }
    for key, (a, b) in pair_names.items():
        m = alignment.get(key)
        if not m:
            report_lines.append(f"{a} ↔ {b}: no data")
            continue
        report_lines += [
            f"{a} ↔ {b}",
            f"  Vocabulary overlap (Jaccard): {m['vocab_overlap']:.1%}",
            f"  Shared vocabulary size:       {m['shared_vocab_size']}",
            f"  Cluster similarity (ARI):     {m['cluster_similarity']:.3f}",
            "",
        ]

    report_path = os.path.join(out, "alignment_report.txt")
    with open(report_path, "w", encoding="utf-8") as f:
        f.write("\n".join(report_lines))
    print(f"  → {report_path}")

    for line in report_lines:
        print(line)
```

You may find it useful to:
- Add `--clusters` / `-k` and `--output-dir` arguments so you can experiment from the command line without editing code
- Swap `method="pca"` to `"tsne"` once everything works, for better-separated final plots
---

## Deliverables

1. **Implementation** of all required functions in `src/da2_vocabulary.py`
2. **All 48 tests passing** in `test/test_da2_vocabulary.py`
3. **Three visualization outputs** saved to `output/`:
   - `commit_clusters.png` - k-means scatter plot of commit vocabulary
   - `identifier_clusters.png` - k-means scatter plot of identifier vocabulary
   - `comment_clusters.png` - k-means scatter plot of comment vocabulary
4. **Alignment report** (`output/alignment_report.txt`) containing:
   - Top-10 tokens per cluster for each vocabulary source (commit, identifier, comment)
   - Vocabulary overlap percentages for all three source pairs
   - Cluster similarity - Adjusted Rand Index (ARI) scores
   - Brief interpretation (2-3 sentences): Do commit messages match code vocabulary?
   - Your human-readable theme labels for each cluster (these carry forward to M1)
5. **Cluster theme notes** - record your cluster → theme label mapping (e.g., in the alignment report or a separate notes file). This is the starting point for our next assignment, which will invovle feature engineering; you will build `token_to_cluster` from these results when you start the *next* assignment.

---

## Research Questions to Explore

Your alignment report should address:

1. **Do commit messages use the same vocabulary as identifier names?**
   - High overlap + high cluster similarity → good alignment
   - Low overlap → developers describe changes differently than they name code

2. **Are comments closer to code or commits in vocabulary?**
   - Compare: (commits ↔ comments) vs (identifiers ↔ comments)
   - Comments closer to code → implementation-focused documentation
   - Comments closer to commits → user-facing documentation

3. **What are the main vocabulary clusters?**
   - Examine top terms per cluster (e.g., "authentication cluster", "database cluster")
   - Do clusters correspond to architectural modules?

---

## Grading Outline

| Component | Weight |
|-----------|--------|
| Comment extraction from srcML | 10% |
| Vocabulary normalization | 10% |
| k-means clustering | 10% |
| Dimensionality reduction | 15% |
| Alignment metrics | 15% |
| `build_vocabulary_dataset` integration | 15% |
| Deliverable outputs + alignment report | 15% |

---

## Suggested File Workflow

1. **Phase 1:** Comment extraction
   - Implement `extract_comments_from_srcml`
   - Run TestCommentExtraction tests

2. **Phase 2:** Vocabulary preprocessing
   - Implement `extract_vocabulary`
   - Run TestVocabularyExtraction tests

3. **Phase 3:** k-means clustering
   - Implement `cluster_vocabulary`
   - Run TestClustering tests

4. **Phase 4:** Dimensionality reduction
   - Implement `reduce_dimensions`
   - Run TestDimensionalityReduction tests
   - `visualize_clusters` is **provided** - copy it into your file and run TestVisualization to confirm

5. **Phase 5:** Alignment
   - Implement `measure_alignment`
   - Run TestAlignment tests

6. **Phase 6:** Inspect clusters and write alignment report
   - Run `python main.py analyze` (or call functions manually) to produce cluster outputs
   - Call `inspect_clusters()` on each vocabulary and read the printed top tokens
   - Assign a short theme label to each cluster (e.g., "bug-fix", "auth", "database")
   - Record the theme labels in `output/alignment_report.txt`
   - Confirm the alignment metrics (Jaccard, ARI) look reasonable for your repo

7. **Phase 7:** Integration
   - Implement `build_vocabulary_dataset` to wire everything together
   - Confirm all three PNG scatter plots are written to `output/`
   - Verify `output/alignment_report.txt` includes cluster tokens and alignment metrics
   - Keep your cluster theme notes - they are the starting point for M1 feature engineering

---

## Suggested Test Repositories

Once your implementation is complete, use one (or more) of these to generate real data.  Each is small enough to mine in a few minutes with a shallow clone, but has enough commit history and source code to produce meaningful vocabulary clusters.

| Repo | Language | Domain | Why it's interesting |
|------|----------|--------|----------------------|
| `junit-team/junit4` | Java | Testing framework | Strong vocabulary split: test/assert/expect cluster vs. runner/lifecycle cluster |
| `nlohmann/json` | C++ | JSON library | Tight token domain (parse, serialize, token, value) - good for watching clusters converge |
| `libuv/libuv` | C | Async I/O (Node.js backend) | Systems vocabulary (handle, loop, stream, callback) clearly distinct from commit-message language |

Mine with a shallow clone to keep it fast. Replace `--ingest` `--depth` `--file-limit` with your own cli arguments, assuming you have them. If you don't, you might not need them-- these are small repositories. The text in this readme shows you how they are implemented, so search for them if you're confused!

```bash
# Java - JUnit 4
python main.py mine junit-team/junit4 --ingest --depth 300 --file-limit 150

# C++ - nlohmann/json
python main.py mine nlohmann/json --ingest --depth 300 --file-limit 100

# C - libuv
python main.py mine libuv/libuv --ingest --depth 300 --file-limit 150
```

Then run analysis as normal:

```bash
python main.py analyze --output-dir output/ --clusters 5
```

> **Tip:** Mine one repo first and run `analyze` immediately to check your pipeline end-to-end before adding more repos.  If you mine all three, `analyze` will pool the data from all three automatically - you don't need to change anything.

---

## Hints

- **Start with comments:** Implement `extract_comments_from_srcml` first (simpler than identifiers)
- **Test incrementally:** Don't wait to run all 48 tests at once
- **Use small k for testing:** k=2 or k=3 clusters are easier to debug visually
- **Check your preprocessing:** Print sample tokens before clustering to verify normalization
- **t-SNE is slow:** Use PCA for rapid iteration, t-SNE for final visualizations
- **Vocabulary size matters:** If you have <20 unique tokens, reduce k to avoid empty clusters
- **Stopword lists vary:** Don't over-filter; domain terms like "user" and "data" should stay

---