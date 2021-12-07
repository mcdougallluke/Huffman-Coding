# CS 241 Data Organization using C

# Lab 9: Huffman Coding

### Brooke Chenoweth

### Fall 2021

## Project Overview

For this assignment, you will write two programs to encode and decode files using Huffman
Coding.

### huffencode

The program inhuffencode.ctakes two command line arguments. The first is the name
of the file to encode and the second is the name of the file where the encoded data will be
written. Exit with an error if either of the files are unable to be opened.
In addition to writing the encoded file, print a table of byte frequencies and huffman codes
to standard output. Separate columns with a tab characters. Visible ASCII characters (33-
126) are printed as characters. Values outside that range are displayed as equals sign followed
by integer value of the byte.
For example, if the filesmall.txtcontains the text:a small sample string(with no
newline at the end of the file), the command

./huffencode small.txt small.huff

will result in the following being printed to standard out.

Symbol Freq Code
=32 3 011
a 3 100
e 1 11110
g 1 11111
i 1 0000
l 3 101
m 2 001
n 1 0001
p 1 0100
r 1 0101
s 3 110
t 1 1110
Total chars = 21


### huffdecode

The program inhuffdecode.ctakes two command line arguments. The first is the name
of the file to decode and the second is the name of the file where the decoded data will be
written. Exit with an error if either of the files are unable to be opened.

### huffman.c andhuffman.h

Obviously, there is a lot of common Huffman code functionality needed for both encoding
and decoding. Place this inhuffman.cand expose the necessary declarations inhuffman.h.
If you want to break down your code among additional files, feel free to do so, but make sure
that you update theMakefileaccordingly.

## Huffman Tree Algorithm

- Count frequency of symbols in a stream.
- Create leaf nodes from symbol frequency data. (Don’t include symbols with zero
    occurances.)
- Add all nodes to priority queue.
- While queue has more than one item:
    - Remove two trees from queue.
    - Create new tree with those two as children. (First removed should be left child.)
       Tree frequency count is sum of children’s counts.
    - Add new tree to priority queue.
- Use final Huffman tree to generate codes.
- Huffman code for symbol at leaf is the path from root to leaf, with ‘0’ for each left
    branch and ‘1’ for each right branch.

### Huffman Tree Node

- All nodes have a frequency count –unsigned long
- Leaf nodes have a symbol –unsigned char
- Internal nodes have references to two subtrees.
- You may want some additional bookkeeping fields, depending on your exact implemen-
    tation. (isLeaf, parentNode, etc.)


### Comparing Huffman Trees

The exact code generated depends on the order of the trees in the priority queue. For this
project, we’ll compare trees in the following manner.

- First, compare frequency values for the trees. The tree with the lower frequency has
    higher priority.
- If the two trees have the same frequency, compare the symbols in the leftmost leaf
    nodes of the two trees. Smaller value has higher priority.

### Reconstructing Tree from Codes

If you have the weights for the symbols, it is easy enough to reconstruct the tree, but it is
still possible to do so if you only have the symbols and their codes instead.

- Begin with an empty tree.
- For each symbol with its code:
    - Follow the path through the tree specified by the code (go left for 0, right for 1)
    - If there is no node in the desired direction, create and add one to the tree before
       going that way.
    - At the end of the code/path, you’ll be at a newly created leaf node. Save the
       symbol there. (For ease of implementation, you might choose to add the symbol
       to every node you create while traversing this path. That shouldn’t hurt anything,
       since the symbols only matter at the leaves.)
- Once all the symbols and codes have been processed, you now have a tree with the
    same structure that was used to generate the codes in the first place.

## Encoded File Format

Binary files written on one system may not match file written by same program on another
system, but a file encoded withhuffencodeshould be able to be decoded withhuffdecode
if they are running on the same system.

- Number of symbols in table –unsigned char
    - 256 will not fit in an unsigned char, so write a zero in that case.
    - We’ll assume there never are zero symbols.
- Symbol and code information
    - Symbol –unsigned char
    - Number of bits in the code –unsigned char


- Bits of the code
    The first bit of the code with be in the most significant bit of the first byte. The
    number of bytes for the code will depend on the length of the code. The last byte
    should be padded with zeros if code length is not a multiple of eight.
- Total number of encoded symbols –unsigned long
- Bits of encoded data. Last byte should be padded with zeros if code string did not end
on byte boundary.
The first bit of the encoded stream should be in the most significant bit of the first
byte.
data a space s m a l l space...
codes 100 011 110 001 100 101 101 011...
code stream 100011110001100101101011...
bytes 10001111 00011001 01101011...
hex 8F 19 6B...

## Turning in your assignment

Make a single zip file containinghuffencode.c,huffdecode.c,huffman.h,huffman.c, and
Makefileand submit it to the Huffman Coding assignment in UNM Learn. If you divided
your program into additional files, include them all in the zip file and make sure that your
makefile is updated to compile them.

## Grading Rubric: 100 points

-5 point : The programs do not start with a comment stating the students first and last
name and/or the source files are not named correctly.

-5 points: Programs compile with warnings on moons.cs.unm.edu using/usr/bin/gccwith
the-Wall -ansi -pedanticoptions (using the makefile)

-5 points: Programs leak memory (tested with valgrind)

-5 points: Using non-standard types such asint128. If your data doesn’t fit in along,
you’ll have to figure out some other way of dealing with it.

-5 points: Program takes more than 1 minute to encode or decode any individual test file.
(I don’t actually expect any test to take more than a second, but this puts a limit as
to how long we are willing to make the graders wait.)

15 points:Follows CS-241 Coding Standard: including quality, quantity and neatness of
comments, no dead code, and Best Practices (functions not being too long, nestings
not needlessly deep, and avoidance of duplicate code).


40 points:Frequency and code table printed out byhuffencodematches expected output
for each ofmississippi.txt,sense.html,ugly.txt,MobyDick.txt, andralph.bmp.
(8 points each)

20 points:Encoded file produced byhuffencodepasses binary diff with encoded versions
of above files. (4 points each)

20 points:Decoded file produced by huffdecodewith encoded versions of above files
matches binary diff with original files. (4 points each)

5 points: Decoded file produced byhuffdecodewithevil.jpg.huffmatches binary diff
with original file.


