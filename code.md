```cpp
/*
 * Sudoku System
 * Mode 1: User enters puzzle, Computer solves it
 * Mode 2: Computer gives puzzle, User plays and solves it
 */

#include <iostream>
#include <vector>
#include <ctime>
#include <stack>
#include <cstdlib>
#include <sstream>

using namespace std;

const int N = 9;

struct Move {
    int row, col, oldVal, newVal;
};

// Verified Solution for game mode
int solution[9][9] = {
    {4,3,5,2,6,9,7,8,1},
    {6,8,2,5,7,1,4,9,3},
    {1,9,7,8,3,4,5,6,2},
    {8,2,6,1,9,5,3,4,7},
    {3,7,4,6,8,2,9,1,5},
    {9,5,1,7,4,3,6,2,8},
    {5,1,9,3,2,6,8,7,4},
    {2,4,8,9,5,7,1,3,6},
    {7,6,3,4,1,8,2,5,9}
};

// Easy puzzle (40 clues)
int puzzleEasy[9][9] = {
    {0,0,0,2,6,0,7,0,1},
    {6,8,0,0,7,0,0,9,0},
    {1,9,0,0,0,4,5,0,0},
    {8,2,0,1,0,0,0,4,0},
    {0,0,4,6,0,2,9,0,0},
    {0,5,0,0,0,3,0,2,8},
    {0,0,9,3,0,0,0,7,4},
    {0,4,0,0,5,0,0,3,6},
    {7,0,3,0,1,8,0,0,0}
};

// Medium puzzle (30 clues)
int puzzleMed[9][9] = {
    {0,0,0,2,0,0,0,0,1},
    {6,0,0,0,7,0,0,0,0},
    {1,0,0,0,0,4,0,0,0},
    {0,2,0,1,0,0,0,0,0},
    {0,0,4,0,0,0,9,0,0},
    {0,0,0,0,0,3,0,2,0},
    {0,0,9,0,0,0,0,7,0},
    {0,4,0,0,5,0,0,0,0},
    {7,0,0,0,0,8,0,0,0}
};

// Hard puzzle (25 clues)
int puzzleHard[9][9] = {
    {0,0,0,2,0,0,0,0,1},
    {6,0,0,0,7,0,0,0,0},
    {1,0,0,0,0,4,0,0,0},
    {0,2,0,1,0,0,0,0,0},
    {0,0,4,6,0,0,9,0,0},
    {0,0,0,0,0,3,0,2,0},
    {0,0,9,0,0,0,0,7,4},
    {0,4,0,0,5,0,0,0,0},
    {7,0,3,0,1,0,0,0,0}
};

// Game variables
int board[9][9];
int original[9][9];
stack<Move> history;
time_t startTime;
string difficulty;

string intToStr(int n) {
    stringstream ss;
    ss << n;
    return ss.str();
}

void printHeader() {
    cout << "\n========================================" << endl;
    cout << "         SUDOKU SYSTEM" << endl;
    cout << "========================================" << endl;
}

void printMenu() {
    cout << "\n-------------- MAIN MENU ---------------" << endl;
    cout << "  1. Enter Puzzle - Computer Solves It" << endl;
    cout << "  2. Play Game - You Solve the Puzzle" << endl;
    cout << "  3. How to Use" << endl;
    cout << "  0. Exit" << endl;
    cout << "----------------------------------------" << endl;
    cout << "Enter choice: ";
}

void copyBoard(int src[9][9], int dest[9][9]) {
    for(int i = 0; i < N; i++)
        for(int j = 0; j < N; j++)
            dest[i][j] = src[i][j];
}

void printGrid(int g[9][9]) {
    cout << "\n    1   2   3   4   5   6   7   8   9" << endl;
    cout << "  +-------+-------+-------+" << endl;
    for(int i = 0; i < N; i++) {
        cout << (i+1) << " | ";
        for(int j = 0; j < N; j++) {
            if(g[i][j] == 0) cout << ". ";
            else cout << g[i][j] << " ";
            if((j+1) % 3 == 0 && j < N-1) cout << "| ";
        }
        cout << "|" << endl;
        if((i+1) % 3 == 0 && i < N-1) {
            cout << "  +-------+-------+-------+" << endl;
        }
    }
    cout << "  +-------+-------+-------+" << endl;
}

// ============== AUTO SOLVER ==============
bool isSafeSolver(int g[9][9], int row, int col, int num) {
    for(int x = 0; x < N; x++) {
        if(g[row][x] == num) return false;
    }
    for(int x = 0; x < N; x++) {
        if(g[x][col] == num) return false;
    }
    int sr = row - row%3, sc = col - col%3;
    for(int i = 0; i < 3; i++) {
        for(int j = 0; j < 3; j++) {
            if(g[sr+i][sc+j] == num) return false;
        }
    }
    return true;
}

bool findEmpty(int g[9][9], int& row, int& col) {
    for(row = 0; row < N; row++) {
        for(col = 0; col < N; col++) {
            if(g[row][col] == 0) return true;
        }
    }
    return false;
}

bool solveBacktrack(int g[9][9]) {
    int row, col;
    if(!findEmpty(g, row, col)) return true;

    for(int num = 1; num <= 9; num++) {
        if(isSafeSolver(g, row, col, num)) {
            g[row][col] = num;
            if(solveBacktrack(g)) return true;
            g[row][col] = 0;
        }
    }
    return false;
}

bool isValidInitial(int g[9][9]) {
    for(int row = 0; row < N; row++) {
        for(int col = 0; col < N; col++) {
            if(g[row][col] != 0) {
                int num = g[row][col];
                for(int x = col + 1; x < N; x++) {
                    if(g[row][x] == num) return false;
                }
                for(int x = row + 1; x < N; x++) {
                    if(g[x][col] == num) return false;
                }
                int sr = row - row%3, sc = col - col%3;
                for(int i = 0; i < 3; i++) {
                    for(int j = 0; j < 3; j++) {
                        int r = sr+i, c = sc+j;
                        if((r != row || c != col) && g[r][c] == num) return false;
                    }
                }
            }
        }
    }
    return true;
}

int countEmpty(int g[9][9]) {
    int c = 0;
    for(int i = 0; i < N; i++)
        for(int j = 0; j < N; j++)
            if(g[i][j] == 0) c++;
    return c;
}

void enterAndSolve() {
    int grid[9][9];

    cout << "\n========================================" << endl;
    cout << "   ENTER YOUR PUZZLE (0 = empty)" << endl;
    cout << "========================================" << endl;
    cout << "Enter 9 rows, 9 numbers each (0 for empty cells):" << endl;
    cout << "Example row: 5 3 0 0 7 0 0 0 0" << endl;
    cout << "----------------------------------------" << endl;

    for(int i = 0; i < N; i++) {
        bool valid = false;
        while(!valid) {
            cout << "Row " << (i+1) << ": ";
            valid = true;
            for(int j = 0; j < N; j++) {
                if(!(cin >> grid[i][j])) {
                    cin.clear();
                    cin.ignore(1000, '\n');
                    cout << "Invalid input! Enter 9 numbers (0-9)." << endl;
                    valid = false;
                    break;
                }
                if(grid[i][j] < 0 || grid[i][j] > 9) {
                    cin.ignore(1000, '\n');
                    cout << "Numbers must be 0-9! Restart row." << endl;
                    valid = false;
                    break;
                }
            }
        }
    }

    cout << "\n--- YOUR PUZZLE ---" << endl;
    printGrid(grid);

    if(!isValidInitial(grid)) {
        cout << "\nERROR: Invalid puzzle! Duplicate numbers found." << endl;
        return;
    }

    cout << "\nEmpty cells: " << countEmpty(grid) << endl;
    cout << "Solving..." << endl;

    if(solveBacktrack(grid)) {
        cout << "\n--- SOLVED PUZZLE ---" << endl;
        printGrid(grid);
        cout << "\nPuzzle solved successfully!" << endl;
    } else {
        cout << "\nNo solution exists for this puzzle." << endl;
    }
}

// ============== GAME MODE ==============
bool isValidMove(int r, int c, int val) {
    if(val < 1 || val > 9) return false;
    for(int j = 0; j < N; j++) {
        if(j != c && board[r][j] == val) return false;
    }
    for(int i = 0; i < N; i++) {
        if(i != r && board[i][c] == val) return false;
    }
    int sr = r - r%3, sc = c - c%3;
    for(int i = 0; i < 3; i++) {
        for(int j = 0; j < 3; j++) {
            if((sr+i != r || sc+j != c) && board[sr+i][sc+j] == val) return false;
        }
    }
    return true;
}

bool isComplete() {
    for(int i = 0; i < N; i++)
        for(int j = 0; j < N; j++)
            if(board[i][j] == 0) return false;
    return true;
}

bool isCorrect() {
    for(int i = 0; i < N; i++)
        for(int j = 0; j < N; j++)
            if(board[i][j] != solution[i][j]) return false;
    return true;
}

int countEmptyGame() {
    int c = 0;
    for(int i = 0; i < N; i++)
        for(int j = 0; j < N; j++)
            if(board[i][j] == 0) c++;
    return c;
}

void showStats() {
    time_t now = time(NULL);
    int elapsed = (int)difftime(now, startTime);
    int mins = elapsed / 60;
    int secs = elapsed % 60;
    cout << "\nDifficulty: " << difficulty 
         << " | Empty: " << countEmptyGame() 
         << " | Time: " << mins << ":";
    if(secs < 10) cout << "0";
    cout << secs << endl;
}

void giveHint() {
    vector<pair<int,int> > empties;
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < N; j++) {
            if(board[i][j] == 0) empties.push_back(make_pair(i,j));
        }
    }
    if(empties.empty()) return;
    int idx = rand() % empties.size();
    int r = empties[idx].first;
    int c = empties[idx].second;
    int val = solution[r][c];

    Move m;
    m.row = r; m.col = c; m.oldVal = 0; m.newVal = val;
    history.push(m);
    board[r][c] = val;

    cout << "\nHint: Cell (" << (r+1) << "," << (c+1) << ") = " << val << endl;
}

void showSolution() {
    cout << "\n--- SOLUTION ---" << endl;
    printGrid(solution);
}

void selectDiff() {
    cout << "\nSelect Difficulty:" << endl;
    cout << "  1. Easy   (40 clues)" << endl;
    cout << "  2. Medium (30 clues)" << endl;
    cout << "  3. Hard   (25 clues)" << endl;
    cout << "Choice: ";

    int ch;
    cin >> ch;

    if(ch == 1) {
        copyBoard(puzzleEasy, board);
        copyBoard(puzzleEasy, original);
        difficulty = "Easy";
    } else if(ch == 2) {
        copyBoard(puzzleMed, board);
        copyBoard(puzzleMed, original);
        difficulty = "Medium";
    } else {
        copyBoard(puzzleHard, board);
        copyBoard(puzzleHard, original);
        difficulty = "Hard";
    }
}

void playGame() {
    while(!history.empty()) history.pop();
    startTime = time(NULL);

    bool playing = true;
    while(playing) {
        cout << "\n========================================" << endl;
        cout << "      SUDOKU GAME" << endl;
        cout << "========================================" << endl;
        showStats();
        printGrid(board);

        cout << "\nCommands:" << endl;
        cout << "  M = Make Move (enter row, col, value)" << endl;
        cout << "  U = Undo" << endl;
        cout << "  H = Hint" << endl;
        cout << "  S = Show Solution" << endl;
        cout << "  Q = Quit Game" << endl;
        cout << "Enter command: ";

        char cmd;
        cin >> cmd;
        cmd = (char)tolower((unsigned char)cmd);

        if(cmd == 'q') {
            playing = false;
            continue;
        }

        if(cmd == 'u') {
            if(history.empty()) {
                cout << "Nothing to undo." << endl;
            } else {
                Move m = history.top();
                history.pop();
                board[m.row][m.col] = m.oldVal;
                cout << "Undo: (" << (m.row+1) << "," << (m.col+1) << ")" << endl;
            }
            continue;
        }

        if(cmd == 'h') {
            giveHint();
            continue;
        }

        if(cmd == 's') {
            showSolution();
            cout << "\nGame ended." << endl;
            playing = false;
            continue;
        }

        if(cmd == 'm') {
            int r, c, v;
            cout << "Row (1-9): ";
            cin >> r;
            if(r < 1 || r > 9) {
                cout << "Invalid row." << endl;
                continue;
            }
            cout << "Col (1-9): ";
            cin >> c;
            if(c < 1 || c > 9) {
                cout << "Invalid col." << endl;
                continue;
            }

            if(original[r-1][c-1] != 0) {
                cout << "Cannot change original clue." << endl;
                continue;
            }

            cout << "Value (1-9) or 0 to clear: ";
            cin >> v;
            if(v < 0 || v > 9) {
                cout << "Invalid value." << endl;
                continue;
            }

            if(v != 0 && !isValidMove(r-1, c-1, v)) {
                cout << "Invalid move! Violates Sudoku rules." << endl;
                continue;
            }

            Move m;
            m.row = r-1; m.col = c-1; 
            m.oldVal = board[r-1][c-1]; 
            m.newVal = v;
            history.push(m);
            board[r-1][c-1] = v;

            if(isComplete()) {
                printGrid(board);
                if(isCorrect()) {
                    time_t now = time(NULL);
                    int elapsed = (int)difftime(now, startTime);
                    cout << "\n*** CONGRATULATIONS! Puzzle solved! ***" << endl;
                    cout << "Time: " << elapsed << " seconds" << endl;
                } else {
                    cout << "\nBoard full but contains errors." << endl;
                }
                playing = false;
            }
        }
    }
}

void playMode() {
    selectDiff();
    playGame();
}

void howToUse() {
    cout << "\n========================================" << endl;
    cout << "         HOW TO USE" << endl;
    cout << "========================================" << endl;
    cout << "\nMode 1: Enter Puzzle - Computer Solves" << endl;
    cout << "  You type your own Sudoku puzzle." << endl;
    cout << "  Use 0 for empty cells." << endl;
    cout << "  Computer will solve it for you." << endl;
    cout << "\nMode 2: Play Game - You Solve" << endl;
    cout << "  Computer gives you a puzzle." << endl;
    cout << "  You fill in the empty cells." << endl;
    cout << "\nGame Controls:" << endl;
    cout << "  M = Enter your move (row, col, value)" << endl;
    cout << "  U = Undo last move" << endl;
    cout << "  H = Get a hint" << endl;
    cout << "  S = Give up and see solution" << endl;
    cout << "  Q = Quit current game" << endl;
    cout << "========================================" << endl;
}

int main() {
    srand((unsigned int)time(NULL));
    int choice = 0;
    bool run = true;

    while(run) {
        printHeader();
        printMenu();
        cin >> choice;

        switch(choice) {
            case 1:
                enterAndSolve();
                break;
            case 2:
                playMode();
                break;
            case 3:
                howToUse();
                break;
            case 0:
                cout << "\nExiting..." << endl;
                run = false;
                break;
            default:
                cout << "\nInvalid choice!" << endl;
        }
    }

    return 0;
}
```
