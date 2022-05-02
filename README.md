
# Overall structure (introduced 2022/05/02)
The maze file starts with one byte that represents the number of dimensions the maze has, this number of dimensions henceforth being referred to as `n`. The next `n` bytes represent the maze's sizes in each dimension, the `n` bytes after that represent the maze's entrance coordinates, and the `n` bytes after that represent the exit ones. Then, the rest of the data's formatting will depend on the dimensions. For less than or equal to 4 dimensions, each cell will be represented with a byte. For less than or equal to 8 dimensions and greater than 4 dimensions, it will be with a short. Generally speaking the data size in bytes follows the formula of `ceil(n / 4)`.

The maze's cell data takes the form of a one dimensional array of cells (the format of which will be described below), a position accessed with the formula `(height * width * depth * w) + (height * width * z) + (height * y) + x`. This means you can just put all of the data in this file directly into an array and not need to worry about formatting it.

## Cells
Each maze cell details the directions available from that cell with bits, two for each axis. A bit set to one (1) will represent an available direction, or the wall being removed. The first of every pair of bits (first being the bit representing 1 in denary, or 00000001) will always be the positive in that direction. The first two bits represent y, then the next two represent x, then z, then w, and so on. In other words, up, down, right, left, forward, backwards, ana and kata, or +y, -y, +x, -x, +z, -z, +w, -w. **TODO: make x first? then it's x,y,z,w]**. So a cell of 0001 would represent a U shape cell in 2D.

----------------------------------------------------------------------------------

# Overall structure (old bad overengineered format)
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
