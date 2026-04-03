# schess

`schess` is a small chess engine prototype written in Surge.

The current focus is rule correctness and compiler/runtime stability, not playing strength yet. The codebase now supports legal move generation with self-check filtering, castling, en passant, promotions, and a small regression runner that is used to validate behavior on both the VM and LLVM backends.

## Layout

- `main.sg` - demo entrypoint that plays a short scripted line and prints the board
- `chess/` - board model, move generation, legality, position state, and engine stub
- `tests/regression.sg` - regression runner for castling and en passant

## Run

From the `schess/` directory:

```bash
../surge run
../surge run --backend llvm
```

The project is configured as a directory module, so `cd schess && ../surge run` is the intended entrypoint.

## Verify

Diagnostics:

```bash
../surge diag . --format short
../surge diag tests/regression.sg --format short
```

Regression checks:

```bash
../surge run tests/regression.sg
../surge run --backend llvm tests/regression.sg
```

## Current Scope

Implemented:

- normal legal move generation for all pieces
- self-check filtering
- promotion generation and application
- castling generation, legality checks, and board updates
- en passant generation, legality checks, and board updates
- position state fields for castling rights, en passant target, halfmove clock, and fullmove number

Not implemented yet:

- checkmate and stalemate reporting as explicit game status
- evaluation and search beyond the current engine stub
- FEN, SAN, PGN, UCI, opening book, tablebases
- repetition and 50-move rule adjudication

## Development Notes

This project is also used as a stress target for Surge itself. New work should be checked on both backends whenever possible:

```bash
../surge run ...
../surge run --backend llvm ...
```

If VM and LLVM diverge, treat that as a potential Surge bug first and only then assume the engine code is wrong.
