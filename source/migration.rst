Migration
==================

Now it's time to make the enemies our player will have to dodge. Their behavior
will not be very complex: mobs will spawn randomly at the edges of the screen,
choose a random direction, and move in a straight line.

We'll create a ``Mob`` scene, which we can then *instance* to create any number
of independent mobs in the game.
