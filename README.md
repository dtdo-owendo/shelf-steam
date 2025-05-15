Program Specification:
1. Overall structure

The program has a prompt asking for input to tell it what command to execute. It executes the received command until completion before asking for a new command. The loop continues indefinitely, until the user types the built-in command exit, at which point it exits. Once you print the prompt shelf-steam>  to the standard output, use fflush() to make sure your solution prints everything in the expected order.

For reading lines of input, you should use getline(). All command lines used for testing your program will be provided in a single line with at most 255 characters. To parse the input line into constituent pieces, you might want to use strsep(). Read the man page for more details. Commands can be built-in (Section 2) or games in the repository (Section 3).

2. Built-in Commands

Whenever your program accepts a command, it should check whether the command is a built-in command or not. If it is, it should not be executed like the games in the repository. Instead, your program must invoke your implementation of the built-in command. For example, to implement the exit built-in command, you simply call exit(0); in your source code, which then will exit the program.

In this project, you should implement exit, ls, and path as built-in commands.

exit: When the user types exit, your program should simply call the exit system call with 0 as a parameter. It is an error to pass any arguments to exit in the prompt (see Section 5).

path: The path command takes exactly one argument. A typical usage would be like this: shelf-steam> path /new/path/to/games/, which would change the repository path to /new/path/to/games/. The path command always overwrites the old path with the newly specified path. Report an error (see Section 5) if zero or more than one argument to path is given in the prompt. If the provided argument is not a directory, report an error (see Section 5) and do not change the current repository path.
ls: Lists the existing files in the game repository. All files in the repository must be listed in lexicographical order. After the name of the file, you must include the description of the game, which can be obtained by executing the game with the --help switch (e.g. sudoku --help). The description is printed to the standard output. You must redirect the standard output to store this description to a file, so you can later retrieve its content and use it in the ls command. If nothing is printed or the execution attempt fails, use "(empty)" as the description for that file. See the test cases at the bottom for examples and format.
3. Running games

Games are executable files in the folder specified as a command-line argument (or modified via the built-in command path, see Section 3). If the first word in a command is not a built-in command, it should be considered an attempt to run a game. Following the command example at the top, when the user types any non-built-in command (e.g. sudoku), your program must attempt to execute the program /path/to/games/sudoku.

To execute games, look into the fork(), exec(), and wait() system calls. Read the man page for more details. If fork() fails, report an error (see Section 5).

You will note that there are a variety of commands in the exec family; for this project, you must use execvp. Read the man page for more details. You cannot use the system() library function call to run a game. If execvp() is successful, it will not return; if it does return, report an error (see Section 5) because the game either does not exist or does not have the proper permissions set.

Note: You do not have to worry about absolute paths (/bin/sudoku) or relative paths (./tictactoe) in the program name. The location of the programs (e.g. sudoku and tictactoe) will only be specified through the repository path in the test cases.

4. Standard input redirection

Many times, a gamer wants to use a log of moves to "replay" a certain game. Your shelf-steam implementation must support this through the < operator.

For example, if the gamer types tictactoe --seed 0 < /path/to/match1.txt, nothing should be read from the standard input. Instead, the standard input of the program should be rerouted to the file /path/to/match1.txt.

If the redirection file cannot be opened, report as an error (see Section 5) and do not attempt to execute the game.

The exact format of redirection is a command (and possibly some arguments) followed by the redirection operator < followed by a filename. Multiple redirection operators or zero/multiple arguments to the right of the redirection operator must be reported as errors (see Section 5). Do not attempt to execute the game in these cases as well.

Note: don't worry about redirection for built-in commands (e.g., we will not test what happens when you type path /bin < file).

5. Program Errors (in blue in the specification)

The one and only error message. You should print this one and only error message whenever you encounter an error of any type:

    char error_message[30] = "An error has occurred\n";
    write(STDERR_FILENO, error_message, strlen(error_message)); 
The error message should be printed to stderr (standard error), as shown above. Once you print the error message, use fflush() to make sure your solution prints everything in the expected order.

After most errors, your shell simply continue processing after printing the error message. However, if shelf-steam is invoked with an invalid argument (not a directory), it should exit by calling exit(1).

Note: Empty command lines are not errors.

There is a difference between errors that your program catches and those that the games catch. Your program should catch all the syntax errors specified in this project page. If the syntax of the command looks perfect, you simply run the specified command. If there are any game-related errors (e.g., invalid arguments to sudoku when you run it, for example), shelf-steam does not have to worry about that (rather, the program will print its own error messages and exit).

6. Other requirements

Make sure your code is robust to white space of various kinds, including spaces ( ) and tabs (\t). The user should be able to put variable amounts of white space before and after commands, arguments, and various operators; even redirection operators must be separated from other arguments through white spaces.

HOW TO RUN:
$ unzip shelf-steam.zip
$ cd src
$ make
$ if [ -f shelf-steam ]; then echo SUCCESS; fi
SUCCESS
$

Test cases:
$ shelf-steam /path/to/games/bin/
shelf-steam> ls
2048: 2048 is a single-player sliding tile puzzle video game. The objective of the game is to slide numbered tiles on a grid to combine them to create a tile with the number 2048.
sudoku: sudoku is a logic-based, combinatorial number-placement puzzle. The objective is to fill a 9x9 grid with digits so that each column, each row, and each of the nine 3x3 subgrids that compose the grid contains all of the digits from 1 to 9.
tictactoe: tictactoe is a paper-and-pencil game for two players who take turns marking the spaces in a three-by-three grid with X or O. The player who succeeds in placing three of their marks in a horizontal, vertical, or diagonal row first is the winner.
shelf-steam> 
shelf-steam> help
An error has occurred
shelf-steam> sudoku --help
sudoku is a logic-based, combinatorial number-placement puzzle. The objective is to fill a 9x9 grid with digits so that each column, each row, and each of the nine 3x3 subgrids that compose the grid contains all of the digits from 1 to 9.
shelf-steam> exit with arguments
An error has occurred
shelf-steam> exit
$

$ shelf-steam /path/to/games/bin/
shelf-steam> path /usr/
shelf-steam> sudoku --help
An error has occurred
shelf-steam> path /path/to/games/bin/
shelf-steam> sudoku --help
sudoku is a logic-based, combinatorial number-placement puzzle. The objective is to fill a 9x9 grid with digits so that each column, each row, and each of the nine 3x3 subgrids that compose the grid contains all of the digits from 1 to 9.
shelf-steam> exit
$
