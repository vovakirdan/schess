# Changelog

## 2026-04-03

This round focused on turning `schess` from a movegen prototype into a more complete chess-position core.

### Added

- position state for castling rights
- en passant target tracking
- halfmove and fullmove counters
- dedicated special-move helpers in `chess/special_moves.sg`
- regression runner in `tests/regression.sg`

### Changed

- `legal_moves` now uses full game state instead of only the board plus side-to-move
- `is_legal_move` now validates against full position state
- `apply_move` now updates derived position state after every move
- the engine helper and demo code were updated to match current ownership diagnostics

### Implemented Rules

- white and black castling rights are tracked and cleared when king or rook moves, or when a rook is captured on its home square
- castling legality now checks empty path squares and attacked transit squares
- en passant targets are created only after double pawn pushes
- en passant captures are generated and applied correctly
- halfmove clock resets on pawn moves and captures
- fullmove number increments after Black moves

### Validation

The following checks pass on both VM and LLVM backends:

- `cd schess && ../surge diag . --format short`
- `cd schess && ../surge diag tests/regression.sg --format short`
- `cd schess && ../surge run`
- `cd schess && ../surge run --backend llvm`
- `cd schess && ../surge run tests/regression.sg`
- `cd schess && ../surge run --backend llvm tests/regression.sg`

### Remaining Work

- explicit game status: check, mate, stalemate
- search and evaluation
- notation and protocol support
- draw rules beyond the current move counters
