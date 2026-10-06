---
title: Chess Engine
period: "2022-2023"
date: "2022-01-01"
---

*[Github (UCI)](https://github.com/glolichen/potato-chess-uci), [Github (Browser)](https://github.com/glolichen/potato-chess-browser)*

I made a chess engine while bored in middle school during Covid. I originally made a browser version [here](https://glolichen.github.io/potato-chess-browser/) (it still works, you can play against it), which runs fully client-side on WebAssembly (compiled from C++ with [Emscripten](https://github.com/emscripten-core/emscripten)). I then made a UCI-compliant (a protocol that chess engines follow for other applications) version that doesn't have to run fully in the browser client and supports things like multithreading and ponder (thinking during opponent's turn) and is therefore stronger.

I set it up as a [Lichess bot](https://lichess.org/@/potato-chess-bot) and it has a rating of around 1800-2000 (back when it was up). It ran on my old computer when I wasn't using it. I believe its "true" rating to be around slightly higher (around 2000) because it almost exclusively faced other bots. It will probably be at least a bit stronger running on a more powerful computer/server.

Programming this used a lot of cool programming techniques I did not know at the time: bitwise operators and bitmaps ("bitboard" move generation), multithreading, Minimax-derived search functions, web development for website, etc.

 - 30-100 million nodes per second move generator (depends on platform and position)
   - Keeps track of pinned pieces and the direction they are pinned
   - Keeps track of squares attacked by the opponent to prevent king from moving there
 - Tapered static evaluation (uses game phase to "weigh" the importance of midgame score to endgame score) with piece tables
 - Minimax search with Alpha-Beta pruning
 - Transposition table with Zobrist Hashing
 - Move ordering is used to guess the strength of moves to speed up AB pruning
 - Syzygy endgame tablebase (powered by [Lichess](https://github.com/lichess-org/lila-tablebase))

