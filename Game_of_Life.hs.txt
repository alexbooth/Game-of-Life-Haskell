{-
Copyright (c) 2016 Alexander Booth, Jackson Mandeville, Theo Chitayat

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
-}

-- Gloss is the graphics package
import Graphics.Gloss
import Graphics.Gloss.Interface.Pure.Game

main = play
           window       -- window
           white        -- background color
           60           -- frames/second
           initialState -- starting state of the board
           render       -- function that turns the current state into an image
           handleKeys   -- handle key events
           update       -- updates the state of the world

-- window width and height in pixels
width = 600
height = 600

-- defines the window of the canvas
window = InWindow "Conway's Game of Life" (width, height) (0,0)

-- there are x*y cells in the entire grid
allCells = [(a,b) | a <- [0..(x-1)], b <- [0..(y-1)]]

inBounds (a,b)
	| a >= 0 && b >= 0 && a < x && b < y = True
	| otherwise = False

-- gives all 8 neighbors of the cell at (x0, y0)
neighbors (x0,y0) = [boundaryMap (a,b) | a <- [(x0-1)..(x0+1)], b <- [(y0-1)..(y0+1)], (a,b) /= (x0,y0)]

boundaryMap (a,b) = ((boundaryDigit a),(boundaryDigit b))

boundaryDigit a
	| a > (x-1) = 0
	| a < 0 = (x-1)
	| otherwise = a


numAliveNeighbors alive (a,b) = length (filter alive (neighbors (a,b)))

-- determines whether the cell at (a,b) will be alive in the next state
nextAlive alive (a,b)
	| numAliveNeighbors alive (a,b) == 3 = True
	| alive (a,b) && numAliveNeighbors alive (a,b) == 2 = True
	| otherwise = False

step state deltatime = state {
	aliveCells = filter (nextAlive (\n -> elem n (aliveCells state))) allCells,
	time = (time state) + deltatime - (1/fromIntegral (fps state)),
	visitedCells = if (isColored state) then (visitedCells state)++[(a,b) | (a,b) <- (aliveCells state), notElem (a,b) (visitedCells state)] else []}
	

-- Description of a state of the world
data LifeState = Game
    { isRunning :: Bool,
    isColored :: Bool,
    visitedCells :: [(Float,Float)],
    aliveCells :: [(Float,Float)],
    time :: Float,
    fps :: Int	}

	  
	  
-- Initial state of the board
initialState = Game
    { isRunning = False,
    isColored = True,
    visitedCells = [(1,1),(2,2),(0,3),(1,3),(2,3)],
    aliveCells = [(1,1),(2,2),(0,3),(1,3),(2,3)],
    time = 0.0,
    fps = 20	}
    
-- prints the Alive Cell Count on top of the grid	
aliveCellCount state
	= Translate (-150) (260) -- shift the text to the middle of the window
	$ Scale 0.25 0.25		 -- display it half the original size
	$ Text ("Alive Cell Count: " ++ show (length(state)))	-- text to display

-- prints the FPS below the grid		
printFps fps
	= Translate (-70) (-280) -- shift the text to the middle of the window
	$ Scale 0.25 0.25		 -- display it half the original size
	$ Text ("FPS: " ++ show fps)	-- text to display
    

-- Draws the current state of the board as a picture
render state = pictures $ visistedPictures ++ grid ++ aliveCellsPictures -- ++ [(aliveCellCount (aliveCells state))] ++ [printFps (fps state)] -- will draw this list of picture types
    where aliveCellsPictures = [drawCell (a,b) | (a,b) <- aliveCells state]
          visistedPictures = if (isColored state) then [color green $ drawCell (a,b) | (a,b) <- visitedCells state] else []


-- Filled in cells look like this
square = rectangleSolid (w/x) (h/y)

-- Defines a grid space with padding
padding = 100
w = (fromIntegral width) - padding
h = (fromIntegral height) - padding

-- x, y cells
x = 50
y = 50

-- defines the grid and the size of each cell
grid = verticalLines ++ horizontalLines ++ [rectangleWire w h]
    where verticalLines = foldr (\a -> \b -> vLine a:b) [] [0..x] 
          vLine a = color  (greyN 0.5)  (line [ (w/x*a-w/2, -h/2), (w/x*a-w/2, h-h/2) ])
          horizontalLines = foldr (\a -> \b -> hLine a:b) [] [0..y] 
          hLine a = color  (greyN 0.5)  (line [ (-w/2, h/y*a-h/2), (w-w/2, h/y*a-h/2) ])
		  
drawCell (x0,y0) =  translate (x0*w/x -w/2 +  w/x/2) (-y0*h/y +h/2 -h/y/2) square

-- pressing the Up or Down arrow keys invoke changeFPS
changeFps current change
    | current + change >= 60 = 60
    | current + change <= 1 = 1
    | otherwise = current + change

-- Loads the glider state
handleKeys (EventKey (Char '1') Down _ _) state = state { isRunning = False, aliveCells = [(1,1),(2,2),(0,3),(1,3),(2,3)], visitedCells = [(1,1),(2,2),(0,3),(1,3),(2,3)] }

-- Loads the pentadecathalon state
handleKeys (EventKey (Char '2') Down _ _) state = state { isRunning = False, aliveCells = [(25,20),(25,21),(24,22),(26,22),(25,23),(25,24),(25,25),(25,26),(24,27),(26,27),(25,28),(25,29)], visitedCells = [(25,20),(25,21),(24,22),(26,22),(25,23),(25,24),(25,25),(25,26),(24,27),(26,27),(25,28),(25,29)] }

-- Loads the glider gun state
handleKeys (EventKey (Char '3') Down _ _) state = state { isRunning = False, aliveCells = [(2,6),(2,7),(3,6),(3,7),(12,6),(12,7),(12,8),(13,5),(13,9),(14,4),(14,10),(15,4),(15,10),(16,7),(17,5),(17,9),(18,6),(18,7),(18,8),(19,7),(22,4),(22,5),(22,6),(23,4),(23,5),(23,6),(24,3),(24,7),(26,2),(26,3),(26,7),(26,8),(36,4),(36,5),(37,4),(37,5)], visitedCells = [(2,6),(2,7),(3,6),(3,7),(12,6),(12,7),(12,8),(13,5),(13,9),(14,4),(14,10),(15,4),(15,10),(16,7),(17,5),(17,9),(18,6),(18,7),(18,8),(19,7),(22,4),(22,5),(22,6),(23,4),(23,5),(23,6),(24,3),(24,7),(26,2),(26,3),(26,7),(26,8),(36,4),(36,5),(37,4),(37,5)] }

-- Loads a custom explosion state
handleKeys (EventKey (Char '4') Down _ _) state = state { isRunning = False, aliveCells = [(25,19),(25,20),(25,21),(23,22),(24,22),(26,22),(27,22),(25,23),(25,24),(25,25),(25,26),(23,27),(24,27),(26,27),(27,27),(25,28),(25,29),(25,30)], visitedCells = [(25,19),(25,20),(25,21),(23,22),(24,22),(26,22),(27,22),(25,23),(25,24),(25,25),(25,26),(23,27),(24,27),(26,27),(27,27),(25,28),(25,29),(25,30)] }

-- Loads another custom explosion state that ends with a stable pattern
handleKeys (EventKey (Char '5') Down _ _) state = state { isRunning = False, aliveCells = [(25,16),(25,17),(25,18),(25,19),(25,20),(25,21),(23,22),(24,22),(26,22),(27,22),(25,23),(25,24),(25,25),(25,26),(23,27),(24,27),(26,27),(27,27),(25,28),(25,29),(25,30),(25,31),(25,32),(25,33)], visitedCells = [(25,16),(25,17),(25,18),(25,19),(25,20),(25,21),(23,22),(24,22),(26,22),(27,22),(25,23),(25,24),(25,25),(25,26),(23,27),(24,27),(26,27),(27,27),(25,28),(25,29),(25,30),(25,31),(25,32),(25,33)] }

-- Loads the spaceship state
handleKeys (EventKey (Char '6') Down _ _) state = state { isRunning = False, aliveCells = [(24,17),(24,19),(25,20),(26,20),(27,20),(28,20),(28,19),(28,18),(27,17)], visitedCells = [(24,17),(24,19),(25,20),(26,20),(27,20),(28,20),(28,19),(28,18),(27,17)] }

-- Loads a blank state
handleKeys (EventKey (Char '0') Down _ _) state = state { isRunning = False, aliveCells = [], visitedCells = [] }

-- steps to the next state when 's' is pressed
handleKeys (EventKey (Char 's') Down _ _) state = step state 0

-- pauses or resumes animation
handleKeys (EventKey (SpecialKey KeySpace) Down _ _) state = state {isRunning = if (isRunning state) then False else True}

-- pauses or resumes animation
handleKeys (EventKey (Char 'c') Down _ _) state = state {isColored = if (isColored state) then False else True}

-- draws a new cell at mouse location or erases present cell
handleKeys (EventKey (MouseButton LeftButton) Down _ (xPos, yPos)) state = state { aliveCells = if elem (xCoord xPos, yCoord yPos) (aliveCells state) then filter (\x -> x /= ( xCoord xPos, yCoord yPos )) (aliveCells state) else (aliveCells state)++[( xCoord xPos, yCoord yPos )] }

-- Changes the FPS to better render complex states
handleKeys (EventKey (SpecialKey KeyUp) Down _ _) state = state { fps = changeFps (fps state) 5 }

handleKeys (EventKey (SpecialKey KeyDown) Down _ _) state = state { fps = changeFps (fps state) (-5) }

-- Do nothing for all other events (crashes if this isn't here for some reason)
handleKeys _ state = state

xCoord pos =  fromIntegral $ div (mod (floor $ pos - (w/2))  (floor w)) (floor (w/x))
yCoord pos =  (y-1) - (fromIntegral $ div (mod (floor $ pos + (h/2))  (floor h)) (floor (h/y)))


-- called every 'fps' times per second, time since last frame is deltatime
update deltatime state
	| (isRunning state) && (time state) >= 1/fromIntegral(fps state) = step state deltatime
	| (isRunning state) = state { time = (time state) + deltatime }
	| otherwise = state
