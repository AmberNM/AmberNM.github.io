---
layout: post
title: Computational Methods and Software (CS 417)
categories: [C++,OOP]
---

## Decimal-to-Binary C++

Decimal-to-Binary will take the absolute value of a decimal (base 10) number and convert it into binary (base 2). Written in **C++**.

### Requirements

  * g++ v5.4 or newer
  * Make


### Compilation

The code can be compiled with the provided makefile using the standard `make` command.

If compiling the code manually, or integrating into a larger program, include the following flags:

```
FLAGS=-std=c++11 -fsanitize=address -fuse-ld=gold -Wall -MMD
```

Note that flag `-fuse-ld=gold` is only required on certain Ubuntu systems due to a known bug with g++ 5.x.


### Sample Execution & Output

To be run without command line arguments, using

```
./binary
```
the program will ask for a decimal number to be input from the keyboard (can be positive or negative). After the user inputs a number, output *simliar* to

```
   Please enter a decimal (base 10): 2.44
 |----------------------------------------------------|
 |   Step |         Buffer              |    Bit      |
 |----------------------------------------------------|
 |      1 |          2 / 2 = 0          |      0      |
 |      2 |          1 / 2 = 0          |      1      |
 |      3 | 0.44000006 * 2 = 0.88000011 |      0      |
 |      4 | 0.88000011 * 2 = 1.76000023 |      1      |
 |      5 | 0.76000023 * 2 = 1.52000046 |      1      |
 |      6 | 0.52000046 * 2 = 1.04000092 |      1      |
 |      7 | 0.04000092 * 2 = 0.08000183 |      0      |
 |      8 | 0.08000183 * 2 = 0.16000366 |      0      |
 |      9 | 0.16000366 * 2 = 0.32000732 |      0      |
 |     10 | 0.32000732 * 2 = 0.64001465 |      0      |
 |----------------------------------------------------|

| 2.44000006 | in binary is 10.01110000
```

will  be displayed. Note that the precision estimates will vary by architecture/system.

## Decimal-to-Base C++

Decimal-to-Base will take the absolute value of a decimal (base 10) number and convert it into a base of your choosing (e.g. base 4). Written in **C++**.

### Requirements

  * g++ v5.4 or newer
  * Make


### Compilation

The code can be compiled with the provided makefile using the standard `make` command.

If compiling the code manually, or integrating into a larger program, include the following flags:

```
FLAGS=-std=c++11 -fsanitize=address -fuse-ld=gold -Wall -MMD
```

Note that flag `-fuse-ld=gold` is only required on certain Ubuntu systems due to a known bug with g++ 5.x.


### Sample Execution & Output

If program is run without command line arguments, using

```
./convertBase
```
the following message will be displayed.

```
Not enough data.
Usage: ./convertBase <base> <number>
```

If run using
```
./convertBase 11 1935.65
```

output *similar* to

```
 |-----------------------------------------------------|
 |   Step |         Buffer               |    Bit      |
 |-----------------------------------------------------|
 |      1 |       1935 / 11 = 175        |      A      |
 |      2 |        175 / 11 = 15         |      A      |
 |      3 |         15 / 11 = 0          |      4      |
 |      4 |          1 / 11 = 0          |      1      |
 |      5 | 0.65002441 * 11 = 7.15026855 |      7      |
 |      6 | 0.15026855 * 11 = 1.65295410 |      1      |
 |      7 | 0.65295410 * 11 = 7.18249512 |      7      |
 |      8 | 0.18249512 * 11 = 2.00744629 |      2      |
 |      9 | 0.00744629 * 11 = 0.08190918 |      0      |
 |     10 | 0.08190918 * 11 = 0.90100098 |      0      |
 |     11 | 0.90100098 * 11 = 9.91101074 |      9      |
 |     12 | 0.91101074 * 11 = 10.02111816|      A      |
 |-----------------------------------------------------|

| 1935.65000000 | in base 11 is 14AA.7172009A
```

will  be displayed. Note that the precision estimates will vary by architecture/system.

## Matrix Solver

**Language:** C++

### My Pseudocode

//Iterate through all rows
for every i in 0 to n-1 --> for i = 0, i < rows.size()-1, i++
{
	//Pivot()
	int findMaxColumn()
	{
		int idx;

		for (iterate through rows) i = 0, i < rows.size(), i++
			compare row[i][0]
		ind++;

		return idx;
	}

	//Swap()
	row[i] = row(indx) 

	//Scale()
	multiply(int num)
	{
		for (iterate through rows) i = 0, i < rows.size(), i++
			row[i] = row[i] / num
	}

	//Eliminate
	eliminate()
	{	
		for ( i = 1, i != rows.size(), i++ )
			row[i] = row[i] - row[i-1];

	}

}

//BackSolve
backSolve()
{
	int augmented = first row size;
    int lastRow = rows.size()-1;

    for (int i = lastRow; i != 0; i--)
    {
        for (int j = 0; j != lastRow; j++)
        {
            int s = rows[j][i];

            rows[j][i] = rows[j][i] - (s * rows[i][i]);
            rows[j][augmented] = rows[j][augmented] - (s * rows[i][augmented]);
        }
    }
}


### Requirements

  * g++ v5.4 or newer
  * Make


### Compilation & Execution Instructions

The code can be compiled with the provided makefile using the standard `make` command.

If compiling the code manually, or integrating into a larger program, include the following flags:

```
FLAGS=-std=c++11 -fsanitize=address -fuse-ld=gold -Wall -MMD
```

Note that flag `-fuse-ld=gold` is only required on certain Ubuntu systems due to a known bug with g++ 5.x.


### Sample Execution & Output

If program is run without command line arguments, using

```
./matrixSolver
```
the following message will be displayed.

```
Not enough data.
Usage: ./matrixSolver <filename>
```

If run using
```
./matrixSolver test0.txt
```

output *similar* to

```
 Original Matrix: 
[ 1 2 1 ]
[ 2 2 4 ]


------------------------------------
Iteration: 1

Swap row 1 with row 0.
[ 2 2 4 ]
[ 1 2 1 ]

Scaled Matrix: 
[ 1 1 2 ]
[ 1 2 1 ]

Subtracted Matrix: 
[ 1 1 2 ]
[ 0 1 -1 ]

------------------------------------
Iteration: 2

Swap row 1 with row 1.
[ 1 1 2 ]
[ 0 1 -1 ]

Scaled Matrix: 
[ 1 1 2 ]
[ 0 1 -1 ]

Subtracted Matrix: 
[ 1 1 2 ]
[ 0 1 -1 ]

------------------------------------
Solved Matrix: 
[ 1 0 2 ]
[ 0 1 -1 ]
```

will  be displayed. Note that the precision estimates will vary by architecture/system.

## Lin-Reg Approximation C++

Lin-Reg Approximation will take an input file of CPU core temp values and perform both a linear piecewise interpolation as well as a linear approximation. Written in **C++**.

### Requirements

  * g++ v5.4 or newer
  * Make


### Compilation

The code can be compiled with the provided makefile using the standard `make` command.

If compiling the code manually, or integrating into a larger program, include the following flags:

```
FLAGS=-std=c++11 -fsanitize=address -fuse-ld=gold -Wall -MMD
```

Note that flag `-fuse-ld=gold` is only required on certain Ubuntu systems due to a known bug with g++ 5.x.


### Sample Execution & Output

If program is run without command line arguments, using

```
./LinReg
```
the following message will be displayed.

```
Not enough data.
Usage: ./LinReg <filename>
```

If run using
```
./LinReg sensors1.txt
```

output *similar* to

```
61 <= x <= 80; y(0) = 61 + 0.633333x ; interpolation
80 <= x <= 62; y(30) = 98 + -0.6x ; interpolation
62 <= x <= 83; y(60) = 20 + 0.7x ; interpolation
...
```

will  be provided in an accompanying .txt file labeled: `Piecewise-Interpolation-core{#}.txt`. Note that the precision estimates will vary by architecture/system.