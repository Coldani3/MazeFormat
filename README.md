# Overall structure
Each maze is composed of a set of 2D slices (though each cell can go in any direction outside of 2D), with each cell described in a flag, the size of which is determined by a number at the start of a file (e.g 8 for a byte, 16 for a short, 32 for an int, etc.), henceforth referred to as the cell size. After the flag data type determinator, at the top of the file, the overall size of the maze is described as follows: `{x,y,z,w,etc.}`, so for a 6D maze it would look like `{6,6,6,6,6,6}` - comma separated, no spaces.

# Slices
Each 2D Slice starts with a `[x|y](xO|yO)\z|w|.../`, where x and y determine the size of this slice and xO and yO determine the offset of the maze, notably of the point 0, 0 of the maze. The bit between round brackets can be ommitted if there is no offset. Do note that `x + xO` and `y + yO` must be equal to or less than the x and y size respectively of the overall maze.
The numbers between `\` and `/` describe the slice's coordinates in the other dimensions. If the maze is just 2D, these can be ommitted.

## Data
Slices take the form of a grid of cells. Each cell is represented by a numeric type of the same byte size as the cell size. With a cell size of 8, it is represented by a byte; size 16 and it is by a short; size 32 and by an int; etc. Each two bits represent two walls for each dimension. The first two bits are for the x dimension, next two are for y, next two for z, next two for w, etc. If a bit is on, then that wall exists. There is no comma separation as the cell size is sufficient to describe when one cell starts and another ends. The first cell always describes the one at the maze's 0,0, then increments in the x direction until it eventually loops back and y is increased. As a general rule, the first bit of two describes the wall closest to 0 on that axis. Please also note that even if you do not use every bit they must still exist.

The entrance and exit are defined at the end of the file between two + symbols, with each being inside a set of round brackets. The first coordinates represents the entrance and the second, the exit. e.g+(1,2)(2,1)+ Optionally, you can have a cell description after each coordinate to denote the wall the exit is specifically on - otherwise it will assume the nearest wall or if multiple exist the one nearest 0, 0. If there are no walls, it will treat the entire cell that entrance/exit describes as the entrance/exit. +(1,2|0001)(2,1|0010)+

# Examples
Where "..." represents data about a slice:

```16{6,6,2,2}[6|6]\1|1/...[6|6]\2|1/...[6|6]\1|2/...[6|6]\2|1/...[6|5](0|1)\2|2/```

Please note that the maze data would not be represented in ASCII but instead as raw bits.
```4{2,2}[4|4]1010011010010101```