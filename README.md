<img src="https://raw.githubusercontent.com/alexdbooth/Game-of-Life-Haskell/master/images/Variable_FPS_Example.gif" width="300"><img src="https://raw.githubusercontent.com/alexdbooth/Game-of-Life-Haskell/master/images/Glider.gif" width="300">
<img src="https://raw.githubusercontent.com/alexdbooth/Game-of-Life-Haskell/master/images/Glider_gun.gif" width="300"><img src="https://raw.githubusercontent.com/alexdbooth/Game-of-Life-Haskell/master/images/Pentadecathalon.gif" width="300">
<img src="https://raw.githubusercontent.com/alexdbooth/Game-of-Life-Haskell/master/images/Trippy.gif" width="300">

Alexander Booth, Theo Chitayat, Jackson Mandeville

Conway's Game of Life in Haskell

The famous Game of Life is a cellular automaton simulation created by mathematician John Conway.

The game involves a series of states in which each pixel on a grid is "alive" (filled in) or "dead" (empty) based on the previous state. Using a small set of rules, the user gives an initial state which often creates surprisingly intricate and amazing geometrical patterns. Though the "game" only involves a single initial interaction from the user, it's definitely a lot of fun to play with!

We used the functional language Haskell to recreate the game. We added a few features which show information of interest to the user, such as tracing which pixels have been filled at some point throughout the current simulation and showing the number of alive cells at any given time. A list of all the functions can be found below. Enjoy!

Note that our version of the Game of Life can only be run directly in the Haskell interpreter by commenting out the calls to printFPS and aliveCellCount, due to a font issue.
Instead we have included a compiled version for speed and simplicity. It was compiled on a Debian based Linux system.
In order to compile the code, the Gloss graphics package will need to be installed. 

To compile it use the following command:

	ghc -o game_of_life Game_of_Life.hs -O3

Then run it using:

	./game_of_life


CONTROLS:

	Use the 1,2,3,4,5, and 6 keys to load our premade start states. The 0 key loads an empty scene.
	
	Use the mouse to add new cells to the scene. Clicking in an empty square adds a cell. Clicking in a square with a cell deletes that cell.

	Press spacebar to start or stop the emulation, or press s to step it.

	Press the up or down keys to increase and decrease the emulation speed (use lower FPS for more complex scenes).
	
	Press c to toggle the coloring effects.
