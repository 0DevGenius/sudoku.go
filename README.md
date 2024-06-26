// # sudoku.go
// Game sudoku , In language go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
	"strings"
)

const N = 9

// printGrid prints the Sudoku grid with an external frame.
func printGrid(grid [N][N]int) {
	fmt.Println("-------------------------")
	for i := 0; i < N; i++ {
		if i%3 == 0 && i != 0 {
			fmt.Println("|-------|-------|-------|")
		}
		for j := 0; j < N; j++ {
			if j%3 == 0 {
				fmt.Print("| ")
			}
			if grid[i][j] == 0 {
				fmt.Print(". ")
			} else {
				fmt.Printf("%d ", grid[i][j])
			}
		}
		fmt.Println("|")
	}
	fmt.Println("-------------------------")
}

// isSafe checks if it's possible to place a number in a given cell.
func isSafe(grid [N][N]int, row, col, num int) bool {
	for x := 0; x < N; x++ {
		if grid[row][x] == num || grid[x][col] == num {
			return false
		}
	}

	startRow, startCol := row-row%3, col-col%3
	for x := 0; x < 3; x++ {
		for y := 0; y < 3; y++ {
			if grid[x+startRow][y+startCol] == num {
				return false
			}
		}
	}
	return true
}

// solveSudoku attempts to solve the Sudoku puzzle using the backtracking algorithm.
func solveSudoku(grid *[N][N]int) bool {
	for row := 0; row < N; row++ {
		for col := 0; col < N; col++ {
			if grid[row][col] == 0 {
				for num := 1; num <= N; num++ {
					if isSafe(*grid, row, col, num) {
						grid[row][col] = num
						if solveSudoku(grid) {
							return true
						}
						grid[row][col] = 0
					}
				}
				return false
			}
		}
	}
	return true
}

// readInput creates a new Sudoku grid based on user input.
func readInput(grid *[N][N]int) {
	reader := bufio.NewReader(os.Stdin)
	fmt.Println("Enter the Sudoku grid (use '0' for empty cells):")
	for i := 0; i < N; i++ {
		fmt.Printf("Row %d (9 numbers): ", i+1)
		input, err := reader.ReadString('\n')
		if err != nil {
			fmt.Println("Error in input: ", err)
			os.Exit(1)
		}
		input = strings.TrimSpace(input)

		if len(input) != N || strings.ContainsAny(input, "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ") {
			fmt.Println("Invalid input: please enter 9 numbers per row, no letters or special characters.")
			i--
			continue
		}

		for j, char := range input {
			number, err := strconv.Atoi(string(char))
			if err != nil || number < 0 || number > 9 {
				fmt.Println("Invalid input: please enter numbers only between 0 and 9.")
				i--
				break
			}
			grid[i][j] = number
		}
	}
}

// newGrid initializes a new grid with zeros.
func newGrid() [N][N]int {
	return [N][N]int{}
}

func main() {
	var grid [N][N]int
	readInput(&grid)
	fmt.Println("\nInput grid:")
	printGrid(grid)

	if solveSudoku(&grid) {
		fmt.Println("\nSolved:")
		printGrid(grid)
	} else {
		fmt.Println("No solution exists for the provided Sudoku puzzle. Try again.")
	}
}
