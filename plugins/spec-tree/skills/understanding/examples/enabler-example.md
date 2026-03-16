# Tree Parser

## Enables

The tree parser reads the `spx/` directory structure and produces an in-memory tree representation. Every command that operates on the spec tree — status, verify, context injection — depends on this parser to translate the filesystem into a traversable data structure.

## Assertions

### Scenarios

- Given a directory with `.enabler` suffix, when parsed, then the node type is enabler ([test](tests/tree-parser.unit.test.{ext}))
- Given a directory with `.outcome` suffix, when parsed, then the node type is outcome ([test](tests/tree-parser.unit.test.{ext}))
- Given a nested directory structure 3 levels deep, when parsed, then the tree preserves parent-child relationships ([test](tests/tree-parser.unit.test.{ext}))

### Mappings

- Directory suffix maps to node type: `.enabler` → enabler, `.outcome` → outcome ([test](tests/tree-parser.unit.test.{ext}))

### Properties

- Parsing is deterministic: the same directory structure always produces the same tree ([test](tests/tree-parser.unit.test.{ext}))
- Round-trip consistency: serializing a parsed tree and re-parsing produces an identical tree ([test](tests/tree-parser.unit.test.{ext}))

### Compliance

- ALWAYS: ignore dotfiles and `node_modules` during traversal — prevents accidental inclusion of tooling artifacts
- NEVER: follow symlinks outside the `spx/` root — prevents path traversal
