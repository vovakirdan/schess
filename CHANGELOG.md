# Changelog

## 2026-04-03

This round focused on turning `schess` from a movegen prototype into a more complete chess-position core.

### Added

- position state for castling rights
- en passant target tracking
- halfmove and fullmove counters
- dedicated special-move helpers in `chess/special_moves.sg`
- regression runner in `tests/regression.sg`
- explicit game status handling in `chess/status.sg`
- baseline search layer in `chess/search.sg`
- `perft(game, depth)` utility in `chess/perft.sg`

### Changed

- `legal_moves` now uses full game state instead of only the board plus side-to-move
- `is_legal_move` now validates against full position state
- `apply_move` now updates derived position state after every move
- the engine helper now exposes task-based move search with explicit depth control
- the demo and regression code were updated to match current ownership and async diagnostics

### Implemented Rules

- white and black castling rights are tracked and cleared when king or rook moves, or when a rook is captured on its home square
- castling legality now checks empty path squares and attacked transit squares
- en passant targets are created only after double pawn pushes
- en passant captures are generated and applied correctly
- halfmove clock resets on pawn moves and captures
- fullmove number increments after Black moves
- game status is recomputed after each move and distinguishes check, checkmate, and stalemate
- search uses a material evaluator with negamax/alpha-beta and a root parallel fan-out
- regression now checks start-position `perft(1)=20` and `perft(2)=400`

### Validation

The following checks pass on both VM and LLVM backends:

- `cd schess && ../surge diag . --format short`
- `cd schess && ../surge diag tests/regression.sg --format short`
- `cd schess && ../surge run`
- `cd schess && ../surge run --backend llvm`
- `cd schess && ../surge run tests/regression.sg`
- `cd schess && ../surge run --backend llvm tests/regression.sg`

### Remaining Work

- notation and protocol support
- draw rules beyond the current move counters
- stronger search features: iterative deepening, move ordering, transposition tables
