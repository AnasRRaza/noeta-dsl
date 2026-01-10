# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Noeta** is a production-ready Domain-Specific Language (DSL) for data analysis that compiles to Python/Pandas code. It provides an intuitive, natural language-like syntax for data manipulation, statistical analysis, and visualization tasks.

**Status**: Production Ready (67% coverage, 167/250 operations)
**Core Implementation**: ~9,094 lines across 8 core modules
**Documentation**: ~14,600 lines across 14 files

---

## Quick Reference for Common Tasks

| Task | Command | Notes |
|------|---------|-------|
| Run a Noeta script | `noeta examples/demo_basic.noeta` | Using installed command |
| Run via Python | `python noeta_runner.py examples/demo_basic.noeta` | Direct script execution |
| Run inline code | `noeta -c 'load "data.csv" as d\ndescribe d'` | One-liner execution |
| View generated Python | Add `-v` flag | Shows compiled output |
| Enable type checking | Add `--type-check` flag | Validates column existence |
| Install Jupyter kernel | `python install_kernel.py` | For notebook integration |
| Run all tests | `pytest tests/` | Comprehensive test suite |
| Run specific test | `pytest tests/test_semantic.py -v` | Individual test file |
| Run with coverage | `pytest tests/ --cov=. --cov-report=html` | Coverage report |
| Install as package | `pip install -e .` | Editable install |
| View syntax examples | See `examples/` directory | 21 example files |

---

## Core Architecture

Noeta follows a classic compiler pipeline architecture with **six main compilation stages**:

```
Source Code → Lexer → Tokens → Parser → AST → Semantic Analyzer → Code Generator → Python Code
                                                     ↓
                                             Catch errors at
                                             compile-time! ✅
```

### Core Modules

1. **noeta_lexer.py** (916 lines, 150+ tokens): Tokenizes Noeta source code
2. **noeta_parser.py** (3,480 lines, 167+ methods): Builds Abstract Syntax Tree (AST)
3. **noeta_ast.py** (1,186 lines, 167 nodes): Defines all AST node types using dataclasses
4. **noeta_semantic.py** (1,717 lines, 138 validators): Validates AST for semantic correctness ⭐
5. **noeta_codegen.py** (1,795 lines, 167 visitors): Converts AST to Python/Pandas code
6. **noeta_errors.py** (~200 lines): Rich error formatting with position tracking
7. **noeta_runner.py** (~100 lines): CLI orchestration and execution
8. **noeta_kernel.py** (~180 lines): Jupyter kernel implementation

**Total Core Implementation**: 9,094 lines

### Key Design Patterns

- **Visitor Pattern**: Code generator and semantic analyzer traverse AST
- **Symbol Table**: Track dataset aliases and schemas across compilation
- **Dataclass Pattern**: All AST nodes use `@dataclass` for clean structure
- **Recursive Descent**: Parser implements top-down LL(1) parsing
- **Multi-Error Recovery**: Collect all errors before failing (not just first error)

---

## Development Commands

### Running Noeta Scripts

Using the installed `noeta` command:
```bash
# Run file
noeta examples/demo_basic.noeta

# Run inline code
noeta -c 'load "data.csv" as d
describe d'

# Verbose mode (shows generated Python)
noeta examples/demo_basic.noeta -v

# Enable column validation
noeta script.noeta --type-check
```

Using Python directly:
```bash
python noeta_runner.py examples/demo_basic.noeta
python noeta_runner.py -c 'load "data.csv" as d; describe d'
```

### Testing

Noeta has a comprehensive pytest test suite with 208+ tests covering all components:

```bash
# Run all tests
pytest tests/

# Run specific test file
pytest tests/test_semantic.py -v

# Run with coverage
pytest tests/ --cov=. --cov-report=html

# Run tests matching pattern
pytest tests/ -k semantic

# Run specific test function
pytest tests/test_semantic.py::TestSemanticUndefinedDataset::test_undefined_dataset_error
```

**Test Structure:**
- `tests/test_lexer.py` - Tokenization tests
- `tests/test_parser.py` - Syntax analysis tests
- `tests/test_semantic.py` - Semantic validation tests (comprehensive)
- `tests/test_codegen.py` - Code generation tests
- `tests/test_integration.py` - End-to-end tests
- `tests/test_errors.py` - Error handling tests

Run example `.noeta` files:
```bash
# Phase-specific tests
python noeta_runner.py examples/test_phase11_all_26_operations.noeta
python noeta_runner.py examples/test_phase12_basic.noeta

# Comprehensive integration test
python noeta_runner.py examples/test_comprehensive_all_phases.noeta
```

### Jupyter Kernel

Install and use:
```bash
# Install kernel
python install_kernel.py

# Start Jupyter
jupyter notebook

# Select "Noeta" kernel from kernel menu
```

### Package Installation

```bash
# Install as editable package
pip install -e .

# Install dependencies only
pip install -r requirements.txt

# Reinstall after module changes
pip install -e . --force-reinstall --no-deps
```

---

## Language Features

### Recent Features (January 2026)

#### Optional Alias Syntax ✅
**New in v2.1**: The `as <alias>` clause is now optional for 32 core operations. Operations without an alias display results immediately (exploratory mode), while operations with an alias store results in the symbol table (pipeline mode).

```noeta
# Display mode (no alias) - shows result immediately
filter sales where price > 100

# Storage mode (with alias) - stores for later use
filter sales where price > 100 as expensive
groupby expensive by {category} compute {sum: quantity} as summary
```

**Operations with Optional Alias Support (32):**
- Selection & Filtering: select, filter, updated_filter, sort
- Aggregation: groupby, join, merge
- Cleaning: dropna, fillna, drop_duplicates, fill_forward, fill_backward
- Transformations: mutate, apply, round, upper, lower, concat, substring
- Math: abs, sqrt, power, log, ceil, floor
- Cumulative: cumsum, cummax, cummin, cumprod
- Time Series: pct_change, diff, shift
- Advanced: normalize, binning, rolling

#### Semantic Validation System ✅
**Implemented December 2025**: Catches errors at **compile-time** instead of runtime.

```bash
# This catches undefined dataset errors before execution
noeta script.noeta
# Error: Dataset 'sale' has not been loaded or created
# Hint: Available datasets: sales, customers
# Did you mean: 'sales'?
```

**Features:**
- 138 validator methods covering all 167 operations
- Smart suggestions using Levenshtein distance
- Symbol table tracks dataset lifecycles
- Context-aware error messages

#### Multi-Error Reporting ✅
Shows **all errors at once**, not just the first one:

```
Found 3 errors in compilation:

Semantic Errors (3):
------------------------------------------------------------

[Error 1]
  Line 2, column 8:
      2 | select sale columns {price, quantity} as subset
                 ^^^^
    Dataset 'sale' has not been loaded or created
  Hint: Available datasets: sales
  Did you mean: 'sales'?

[Error 2]
  Line 3, column 8:
      3 | filter custmer where age > 25 as adults
                 ^^^^^^^
    Dataset 'custmer' has not been loaded or created
  ...
```

#### Column-Level Validation ✅
**Optional feature** enabled with `--type-check` flag. Validates column references at compile-time:

```bash
noeta script.noeta --type-check
```

```noeta
load "sales.csv" as sales
select sales columns {price, quantit} as result  # Typo in column name
```

**Error with --type-check:**
```
Semantic Error:
    Column 'quantit' does not exist in dataset 'sales'

Hint: Available columns: product_id, category, price, quantity, discount, date
```

**Features:**
- Fast: Only reads file headers (no data loading)
- Supports CSV, Excel, JSON, Parquet
- Validates 14 high-impact operations (select, filter, join, groupby, etc.)
- Permissive: Only validates when schema is known

---

## Implementation Status

### Completed Phases (12/15)

**Phase 1-10** (128 operations): Foundation - I/O, selection, filtering, cleaning, aggregation
**Phase 11** (26 operations): High-priority - cumulative, time series, advanced transforms
**Phase 12** (13 operations): Medium-priority - scaling, encoding, validation

### Coverage by Category

| Category | Coverage | Operations |
|----------|----------|------------|
| Data I/O | 100% ✅ | 10/10 |
| Selection & Projection | 100% ✅ | 7/7 |
| Filtering | 100% ✅ | 9/9 |
| Cleaning | 100% ✅ | 13/13 |
| Reshaping | 100% ✅ | 7/7 |
| Combining | 100% ✅ | 6/6 |
| Binning | 100% ✅ | 2/2 |
| Apply/Map | 100% ✅ | 4/4 |
| Cumulative | 100% ✅ | 4/4 |
| Time Series | 100% ✅ | 3/3 |
| Scaling | 100% ✅ | 4/4 |
| Encoding | 100% ✅ | 6/6 |
| Validation | 100% ✅ | 3/3 |
| Index Operations | 100% ✅ | 5/5 |
| Boolean Operations | 100% ✅ | 4/4 |
| String Operations | 64% ⚠️ | 14/22 |
| Date/Time | 58% ⚠️ | 14/24 |
| Math | 54% ⚠️ | 7/13 |
| Aggregation | 63% ⚠️ | 20/32 |
| Window Functions | 64% ⚠️ | 14/22 |
| Statistics | 47% ⚠️ | 9/19 |
| Visualization | 33% ⚠️ | 5/15 |
| **TOTAL** | **67%** | **167/250** |

**Production Ready For:**
- ✅ Data manipulation, ETL pipelines, data cleaning
- ✅ Time series analysis, date/time processing
- ✅ String cleaning, data aggregation, grouping
- ✅ ML preprocessing (scaling, encoding, validation)
- ✅ Exploratory data analysis, feature engineering

**Remaining Gaps** (83 operations in Phases 13-15):
- Trigonometric functions, advanced statistics
- Complex timezone operations, advanced visualizations
- Memory optimization, advanced window functions

See `STATUS.md` for detailed roadmap.

---

## Adding New Operations - Complete Workflow

**CRITICAL**: When adding any new operation, you MUST update ALL 5 core files in this exact order:

### Step 1: Add Token (noeta_lexer.py)

```python
# In TokenType enum
class TokenType(Enum):
    # ... existing tokens ...
    MY_OPERATION = "MY_OPERATION"

# In Lexer.__init__() keywords dictionary
self.keywords = {
    # ... existing keywords ...
    'my_operation': TokenType.MY_OPERATION,
}
```

### Step 2: Create AST Node (noeta_ast.py)

```python
@dataclass
class MyOperationNode(ASTNode):
    """Represents a my_operation statement."""
    source_alias: str          # ⚠️ Use source_alias, NOT source!
    new_alias: Optional[str]   # ⚠️ Use new_alias, NOT alias!
    param1: Optional[str] = None
    param2: Optional[int] = None
```

**⚠️ CRITICAL NAMING CONVENTIONS:**
- Use `source_alias` for input dataset (NOT `source`, `df`, `dataframe`)
- Use `new_alias` for output dataset (NOT `alias`)
- For two-input ops: `alias1`, `alias2` (e.g., JoinNode)
- For merge ops: `left_alias`, `right_alias` (e.g., MergeNode)

### Step 3: Add Parser Method (noeta_parser.py)

```python
def parse_my_operation(self):
    """Parse: my_operation <source> [param1=<value>] as <alias>"""
    self.expect(TokenType.MY_OPERATION)

    source_token = self.expect(TokenType.IDENTIFIER)
    source = source_token.value

    # Parse optional parameters
    param1 = None
    if self.match(TokenType.PARAM1):
        self.pos += 1
        self.expect(TokenType.EQUALS)
        param1 = self.parse_value()

    # Parse optional alias (if operation supports it)
    alias = None
    if self.match(TokenType.AS):
        self.pos += 1
        alias_token = self.expect(TokenType.IDENTIFIER)
        alias = alias_token.value

    return MyOperationNode(source, alias, param1)

# Add to parse_statement() dispatcher
def parse_statement(self):
    # ... existing cases ...
    elif self.match(TokenType.MY_OPERATION):
        return self.parse_my_operation()
```

### Step 4: Add Semantic Validator (noeta_semantic.py) ⭐ REQUIRED!

```python
def visit_MyOperationNode(self, node: MyOperationNode) -> None:
    """Validate my_operation operation."""
    # 1. Check source dataset exists
    source_info = self._check_dataset_exists(node.source_alias, node)

    # 2. Validate column exists (if applicable)
    if hasattr(node, 'column') and node.column:
        self._check_column_exists(source_info, node.column, node)

    # 3. Check column type (if applicable)
    if hasattr(node, 'column') and node.column:
        self._check_column_type(
            source_info,
            node.column,
            DataType.NUMERIC,  # or STRING, DATETIME, etc.
            node
        )

    # 4. Register result dataset (if creates new dataset)
    if node.new_alias:
        result_info = DatasetInfo(
            name=node.new_alias,
            columns=source_info.columns.copy(),  # or modified schema
            source=f"my_operation from {node.source_alias}"
        )
        self.symbol_table.define(node.new_alias, result_info)
```

**WHY SEMANTIC VALIDATION IS CRITICAL:**
- ✅ Catches undefined datasets at **compile-time** (not runtime)
- ✅ Validates column references before execution
- ✅ Provides helpful error messages with "Did you mean?" suggestions
- ✅ Maintains symbol table for cross-operation validation
- ✅ Enables better IDE support in the future

**WITHOUT semantic validation**, errors only appear at runtime when Python executes, giving poor error messages.

### Step 5: Add Code Generator Visitor (noeta_codegen.py)

```python
def visit_MyOperationNode(self, node: MyOperationNode) -> None:
    """Generate Pandas code for my_operation."""
    # Get source variable
    df_var = self.symbol_table.get(node.source_alias)
    if not df_var:
        raise ValueError(f"Unknown dataframe: {node.source_alias}")

    # Add necessary imports
    self.imports.add('import numpy as np')  # if needed

    # Generate Pandas code
    code = f"{df_var}.my_pandas_method("
    if node.param1:
        code += f"param1={repr(node.param1)}"
    code += ")"

    # Handle alias (display mode vs storage mode)
    if node.new_alias:
        # Storage mode - create new variable
        new_var = f"df_{len(self.symbol_table)}"
        self.code.append(f"{new_var} = {code}")
        self.symbol_table[node.new_alias] = new_var
        self.code.append(f"print(f'Applied my_operation to {node.source_alias}')")
    else:
        # Display mode - show result immediately
        self.code.append(f"print({code})")
```

### Step 6: Add Tests (tests/test_my_operation.py)

```python
import pytest
from noeta_lexer import Lexer, TokenType
from noeta_parser import Parser
from noeta_semantic import SemanticAnalyzer
from noeta_codegen import CodeGenerator
from noeta_errors import NoetaError

def test_my_operation_lexer():
    """Test tokenization of my_operation."""
    lexer = Lexer("my_operation data as result")
    tokens = lexer.tokenize()
    assert tokens[0].type == TokenType.MY_OPERATION

def test_my_operation_parser():
    """Test parsing of my_operation."""
    lexer = Lexer("my_operation data as result")
    parser = Parser(lexer.tokenize())
    ast = parser.parse()
    assert isinstance(ast[0], MyOperationNode)
    assert ast[0].source_alias == "data"
    assert ast[0].new_alias == "result"

def test_my_operation_semantic_undefined():
    """Test semantic error for undefined dataset."""
    source = "my_operation undefined_data as result"
    lexer = Lexer(source)
    parser = Parser(lexer.tokenize())
    ast = parser.parse()
    analyzer = SemanticAnalyzer()

    with pytest.raises(NoetaError) as exc:
        analyzer.analyze(ast)
    assert "undefined_data" in str(exc.value)
    assert "not been loaded" in str(exc.value)

def test_my_operation_codegen():
    """Test code generation."""
    source = """
    load "data.csv" as data
    my_operation data as result
    """
    lexer = Lexer(source)
    parser = Parser(lexer.tokenize())
    ast = parser.parse()
    analyzer = SemanticAnalyzer()
    analyzer.analyze(ast)
    codegen = CodeGenerator()
    python_code = codegen.generate(ast)
    assert "my_pandas_method" in python_code
```

Also create example file `examples/test_my_operation.noeta`:
```noeta
load csv "data/sales_data.csv" as sales
my_operation sales param1="value" as result
describe result
```

### Step 7: Update Documentation

1. **STATUS.md** - Update operation count and coverage percentage
2. **NOETA_COMMAND_REFERENCE.md** - Add syntax reference
3. **DATA_MANIPULATION_REFERENCE.md** - Add detailed documentation with:
   - Purpose and description
   - Syntax variations (3-5 examples)
   - Parameter specifications
   - Pandas equivalent code
   - Use cases and examples

### Step 8: Update setup.py (If Adding New Module)

**Only if you created a new Python module** (e.g., `noeta_myfeature.py`):

```python
# In setup.py
py_modules=[
    'noeta_lexer',
    'noeta_parser',
    'noeta_ast',
    'noeta_semantic',
    'noeta_codegen',
    'noeta_runner',
    'noeta_kernel',
    'noeta_errors',
    'noeta_myfeature',  # <-- Add here!
    'install_kernel',
    'test_noeta'
],
```

Then reinstall:
```bash
pip install -e . --force-reinstall --no-deps
```

**IMPORTANT**: This step is only needed when adding new modules. For adding operations to existing modules, skip this step.

---

## Quick Checklist for New Operations

When adding a new operation, check ALL these boxes:

- [ ] ✅ `noeta_lexer.py` - Added TokenType enum and keyword mapping
- [ ] ✅ `noeta_ast.py` - Created AST node with correct field names (source_alias, new_alias)
- [ ] ✅ `noeta_parser.py` - Added parse method and dispatcher case
- [ ] ✅ `noeta_semantic.py` - Added semantic validator (CRITICAL!) ⭐
- [ ] ✅ `noeta_codegen.py` - Added code generator visitor
- [ ] ✅ `tests/test_*.py` - Added comprehensive tests (lexer, parser, semantic, codegen)
- [ ] ✅ `examples/test_*.noeta` - Created example usage file
- [ ] ✅ `STATUS.md` - Updated coverage metrics
- [ ] ✅ `NOETA_COMMAND_REFERENCE.md` - Added syntax reference
- [ ] ✅ `DATA_MANIPULATION_REFERENCE.md` - Added detailed documentation
- [ ] ✅ (If new module) `setup.py` - Updated py_modules list

**Common mistake**: Forgetting the semantic validator! Without it, errors are only caught at runtime instead of compile-time.

---

## Key Implementation Details

### Symbol Table Management

The semantic analyzer and code generator both maintain symbol tables to track datasets:

**In Semantic Analyzer:**
```python
# DatasetInfo tracks schema and source
class DatasetInfo:
    name: str
    columns: Dict[str, ColumnInfo]  # Column name -> type info
    source: str                      # Where it came from

# Register new datasets
self.symbol_table.define(alias, DatasetInfo(...))

# Check existence
info = self.symbol_table.lookup(alias)
```

**In Code Generator:**
```python
# Map Noeta aliases to Python variable names
self.symbol_table[alias] = f"df_{counter}"

# Generate unique variables
df_var = f"df_{len(self.symbol_table)}"
self.code.append(f"{df_var} = pd.read_csv('{filepath}')")
self.symbol_table[alias] = df_var
```

### Import Management

Code generator collects imports dynamically:

```python
# In CodeGenerator.__init__
self.imports = set()

# In visitor methods
self.imports.add('import numpy as np')
self.imports.add('from sklearn.preprocessing import StandardScaler')

# In generate(), emit imports first
for imp in sorted(self.imports):
    code_lines.append(imp)
```

**Standard imports** (always included):
- `pandas as pd`
- `numpy as np`
- `matplotlib.pyplot as plt`
- `seaborn as sns`

### Visualization Handling

The generator tracks plot operations via `self.last_plot`. If any visualization is used, it automatically adds `plt.show()` at the end. The Jupyter kernel captures matplotlib figures and displays them as PNG images.

### Parser Helper Methods

The parser provides utilities for navigation and validation:

```python
current_token()       # Returns token at current position
peek_token(offset)    # Looks ahead without advancing
expect(token_type)    # Consumes and validates expected token
match(*token_types)   # Checks if current token matches any type
parse_value()         # Parses literal values (strings, numbers, booleans, None)
parse_list_value()    # Parses Python lists
parse_dict_value()    # Parses Python dictionaries
```

### Error Handling Infrastructure

**Error Categories** (in `noeta_errors.py`):
- `LEXER`: Invalid characters, unterminated strings
- `SYNTAX`: Grammar violations, unexpected tokens
- `SEMANTIC`: Undefined datasets, invalid references (caught at compile-time!)
- `TYPE`: Type mismatches, invalid operations
- `RUNTIME`: Execution failures

**Multi-Error Reporting:**
```python
# Collect errors instead of failing on first
errors = []
for node in ast:
    try:
        self.validate(node)
    except NoetaError as e:
        errors.append(e)

if errors:
    if len(errors) == 1:
        raise errors[0]
    else:
        raise create_multi_error(errors)  # Show all errors grouped
```

---

## File Structure

### Core Compiler Components
```
noeta_lexer.py       (916 lines)   - Tokenization with regex patterns
noeta_parser.py      (3,480 lines) - Recursive descent parser
noeta_ast.py         (1,186 lines) - AST node definitions (dataclasses)
noeta_semantic.py    (1,717 lines) - Semantic validation (138 validators)
noeta_codegen.py     (1,795 lines) - Code generation (167 visitors)
noeta_errors.py      (~200 lines)  - Error formatting and utilities
```

### Execution & Testing
```
noeta_runner.py      (~100 lines)  - CLI orchestration
noeta_kernel.py      (~180 lines)  - Jupyter kernel
install_kernel.py                  - Kernel installer
test_noeta.py                      - Basic tests
tests/                             - Comprehensive pytest suite
  ├── test_lexer.py               - Tokenization tests
  ├── test_parser.py              - Syntax tests
  ├── test_semantic.py            - Validation tests (comprehensive)
  ├── test_codegen.py             - Code generation tests
  ├── test_integration.py         - End-to-end tests
  └── test_errors.py              - Error handling tests
```

### Examples & Data
```
examples/            - 21 .noeta example files
  ├── demo_basic.noeta
  ├── demo_advanced.noeta
  ├── test_phase11_all_26_operations.noeta
  ├── test_phase12_basic.noeta
  ├── test_comprehensive_all_phases.noeta
  └── ...
data/                - Sample CSV/Excel/JSON files for testing
```

### Documentation (14,600+ lines total)
```
CLAUDE.md            (This file) - Development guide
README.md            - User-facing quick start
STATUS.md            - Implementation status (single source of truth)
DOCUMENTATION_MAP.md - Master documentation index
FLOW_DIAGRAM.md      - Visual architecture (10 Mermaid diagrams)
DATA_MANIPULATION_REFERENCE.md (3,220 lines) - Complete operation reference
DATA_ANALYSIS_REFERENCE.md (2,131 lines) - Statistical functions reference
NOETA_COMMAND_REFERENCE.md - Quick syntax reference
SYNTAX_BLUEPRINT.md  - Design principles and patterns
TESTING.md           - Complete testing guide
docs/archive/        - Historical phase documentation
```

### Build & Config
```
setup.py             - Package configuration
requirements.txt     - Python dependencies
pytest.ini           - Pytest configuration
.gitignore           - Git ignore rules
build/               - Build artifacts
noeta.egg-info/      - Package metadata
```

---

## Debugging and Troubleshooting

### Verbose Mode

Shows generated Python code before execution:

```bash
noeta examples/demo.noeta -v
```

Output shows:
1. Generated Python code
2. All imports
3. Execution result

This is invaluable for:
- Understanding how Noeta compiles to Python
- Debugging compilation issues
- Learning Pandas operations
- Verifying correctness

### Common Issues and Solutions

#### 1. Symbol Not Found
**Issue**: `ValueError: Unknown dataframe: mydata`

**Cause**: Alias not registered in symbol table

**Solution**:
- Check if semantic validator calls `self.symbol_table.define()` for new datasets
- Verify parser correctly extracts alias from `as <alias>` clause
- Enable verbose mode to see symbol table state

#### 2. Import Missing
**Issue**: `NameError: name 'StandardScaler' is not defined`

**Cause**: Code generator didn't add required import

**Solution**:
```python
# In visitor method
self.imports.add('from sklearn.preprocessing import StandardScaler')
```

#### 3. Semantic Validation Missing
**Issue**: Undefined dataset error only caught at runtime

**Cause**: No semantic validator for operation

**Solution**: Add visitor method in `noeta_semantic.py`:
```python
def visit_MyOperationNode(self, node):
    source_info = self._check_dataset_exists(node.source_alias, node)
    # ... rest of validation
```

#### 4. Column Validation Not Working
**Issue**: Column errors not caught at compile-time

**Cause**: `--type-check` flag not used

**Solution**:
```bash
noeta script.noeta --type-check
```

#### 5. Visualization Not Showing
**Issue**: Plot operations don't display

**Cause**: `self.last_plot` not set

**Solution**:
```python
# In visualization visitor
self.last_plot = True
```

#### 6. Test Failures
**Issue**: Tests fail after changes

**Solution**:
```bash
# Run specific test to identify issue
pytest tests/test_semantic.py::test_my_operation -v

# Check coverage to find untested code
pytest tests/ --cov=. --cov-report=html
```

---

## Dependencies

### Required Packages

From `requirements.txt`:
```
pandas>=1.3.0
numpy>=1.20.0
matplotlib>=3.3.0
seaborn>=0.11.0
scipy>=1.7.0
scikit-learn>=0.24.0
jupyter>=1.0.0
ipykernel>=6.0.0

# Testing
pytest>=7.0.0
pytest-cov>=4.0.0
pytest-xdist>=3.0.0
```

### Version Compatibility

- **Python**: 3.8+ (tested on 3.8-3.12)
- **Pandas**: 1.x or 2.x (code generator handles both)
- **NumPy**: Any recent version
- **Matplotlib**: 3.x
- **Seaborn**: 0.11+
- **Scikit-learn**: 0.24+

---

## Performance Considerations

### Compilation Performance

**Lexer**: Single-pass tokenization with compiled regex patterns
**Parser**: LL(1) grammar with no backtracking, single-token lookahead
**Semantic**: Single-pass validation over AST
**Codegen**: Single-pass visitor pattern

**Result**: Very fast compilation (<100ms for typical scripts)

### Generated Code Performance

The code generator produces **idiomatic Pandas code**:

✅ **GOOD - Vectorized operations:**
```python
df['price'] = df['price'] * 1.1
```

❌ **BAD - Row-by-row iteration:**
```python
for i in range(len(df)):
    df.loc[i, 'price'] = df.loc[i, 'price'] * 1.1
```

### Type Checking Performance

With `--type-check`, the semantic analyzer reads file headers:

```python
# Very fast - only reads header, not data
df = pd.read_csv(filepath, nrows=0)  # CSV
schema = pd.read_parquet(filepath).columns  # Parquet
```

**Impact**: Negligible overhead (<50ms per file)

---

## Current Project Status

**Last Updated**: January 2026
**Version**: 2.1
**Status**: ✅ Production Ready

### Completed Work

- ✅ 167/250 operations (67% coverage)
- ✅ Phases 1-12 completed
- ✅ Comprehensive semantic validation (138 validators)
- ✅ Multi-error reporting system
- ✅ Column-level validation (--type-check)
- ✅ Optional alias syntax (32 operations)
- ✅ 208+ pytest tests
- ✅ 14,600+ lines of documentation

### Recent Updates

**January 2026**: Optional alias syntax for 32 operations
**December 2025**: Column-level validation with `--type-check`
**December 2025**: Multi-error reporting system
**December 2025**: Semantic validation system (compile-time error detection)

### Next Steps

**Phase 13** (30 operations):
- Trigonometric functions (sin, cos, tan, etc.)
- Advanced string operations
- Additional date/time operations

**Phase 14** (30 operations):
- Advanced aggregations and window functions
- Additional reshaping operations
- Memory optimizations

**Phase 15** (23 operations):
- Statistical hypothesis tests
- Additional visualization types

See `STATUS.md` for detailed roadmap.

---

## Documentation Resources

### For Development
- **CLAUDE.md** (this file) - Comprehensive development guide
- **FLOW_DIAGRAM.md** - Visual architecture with 10 Mermaid diagrams
- **SYNTAX_BLUEPRINT.md** - Design principles and patterns
- **.cursorrules** - IDE-specific development rules

### For Implementation Status
- **STATUS.md** - Single source of truth for coverage and roadmap
- **DOCUMENTATION_MAP.md** - Master index of all documentation
- **PHASE11_COMPLETION_SUMMARY.md** - Phase 11 details
- **PHASE12_COMPLETION_SUMMARY.md** - Phase 12 details

### For Users
- **README.md** - Quick start guide
- **DEMO_GUIDE.md** - Interactive tutorials
- **NOETA_COMMAND_REFERENCE.md** - Quick syntax reference
- **DATA_MANIPULATION_REFERENCE.md** - Complete operation reference (3,220 lines)
- **DATA_ANALYSIS_REFERENCE.md** - Statistical functions (2,131 lines, 9/350 functions documented)

### For Testing
- **TESTING.md** - Complete testing guide with all pytest commands
- `tests/` directory - 208+ comprehensive tests
- `examples/` directory - 21 example .noeta files

---

## Important Notes for Future Development

### When Adding Operations

1. **ALWAYS** update all 5 core files (lexer, ast, parser, semantic, codegen)
2. **NEVER** skip semantic validation - it's what makes compile-time error detection possible
3. **ALWAYS** use correct field names: `source_alias`, `new_alias` (not `source`, `alias`)
4. **ALWAYS** add comprehensive tests (lexer, parser, semantic, codegen)
5. **ALWAYS** update documentation (STATUS.md, references)

### When Adding Modules

1. Add module to `setup.py` in `py_modules` list
2. Reinstall package: `pip install -e . --force-reinstall --no-deps`
3. Add tests for new module
4. Update documentation

### Code Quality Standards

- Use type hints for all functions
- Add docstrings for all public methods
- Follow existing naming conventions
- Keep lines under 100 characters
- Run tests before committing
- Update documentation with code changes

---

**Last Updated**: January 10, 2026
**Project Status**: ✅ PRODUCTION READY with Semantic Validation
**Coverage**: 167/250 operations (67%)
**Core Codebase**: 9,094 lines of implementation + 14,600 lines of documentation
**Key Features**: ✅ Compile-time validation, ✅ Multi-error reporting, ✅ Column validation, ✅ Optional aliases
**Maintainer**: Noeta Development Team
