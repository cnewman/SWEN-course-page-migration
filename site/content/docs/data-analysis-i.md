# STRATA — DA1: Data Analysis I

**Mode:** Test-driven assignment — **tests are provided** to define expected behavior.

**Builds on:** DC0/DC1/DC2 + DI1 + DI2. This assignment adds static analysis from source code structure using srcML.

---

## Goal

Implement a small static-analysis extractor that mines **identifiers** from srcML XML and produces a compact dataset for analysis.

---

## Learning Outcomes

1. Parse srcML with either **DOM** or **SAX** (your choice).
2. Extract identifier-level features from XML and aggregate file-level statistics.
3. Compute vocabulary and naming-quality style metrics (length, diversity, convention consistency).

---

## Get srcML

You can install srcml [from here](https://www.srcml.org/)

## What You Will Implement

You must implement these functions in `src/da1_identifiers.py`:

**Required for all:**
- `xml_from_file()` — read XML from disk
- `aggregate_identifier_features()` — compute file-level metrics
- `build_file_identifier_dataset()` — build dataset from multiple files

**Choose ONE parsing approach:**
- `extract_identifiers_dom()` — DOM with ElementTree (easiest, no dependencies)
- `extract_identifiers_sax()` — SAX event-driven (memory-efficient)
- `extract_identifiers_dom_xpath()` OR `extract_identifiers_dom()` with lxml — XPath queries (most concise)

You may implement more than one if you would like, but only one is required. You are also allowed to add as many helper functions as you'd like.

---

### Function Signatures & Docstrings

```python
from typing import Any, Dict, List

def xml_from_file(path: str) -> str:
    """Read srcML XML content from disk.
    
    Parameters:
    - path: absolute or relative path to XML file
    
    Returns:
    - XML content as string
    
    Example:
    >>> xml = xml_from_file('output/file.py.srcml')
    >>> '<unit' in xml
    True
    
    Implementation hints:
    - Use UTF-8 encoding
    - Simple file read operation
    """
    # TODO: Implement
    pass


def extract_identifiers_dom(xml_str: str) -> List[Dict[str, Any]]:
    """Extract identifier rows using a DOM-style approach (ElementTree/XPath-style finds).
  
    Parameters:
    - xml_str: srcML XML document as string
    
    Returns:
    - List of identifier dicts, each with keys:
      - name (str): identifier text
      - kind (str): one of 'function', 'parameter', 'variable', 'class'
      - convention (str): naming convention detected
      - length (int): character count
      - n_tokens (int): token count after splitting
      - scope (str): one of 'global', 'local', 'parameter'
    
    Behavior:
    - Parse the entire XML tree with ElementTree
    - Use `.iter()` or `.findall()` to locate function/class/parameter/decl nodes
    - For each <name> node, classify its context (function name vs parameter name vs variable)
    - Return one row per identifier found
    
    Examples:
    >>> xml = '<unit><function><name>process</name></function></unit>'
    >>> ids = extract_identifiers_dom(xml)
    >>> ids[0]['name']
    'process'
    >>> ids[0]['kind']
    'function'
    
    Implementation hints:
    - Use `ET.fromstring(xml_str)` to parse
    - Namespace-aware: strip namespace prefixes with helper (e.g., tag.rsplit('}', 1)[1])
    - Iterate over functions first, then parameters within, then local variables
    - Check parent/ancestor tags to determine context (function vs class vs global)
    """
    # TODO: Implement
    pass


def extract_identifiers_sax(xml_str: str) -> List[Dict[str, Any]]:
    """Extract identifier rows using a SAX event parser.
     
    Parameters:
    - xml_str: srcML XML document as string
    
    Returns:
    - List of identifier dicts with same schema as extract_identifiers_dom
    
    Behavior:
    - Use xml.sax.parse with a custom ContentHandler
    - Maintain a stack of open tags to track context (are we inside <function>? <parameter>?)
    - On endElement for <name>, inspect stack to classify identifier kind/scope
    - Accumulate characters() events into a buffer for each element's text
    
    Examples:
    >>> xml = '<unit><function><name>getData</name></function></unit>'
    >>> ids = extract_identifiers_sax(xml)
    >>> ids[0]['convention']
    'camelCase'
    
    Implementation hints:
    - Subclass xml.sax.handler.ContentHandler
    - Use self.stack = [] to track tag ancestry
    - On startElement, push tag; on endElement, pop
    - Set parser.setFeature(feature_namespaces, True) for namespace support
    - Check stack[-2] or 'function' in stack to determine identifier context
    """
    # TODO: Implement
    pass


# ============================================================================
# NOTE: You only need to implement ONE of the above extraction functions!
# Choose DOM, SAX, or XPath based on your preference and learning goals.
# ============================================================================


def aggregate_identifier_features(identifiers: List[Dict[str, Any]]) -> Dict[str, Any]:
    """Compute file-level aggregate metrics from identifier rows.
    
    Parameters:
    - identifiers: list of identifier dicts from extract_identifiers_dom/sax
    
    Returns:
    - dict with keys:
      - n_identifiers (int): total count
      - avg_identifier_length (float): mean character length
      - avg_tokens_per_identifier (float): mean tokens per name
      - vocab_size (int): unique normalized tokens
      - vocab_diversity (float): unique tokens / total tokens (0.0 to 1.0)
      - pct_snake_case (float): fraction using snake_case
      - pct_camel_case (float): fraction using camelCase
      - pct_pascal_case (float): fraction using PascalCase
    
    Behavior:
    - Return all metrics as 0/0.0 if identifiers list is empty
    - Compute means with simple arithmetic (sum / count)
    - Vocabulary = set of all unique tokens (after lowercasing and splitting)
    - Diversity = len(vocab) / total_token_count (avoid division by zero)
    
    Examples:
    >>> ids = [{'name': 'getUser', 'convention': 'camelCase', 'length': 7, 'tokens': ['get', 'user']}]
    >>> agg = aggregate_identifier_features(ids)
    >>> agg['n_identifiers']
    1
    >>> agg['pct_camel_case']
    1.0
    
    Implementation hints:
    - Use sum() and len() for averages
    - Build vocab with set comprehension: {token for row in identifiers for token in row['tokens']}
    - Count conventions with list comprehension and sum(1 for ...)
    """
    # TODO: Implement
    pass


def build_file_identifier_dataset(xml_by_file: Dict[str, str], parser: str = "dom") -> List[Dict[str, Any]]:
    """Build file-level dataset rows from {file_path: xml_str}.

    Parameters:
    - xml_by_file: dict mapping file paths to srcML XML strings
    - parser: either 'dom' or 'sax' (default 'dom')
    
    Returns:
    - List of dicts, one per file, with keys:
      - file_path (str)
      - n_identifiers (int)
      - avg_identifier_length (float)
      - ... (all metrics from aggregate_identifier_features)
    
    Behavior:
    - Raise ValueError if parser is not 'dom' or 'sax'
    - Process files in sorted order (for reproducibility)
    - For each file: extract identifiers → aggregate → append to output
    
    Examples:
    >>> xml_map = {'a.py': '<unit>...</unit>', 'b.py': '<unit>...</unit>'}
    >>> dataset = build_file_identifier_dataset(xml_map, parser='dom')
    >>> len(dataset)
    2
    >>> dataset[0]['file_path']
    'a.py'
    
    Implementation hints:
    - Normalize parser string: parser.lower().strip()
    - Use sorted(xml_by_file.keys()) for deterministic iteration
    - Call extract_identifiers_dom or extract_identifiers_sax based on parser choice
    - Merge file_path with aggregate dict: {'file_path': path, **agg}
    """
    # TODO: Implement
    pass
```

---

## Identifier-level data to collect

Each extracted identifier row must include at least:
- `name` (str)
- `kind` (str): one of `function`, `parameter`, `variable`, `class`
- `convention` (str): one of `snake_case`, `camelCase`, `PascalCase`, `SCREAMING_SNAKE`, `other`
- `length` (int): number of characters
- `n_tokens` (int): count after tokenization by underscore/case boundaries
- `scope` (str): one of `global`, `local`, `parameter`

---

## File-level data to calculate

From each file's identifier list, compute:
- `n_identifiers`
- `avg_identifier_length`
- `avg_tokens_per_identifier`
- `vocab_size` (unique normalized tokens)
- `vocab_diversity` (type-token ratio = unique tokens / total tokens)
- `pct_snake_case`
- `pct_camel_case`
- `pct_pascal_case`

You may add extra columns, but these are the minimum required.

---

## DOM and SAX Starter Snippets

You can choose either parsing strategy for your implementation. Both are acceptable.

### Option A — DOM (ElementTree + XPath-style search)

```python
import xml.etree.ElementTree as ET

root = ET.fromstring(xml_str)

# Example XPath-style searches (ElementTree subset)
for fn in root.findall('.//function'):
    name_node = fn.find('./name')
    if name_node is not None and (name_node.text or '').strip():
        fn_name = name_node.text.strip()

for param_name in root.findall('.//parameter//name'):
    token = (param_name.text or '').strip()
```

**Note:** ElementTree supports only a **limited subset** of XPath (basic paths, `//`, wildcards). For full XPath 1.0 with axes (`ancestor::`, `not()`, etc.), see Option C below.

### Option B — SAX (streaming parser)

```python
import io
import xml.sax
from xml.sax.handler import ContentHandler


class IdentifierHandler(ContentHandler):
    def __init__(self):
        super().__init__()
        self.stack = []
        self.buffer = []
        self.rows = []

    def startElement(self, name, attrs):
        self.stack.append(name)
        self.buffer = []

    def characters(self, content):
        self.buffer.append(content)

    def endElement(self, name):
        text = ''.join(self.buffer).strip()
        # Decide whether this <name> corresponds to function/parameter/decl/class
        # based on self.stack context, then append to self.rows
        self.stack.pop()
        self.buffer = []


handler = IdentifierHandler()
xml.sax.parse(io.StringIO(xml_str), handler)
rows = handler.rows
```

### Option C — XPath with lxml (Advanced/Optional)

If you want to use **full XPath 1.0** with advanced features like axes, predicates, and complex queries, you can use `lxml`:

```python
from lxml import etree

root = etree.fromstring(xml_str.encode('utf-8'))

# Full XPath 1.0 support with axes and predicates
# Function names: first <name> child of any <function>
for name_node in root.xpath('//function/name[1]'):
    text = (name_node.text or '').strip()

# Local variables: <decl_stmt> WITH <function> ancestor
for name_node in root.xpath('//decl_stmt[ancestor::function]//decl/name[1]'):
    text = (name_node.text or '').strip()

# Global variables: <decl_stmt> WITHOUT <function> ancestor
for name_node in root.xpath('//decl_stmt[not(ancestor::function)]//decl/name[1]'):
    text = (name_node.text or '').strip()
```

**XPath Features Available:**
- **Axes:** `ancestor::`, `parent::`, `child::`, `descendant::`, `following::`, `preceding::`
- **Predicates:** `[position()=1]`, `[not(...)]`, `[@attribute='value']`
- **Functions:** `count()`, `contains()`, `starts-with()`, `string-length()`
- **Boolean logic:** `and`, `or`, `not()`

**Installation:**
```bash
pip install lxml
```

---

## Support Code: Running srcML

A [helper module](/code/srcml_runner.py) is provided so you can:
- locate `srcml` on your machine,
- extract file content at a commit,
- run srcML and retrieve XML as text.

Example usage:

```python
from src import srcml_runner
from src import DA1_identifiers

xml = srcml_runner.run_srcml_on_repo_file('.', 'src/qual_clean.py', commit='HEAD')
ids = DA1_identifiers.extract_identifiers_dom(xml)
summary = DA1_identifiers.aggregate_identifier_features(ids)
print(summary)
```

---

## Provided Tests

Tests are provided [here](/code/test_da1_identifiers.py)

**IMPORTANT:** The tests are organized into separate classes for each parsing approach:
- **`TestCommon`** — parser-agnostic tests (aggregate functions, etc.) — KEEP THIS
- **`TestDOM`** — tests for DOM (ElementTree) implementation
- **`TestSAX`** — tests for SAX (event-driven) implementation  
- **`TestXPath`** — tests for XPath (lxml) implementation

**What you need to do:**
1. Choose ONE parsing approach to implement (DOM, SAX, or XPath)
2. Delete the test classes for the approaches you did NOT implement
   - Example: If you implemented SAX, delete `TestDOM` and `TestXPath` classes
   - Keep `TestCommon` — it works with any parser choice
3. Verify your tests pass: `pytest test/test_da1_identifiers.py -v`

**Example:** If you implemented DOM only, your test file should contain:
- `TestCommon` (keep)
- `TestDOM` (keep)
- ~~`TestSAX`~~ (delete entire class)
- ~~`TestXPath`~~ (delete entire class)

This way, you only run tests for the code you actually wrote.

The tests validate:
- identifier extraction correctness
- naming convention detection  
- file-level aggregate metrics
- dataset generation over multiple files

Run with:

```bash
pytest test/test_DA1_identifiers.py -v
```

---

## Deliverables

1. **Implementation** of all required functions in `src/da1_identifiers.py`
   - You only need to implement **ONE** of: `extract_identifiers_dom()`, `extract_identifiers_sax()`, or `extract_identifiers_dom_xpath()`
   - You must implement: `xml_from_file()`, `aggregate_identifier_features()`, and `build_file_identifier_dataset()`
2. **All tests passing** in `test/test_da1_identifiers.py`
   - Delete the test classes (TestDOM, TestSAX, or TestXPath) for parsers you did NOT implement
   - Keep TestCommon — it applies to all parser choices
3. **A short output dataset** (CSV/JSON is fine) for a small file sample
   - Save to your repo as `output/identifier_dataset.csv` or similar
   - Can come from any repo (including your own projects)
   - Shows you've tried it on real code outside of the tests!

**Parser Implementation Notes:**
- If you chose **DOM (ElementTree)**: Implement `extract_identifiers_dom()` using Option A patterns
- If you chose **SAX**: Implement `extract_identifiers_sax()` using Option B patterns  
- If you chose **XPath (lxml)**: Implement `extract_identifiers_dom()` OR `extract_identifiers_dom_xpath()` using Option C patterns
  - Add `lxml` to your `requirements.txt`
  - Your `build_file_identifier_dataset()` should accept `parser='dom'`

---

## Grading Outline

| Component | Weight |
|-----------|--------|
| Identifier extraction correctness (`dom` or `sax`) | 30% |
| Aggregate metrics correctness | 30% |
| Dataset production and structure | 30% |
| `main.py` updated | 10% |

---

## Hints

- Keep extractor functions pure: input XML string, output rows/dicts.
- srcML XML has namespaces. Be sure to handle them properly.
- Start small: parse one file, inspect output, then scale to a mini sample.
- **Parser choice guidance:**
  - **DOM (ElementTree)** — easiest, no dependencies, but can be very loop heavy
  - **SAX** — best for large files and memory constraints (big documents won't fit in RAM)
  - **XPath (lxml)** — most concise, easy to comprehend, requires XPath knowledge and lxml