# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build System and Common Commands

Apache Lucene uses Gradle as its build system. **Always use the `./gradlew` wrapper script** instead of a system-installed gradle to ensure correct version compatibility.

### Essential Commands

**Building and Testing:**
```bash
./gradlew assemble                    # Build all JARs
./gradlew check                       # Run all tests and validation
./gradlew tidy                        # Format code (required before commits)
./gradlew test                        # Run all unit tests
./gradlew check -x test              # Run validation without tests
```

**Module-specific operations:**
```bash
./gradlew -p lucene/core test        # Test specific module
./gradlew -p lucene/core check       # Check specific module
./gradlew -p lucene/core assemble    # Build specific module
```

**Single test execution:**
```bash
./gradlew -p lucene/core test --tests "TestDemo"
./gradlew -p lucene/core test --tests "*testFeatureMissing*"
./gradlew -p lucene/core test --tests "org.apache.lucene.document.*"
```

**Test iteration (for debugging flaky tests):**
```bash
./gradlew -p lucene/core test --tests TestDemo -Ptests.iters=5
./gradlew -p lucene/core beast -Ptests.dups=10 --tests TestDemo
```

**Documentation:**
```bash
./gradlew documentation              # Generate all documentation
./gradlew -p lucene/core javadoc     # Generate module javadocs
```

**Validation and maintenance:**
```bash
./gradlew :dependencyCheckAnalyze    # OWASP vulnerability check
./gradlew mavenLocal                 # Create local Maven repository
```

### Test Properties and Randomization

Lucene tests use randomization. Important properties:
- `-Ptests.seed=DEADBEEF` - Use specific seed for reproducible tests
- `-Ptests.verbose=true` - Enable verbose test output
- `-Ptests.filter=@Nightly` - Run tests with specific annotations
- `-Ptests.heapsize=32g` - Increase heap size for memory-intensive tests

## Code Architecture

### Core Module Structure

**Primary packages in `lucene/core/src/java/org/apache/lucene/`:**
- `index/` - Index reading/writing (IndexWriter, IndexReader, etc.)
- `search/` - Search functionality (IndexSearcher, Query, Scorer, etc.)
- `document/` - Document model (Document, Field types)
- `analysis/` - Text analysis (Analyzer, Tokenizer, TokenFilter)
- `store/` - Storage abstractions (Directory, IndexInput/Output)
- `util/` - Utility classes and data structures
- `codecs/` - Pluggable encoding formats
- `geo/` - Geospatial functionality

### Key Architecture Patterns

**Modular Design:**
- `lucene/core` - Core indexing and search functionality
- `lucene/analysis/*` - Language-specific analyzers
- `lucene/queries` - Additional query types
- `lucene/facet` - Faceted search
- `lucene/highlighter` - Search result highlighting
- `lucene/suggest` - Auto-complete functionality
- `lucene/spatial-*` - Geospatial search
- `lucene/backward-codecs` - Backward compatibility

**Extension Points:**
- Analyzers for custom text processing
- Codecs for custom storage formats
- Queries for custom scoring
- Collectors for custom result gathering

### Testing Structure

Tests are organized in parallel structure:
- `lucene/core.tests` - Tests for core module
- `lucene/analysis.tests` - Tests for analysis modules
- `lucene/test-framework` - Testing utilities and base classes

## Code Style and Formatting

**Automatic formatting is enforced** using google-java-format:
- Run `./gradlew tidy` before committing
- Wildcard imports are banned
- No sections can be excluded from formatting
- Code may need restructuring for readability after formatting

**Additional checks:**
- eclint for general file formatting
- Spotless for Gradle script formatting
- Error-prone for additional Java validations

## Module Dependencies

Key dependency relationships:
- Most modules depend on `lucene/core`
- `lucene/test-framework` provides testing utilities
- Analysis modules are generally independent of each other
- Backward compatibility maintained through `lucene/backward-codecs`

## Development Requirements

- **Java 24** - Required for current main branch
- **Perl and Python 3** - Required for full validation (`./gradlew check`)
- **Git** - Preserve original line breaks (avoid `core.autocrlf=true`)