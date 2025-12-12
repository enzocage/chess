https://enzocage.de/code/chess

AI Chess by Felix Schmidt - Technical Documentation

This project implements a strategic Chess AI using purely browser-based technologies (HTML5, CSS3, JavaScript). The core logic relies on classical chess programming algorithms to evaluate positions and determine the optimal move.

🧠 AI Architecture

The Artificial Intelligence is built upon a Negamax search algorithm enhanced with Alpha-Beta Pruning and several heuristic optimizations to play at a competent level.

1. Board Evaluation (evaluateBoard)

The AI evaluates a given board state by calculating a score in "centipawns". A positive score favors White, negative favors Black. The evaluation consists of three main components:

Material Weight: Standard values are assigned to pieces:

Pawn: 100, Knight: 320, Bishop: 330, Rook: 500, Queen: 900, King: 20000.

Piece-Square Tables (PST):

Defines positional bonuses for every piece on every square.

Example: Knights are penalized on edges and rewarded in the center. Pawns are rewarded for advancing.

Dynamic King Safety: The AI switches to a specific PST_K_END table in the endgame to encourage the King to become active in the center, whereas in the middlegame it is encouraged to stay safe in the corners.

Mobility:

The AI calculates the number of pseudo-legal moves available.

It adds a small bonus (5 centipawns per move) to encourage active piece play and open lines.

2. Search Algorithm

The engine does not look at every single possibility (which is impossible) but uses a smart tree search:

Negamax: A simplified variant of Minimax that relies on the zero-sum property of chess ($Score(White) = -Score(Black)$).

Iterative Deepening:

The AI searches depth 1, then depth 2, up to MAX_DEPTH (currently 4).

This ensures that if the search is interrupted (or for time management), we always have a "best move so far". More importantly, it populates the Transposition Table to speed up deeper searches.

Quiescence Search:

To prevent the "Horizon Effect" (stopping the search in the middle of a capture sequence), the engine continues searching only capture moves at the leaf nodes until a "quiet" position is reached.

3. Move Ordering & Optimization

To make Alpha-Beta pruning effective, good moves must be searched first. The engine uses a sophisticated sorting pipeline:

Transposition Table (Hash Move):

If the position was already analyzed in a previous iteration (or depth), the best move found then is tried immediately. This is the biggest performance booster.

MVV-LVA (Most Valuable Victim - Least Valuable Aggressor):

Captures are sorted to prioritize, for example, a Pawn capturing a Queen over a Queen capturing a Pawn.

Killer Heuristic:

Moves that caused a "beta-cutoff" (refutation) in a sibling node at the same depth are stored and tried early. These are often strong, quiet positional moves.

4. Transposition Table (TT)

A Map stores the evaluation results of positions (keyed by FEN string).

It stores the Depth, Score, Flag (Exact, Alpha, or Beta), and the Best Move.

This prevents the AI from recalculating the same position multiple times (e.g., if reached via a different move order).

📊 Win Probability Logic

The UI displays a winning percentage based on the centipawn score:


$$P(win) = \frac{1}{1 + e^{-score / 180}}$$

A sigmoid function converts the raw score into a probability.

A score of +180 (approx. 2 pawns advantage) results in ~73% win chance.

🛠 Tech Stack

Logic: Vanilla JavaScript (ES6+).

Rules Engine: chess.js (Handles move generation, validation, FEN parsing).

Rendering: HTML5 DOM with SVG pieces (no external images required).

Styling: CSS3 Flexbox/Grid for responsive layout.

🚀 How to Run

Simply open index.html in any modern web browser. No server or build process is required.

Created by Felix Schmidt
