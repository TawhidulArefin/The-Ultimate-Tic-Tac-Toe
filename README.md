# The-Ultimate-Tic-Tac-Toe
Trial Version
Source Code:-

#include <bits/stdc++.h>
using namespace std;

class ttt_board {
private:
    char board[3][3];
public:
    static const char NOBODY = ' ', PLAYER1 = 'X', PLAYER2 = 'O';

    ttt_board();
    void init_board();
    void display();
    void set(int x, int y, char c);
    char get(int x, int y) const;
};

ttt_board::ttt_board() {
    init_board();
}

void ttt_board::init_board() {
    for(int x = 0; x < 3; x ++) {
        for(int y = 0; y < 3; y ++) {
            board[x][y] = NOBODY;
        }
    }
}

void ttt_board::display() {
    cout << endl;

    for(int y = 0; y < 3; y ++) {
        for(int x = 0; x < 3; x ++) {
            cout << board[x][y];
            if(x < 2) cout << '|';
        }

        cout << endl;
        if(y < 2) cout << "-+-+-\n";
    }
}

void ttt_board::set(int x, int y, char c) {
    board[x][y] = c;
}

char ttt_board::get(int x, int y) const {
    return board[x][y];
}

class opponent {
private:
    string name;
protected:
    char c;
    void set_name(string name, char c);
public:
    virtual void move(const ttt_board &board, int &xpos, int &ypos) = 0;
    string get_name() { return name; }
};

void opponent::set_name(string name, char c) {
    this->name = name + " (" + c + ")";
    this->c = c;
}

class aiopponent : public opponent {
private:
    int two(char c, char x, char y, char z);
public:
    aiopponent(string name, char c);
    void move(const ttt_board &board, int &xpos, int &ypos);
};

aiopponent::aiopponent(string name, char c) {
    set_name(name, c);
}



int aiopponent::two(char c, char x, char y, char z) {
    if(x == c && y == c && z == ttt_board::NOBODY) return 3;
    if(x == c && y == ttt_board::NOBODY && z == c) return 2;
    if(x == ttt_board::NOBODY && y == c && z == c) return 1;

    return 0;
}

void aiopponent::move(const ttt_board &board, int &xpos, int &ypos) {
    int r;
    char ic;
    if(c == ttt_board::PLAYER1) ic = ttt_board::PLAYER2;
    else ic = ttt_board::PLAYER1;
    for(int x = 0; x < 3; x ++) {
        if((r = two(c, board.get(0, x), board.get(1, x), board.get(2, x)))) {
            ypos = x, xpos = r-1;
            return;
        }

        if((r = two(c, board.get(x, 0), board.get(x, 1), board.get(x, 2)))) {
            xpos = x, ypos = r-1;
            return;
        }
    }

    if((r = two(c, board.get(0, 0), board.get(1, 1), board.get(2, 2)))) {
        xpos = r-1, ypos = r-1;
        return;
    }

    if((r = two(c, board.get(2, 0), board.get(1, 1), board.get(0, 2)))) {
        xpos = 3-r, ypos = r-1;
        return;
    }

    /* Block losing spots */
    for(int x = 0; x < 3; x ++) {
        if((r = two(ic, board.get(0, x), board.get(1, x), board.get(2, x)))) {
            ypos = x, xpos = r-1;
            return;
        }

        if((r = two(ic, board.get(x, 0), board.get(x, 1), board.get(x, 2)))) {
            xpos = x, ypos = r-1;
            return;
        }
    }

    if((r = two(ic, board.get(0, 0), board.get(1, 1), board.get(2, 2)))) {
        xpos = r-1, ypos = r-1;
        return;
    }

    if((r = two(ic, board.get(2, 0), board.get(1, 1), board.get(0, 2)))) {
        xpos = 3-r, ypos = r-1;
        return;
    }

    /* Pick the middle if it's available */
    if(board.get(1, 1) == ttt_board::NOBODY) {
        xpos = 1, ypos = 1;
        return;
    }

    /* Take a corner if we don't have one yet */
    if(board.get(0, 0) != c && board.get(2, 0) != c
        && board.get(0, 2) != c && board.get(2, 2) != c) {

        if(board.get(0, 0) == ttt_board::NOBODY) {
            xpos = 0, ypos = 0;
            return;
        }
        if(board.get(2, 0) == ttt_board::NOBODY) {
            xpos = 2, ypos = 0;
            return;
        }
        if(board.get(0, 2) == ttt_board::NOBODY) {
            xpos = 0, ypos = 2;
            return;
        }
        if(board.get(2, 2) == ttt_board::NOBODY) {
            xpos = 2, ypos = 2;
            return;
        }
    }

    /* Pick the next available spot */
    for(int x = 0; x < 3; x ++) {
        for(int y = 0; y < 3; y ++) {
            if(board.get(x, y) == ttt_board::NOBODY) {
                xpos = x, ypos = y;
                return;
            }
        }
    }
}

class humanopponent : public opponent {
public:
    humanopponent(char c);
    void move(const ttt_board &board, int &xpos, int &ypos);
};

humanopponent::humanopponent(char c) {
    cout << "\nEnter your name: ";
    string name;
    cin >> name;
    set_name(name, c);
}

void humanopponent::move(const ttt_board &board, int &xpos, int &ypos) {
    for(;;) {
        cout << "Enter column (x-axis) number (1-3): ";
        while(!(cin >> xpos) || xpos <= 0 || xpos > 3) {
            // !!!
            cin.clear();
            cout << ": ";
        }

        cout << "Enter row (y-axis) number (1-3): ";
        while(!(cin >> ypos) || ypos <= 0 || ypos > 3) {
            cin.clear();
            cout << ": ";
        }

        xpos --, ypos --;

        if(board.get(xpos, ypos) != ttt_board::NOBODY) {
            cout << "That spot's already taken\n";
            continue;
        }

        break;
    }
}

class ttt {
private:
    ttt_board board;
    opponent *player[2];
    int turn;

public:
    ttt();
    ~ttt();
    bool new_game();
    void play_game();
    int game_done();
};

ttt::ttt() : turn(0) {
    player[0] = player[1] = 0;
}

ttt::~ttt() {
    for(int x = 0; x < 2; x ++) {
        if(player[x]) delete player[x];
    }
}

bool ttt::new_game() {
    cout << "\nNew game\nEnter the type of player #1 ([A]I/[H]uman/[Q]uit): ";

    char one;
    while(1) {
        if(!(cin >> one)) {
            cin.clear();
            continue;
        }

        one = tolower(one);
        if(one == 'q') return false;
        if(one == 'a' || one == 'h') break;
    }

    cout << "Enter the type of player #2 ([A]I/[H]uman/[Q]uit): ";

    char two;
    while(1) {
        if(!(cin >> two)) {
            cin.clear();
            continue;
        }

        two = tolower(two);
        if(two == 'q') return false;
        if(two == 'a' || two == 'h') break;
    }

    if(one == 'h') player[0] = new humanopponent(ttt_board::PLAYER1);
    else {
        player[0] = new aiopponent("AI for player #1", ttt_board::PLAYER1);
    }

    if(two == 'h') player[1] = new humanopponent(ttt_board::PLAYER2);
    else {
        player[1] = new aiopponent("AI for player #2", ttt_board::PLAYER2);
    }

    turn = 0;
    play_game();

    return true;
}

void ttt::play_game() {
    int x, y, won;
    char again;

    do {
        board.init_board();

        while(!(won = game_done())) {
            board.display();
            cout << player[turn]->get_name() << endl;
            player[turn]->move(board, x, y);
            cout << player[turn]->get_name() << " picked position "
                << x+1 << ", " << y+1 << endl;
            board.set(x, y, turn ? ttt_board::PLAYER2 : ttt_board::PLAYER1);
            turn = !turn;
        }

        board.display();
        cout << "Game over. ";

        switch(won) {
        case -1: cout << "Tie."; break;
        case 1: cout << player[0]->get_name() << " won!"; break;
        case 2: cout << player[1]->get_name() << " won!"; break;
        }

        cout << endl;

        cout << "Another round? ";
        cin >> again;
    } while(tolower(again) == 'y');
}

int ttt::game_done() {
    int n, flag = 0;

    for(int x = 0; x < 3 && !flag; x ++) {
        for(int y = 0; y < 3 && !flag; y ++) {
            if(board.get(x, y) == ttt_board::NOBODY) flag = 1;
        }
    }

    if(!flag) return -1;

    for(int x = 0; x < 3; x ++) {
        if((n = board.get(x, 0)) != ttt_board::NOBODY) {
            if(n == board.get(x, 1) && n == board.get(x, 2)) {
                return n == ttt_board::PLAYER1 ? 1 : 2;
            }
        }
    }

    for(int y = 0; y < 3; y ++) {
        if((n = board.get(0, y)) != ttt_board::NOBODY) {
            if(n == board.get(1, y) && n == board.get(2, y)) {
                return n == ttt_board::PLAYER1 ? 1 : 2;
            }
        }
    }

    if((n = board.get(0, 0)) != ttt_board::NOBODY) {
        if(n == board.get(1, 1) && n == board.get(2, 2)) {
            return n == ttt_board::PLAYER1 ? 1 : 2;
        }
    }

    if((n = board.get(2, 0)) != ttt_board::NOBODY) {
        if(n == board.get(1, 1) && n == board.get(0, 2)) {
            return n == ttt_board::PLAYER1 ? 1 : 2;
        }
    }

    return 0;
}

int main()
{
    cout << "\t\tThe Unltimate Tic-Tac-Toe!\n\t\tCreated by: Tawhidul Arefin\n\t\tInspired by: Ali Akbar Akash" << endl;

    ttt tictactoe;

    while(tictactoe.new_game());

    return 0;
}

