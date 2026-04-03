# schess

`schess` is a small chess engine prototype written in Surge.

The current focus is rule correctness, a minimal usable engine core, and compiler/runtime stability. The codebase now supports legal move generation with self-check filtering, castling, en passant, promotions, explicit game status tracking, a small search layer, and a regression runner that is used to validate behavior on both the VM and LLVM backends.

## Layout

- `main.sg` - demo entrypoint that plays a short scripted line and prints the board
- `chess/` - board model, move generation, legality, position state, search, and engine API
- `tests/regression.sg` - regression runner for rules, status, perft, and search smoke tests

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
- explicit game status reporting: `ongoing`, `check`, `checkmate`, `stalemate`
- baseline material evaluation plus negamax/alpha-beta move search
- root-level parallel search path with a snapshot-based task API
- `perft(game, depth)` for move-generation validation

Not implemented yet:

- FEN, SAN, PGN, UCI, opening book, tablebases
- repetition and 50-move rule adjudication
- deeper search features such as iterative deepening, transposition tables, and stronger move ordering

## Development Notes

This project is also used as a stress target for Surge itself. New work should be checked on both backends whenever possible:

```bash
../surge run ...
../surge run --backend llvm ...
```

If VM and LLVM diverge, treat that as a potential Surge bug first and only then assume the engine code is wrong.

## Library Howto

The module can be used directly from another `.sg` file:

```surge
import ./chess::{initial_game, Engine, perft};

@entrypoint
fn main() {
    let game = initial_game();
    let engine = Engine { game = game };

    print(perft(&engine.game, 2) to string);

    compare engine.suggest_move_depth(1).await() {
        Success(Some(mv)) => print("move found");
        Success(nothing) => print("no legal move");
        Cancelled() => print("search cancelled");
    };
}
```

For regression use, prefer a shallow depth such as `1` or `2`. The search layer is intentionally minimal for now and deeper searches are still more useful as development/debugging tools than as a strong playing engine.
