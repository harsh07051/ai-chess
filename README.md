 It is an AI-based chess game developed using javascript,html, and CSS that allows users to play with ai by finding the best move for it using an alpha-beta search on binary trees and quiescence search.
It uses bit manipulation to implement special moves like castling and en passant capture and detects pieces' positions using hashmaps.
try it here.https://classy-kelpie-5a6b43.netlify.app/

How min-max works
Min-max is a pair of functions that look almost the same, or one function that has duplicated logic.  There is a better function that does the same thing with less code, but in response to an email criticism of this page, I will first describe the "pure" (inelegant) min-max function.  Here's the code:

int MinMax(int depth)

{

    if (SideToMove() == WHITE)    // White is the "maximizing" player.

        return Max(depth);

    else                          // Black is the "minimizing" player.

        return Min(depth);

}

 

int Max(int depth)

{

    int best = -INFINITY;

 

    if (depth <= 0)

        return Evaluate();

    GenerateLegalMoves();

    while (MovesLeft()) {

        MakeNextMove();

        val = Min(depth - 1);

        UnmakeMove();

        if (val > best)

            best = val;
    }

    return best;

}

 

int Min(int depth)

{

    int best = INFINITY;  // <-- Note that this is different than in "Max".

 

    if (depth <= 0)

        return Evaluate();

    GenerateLegalMoves();

    while (MovesLeft()) {

        MakeNextMove();

        val = Max(depth - 1);

        UnmakeMove();

        if (val < best)  // <-- Note that this is different than in "Max".

            best = val;
    }

    return best;

}

The code is called something like this:

val = MinMax(5);

This will return the value of the position, given 5-plies of look-ahead.

The "Evaluate" function used in the above is the one described in my first definition -- it always returns the value of the position from White's point of view.

I'll briefly describe how the function operates.  Let's say that at the root position (the position on the board now), it's White's turn to move.  The Max function is called, and all of White's legal moves are generated.  In each resulting position, the "Min" function is called.  The "Min" function scores the position and returns a value.  Since it is White to move, and White wants a more positive score if possible, the move with the largest score is selected as best, and the value of this move is returned.

The "Min" function works in reverse.  The "Min" function is called when it's Black's turn to move, and black wants a more negative score, so the move with the most negative score is selected.

These functions are dual recursive, meaning that they call each other until the desired search depth is reached.  When the functions "bottom out", they return the result of the "Evaluate" function.

If you call "MinMax" with a depth value of 1, essentially all that happens is that the "Evaluate" function is called after each legal move, and the position that results in the "best" value for the side to move is selected.  If depth is greater than one, the other side gets a chance to respond, and chose its best move.

The above shouldn't be hard to understand, but it's a lot of code, and there is a better way.

The nega-max function
Nega-max is just min-max with an optimization.  The "Evaluate" function returns scores via my second definition -- it returns scores that are positive if the side to move at the current node is ahead, and everything else is also viewed from the perspective of the side to move.  When the value is returned, it is negated, because it is now being viewed from the perspective of the other side.  Here is the code:

int NegaMax(int depth)

{

    int best = -INFINITY;

 

    if (depth <= 0)

        return Evaluate();

    GenerateLegalMoves();

    while (MovesLeft()) {

        MakeNextMove();

        val = -NegaMax(depth - 1); // Note the minus sign here.

        UnmakeMove();

        if (val > best)

            best = val;
    }

    return best;

}

The function negates the return value in order to reflect the change and perspective that results when the side to move is changed.  So let's say that you call this from the root with White to move.  If there is no remaining depth, "Evaluate" returns its value from the White perspective.  If there is depth remaining, the successor positions are generated, and the function recurses once for each of those.  Each of these calls evaluates the position from the Black perspective, meaning that the score is larger if Black is doing better.  When the values are returned, they are negated, so that they may be evaluated from White's point of view.

This function traverses the same nodes as "min-max" in the same order, and produces the same result.  It's much less code, which means that there is less opportunity to create a bug due to code replication, and the code is easier to maintain.

 credit:-http://www.seanet.com/~brucemo/topics/minmax.htm
