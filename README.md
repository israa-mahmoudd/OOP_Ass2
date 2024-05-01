#include <iostream>
#include <string>
#include <fstream>
#include <limits>
#include <vector>
#include <algorithm>

using namespace std;

class Player {
private:
    string name;
    int yearOfBirth;
    int height;
    int internationalGoals;
    string nationality;

public:
    Player() {} // Default constructor

    Player(string n, int year, int h, int Goals, string nationality)
        : name(n), yearOfBirth(year), height(h), internationalGoals(Goals), nationality(nationality) {}

    void setName(string n) {
        name = n;
    }

    void setYearOfBirth(int y) {
        yearOfBirth = y;
    }

    void setHeight(int h) {
        height = h;
    }

    void setInternationalGoals(int g) {
        internationalGoals = g;
    }

    void setNationality(string Nationality) {
        nationality = Nationality;
    }


    string getName() const {
        return name;
    }

    int getYearOfBirth() const {
        return yearOfBirth;
    }

    int getHeight() const {
        return height;
    }

    int getInternationalGoals() const {
        return internationalGoals;
    }

    string getNationality() const {
        return nationality;
    }
};

class NationalTeam {
private:
    string country;
    Player* players[18] = {nullptr}; // Array of pointers to Player objects
    bool active[18] = {false};

public:
    NationalTeam(string country) : country(move(country)) {}

    ~NationalTeam() {
        for (int i = 0; i < 18; ++i) {
            delete players[i];
        }
    }

    bool addPlayer(const Player& player) {
        for (int i = 0; i < 18; ++i) {
            if (!active[i]) {
                players[i] = new Player(player); // Allocate new Player object
                active[i] = true;
                return true;
            }
        }
        return false; // No empty slot found
    }

    bool removePlayer(string playerName) {
        for (int i = 0; i < 18; ++i) {
            if (active[i] && players[i]->getName() == playerName) {
                delete players[i]; // Deallocate Player object
                players[i] = nullptr;
                active[i] = false;
                return true;
            }
        }
        return false; // Player not found
    }

    int getNumOfPlayers() const {
        int count = 0;
        for (int i = 0; i < 18; ++i) {
            if (active[i]) {
                count++;
            }
        }
        return count;
    }

    double AvgHeight() const {
        int totalHeight = 0;
        int numOfPlayers = getNumOfPlayers();
        for (int i = 0; i < 18; ++i) {
            if (active[i]) {
                totalHeight += players[i]->getHeight();
            }
        }
        return static_cast<double>(totalHeight) / numOfPlayers;
    }

    Player OldestPlayer() const {
        Player oldest;
        bool first = true;
        for (int i = 0; i < 18; ++i) {
            if (active[i]) {
                if (first || players[i]->getYearOfBirth() < oldest.getYearOfBirth()) {
                    oldest = *(players[i]);
                    first = false;
                }
            }
        }
        return oldest;
    }

    Player YoungestPlayer() const {
        Player youngest;
        bool first = true;
        for (int i = 0; i < 18; ++i) {
            if (active[i]) {
                if (first || players[i]->getYearOfBirth() > youngest.getYearOfBirth()) {
                    youngest = *(players[i]);
                    first = false;
                }
            }
        }
        return youngest;
    }

    double AvgIntGoals() const {
        int totalGoals = 0;
        int numOfPlayers = getNumOfPlayers();
        for (int i = 0; i < 18; ++i) {
            if (active[i]) {
                totalGoals += players[i]->getInternationalGoals();
            }
        }
        return static_cast<double>(totalGoals) / numOfPlayers;
    }

    Player highestIntGoals() const {
        Player highest;
        bool first = true;
        for (int i = 0; i < 18; ++i) {
            if (active[i]) {
                if (first || players[i]->getInternationalGoals() > highest.getInternationalGoals()) {
                    highest = *(players[i]);
                    first = false;
                }
            }
        }
        return highest;
    }

   friend ostream& operator<<(ostream& os, const NationalTeam& team) {
    os << "Country: " << team.country << endl;
    for (int i = 0; i < 18; ++i) {
        if (team.active[i]) {
            const Player* player = team.players[i];
            os << "Player Name: " << player->getName() << ", Year of Birth: " << player->getYearOfBirth() 
               << ", Height: " << player->getHeight() << " cm, International Goals: " << player->getInternationalGoals()
               << ", Nationality: " << player->getNationality() << endl;
        }
    }
    return os;
}


    void writeStatistics(ofstream& outFile) const {
        outFile << "Team Country: " << country << endl;
        outFile << "Number of players: " << getNumOfPlayers() << endl;
        outFile << "Average height of players: " << AvgHeight() << " cm" << endl;
        Player oldest = OldestPlayer();
        outFile << "Name and age of oldest player: " << oldest.getName() << " (" << (2024 - oldest.getYearOfBirth()) << " years)" << endl;
        Player youngest = YoungestPlayer();
        outFile << "Name and age of youngest player: " << youngest.getName() << " (" << (2024 - youngest.getYearOfBirth()) << " years)" << endl;
        outFile << "Average number of international goals of players: " << AvgIntGoals() << endl;
        Player highestGoals = highestIntGoals();
        outFile << "Player with the highest international goals: " << highestGoals.getName() << " (" << highestGoals.getInternationalGoals() << " goals)" << endl;
    }

vector<Player> readPlayersFromFile(ifstream& read) {
        vector<Player> players;
        int numPlayers;
        if (read.is_open()) {
            read >> numPlayers;
            read.ignore(numeric_limits<streamsize>::max(), '\n');

            for (int i = 0; i < numPlayers; ++i) {
                string playerName, nationality;
                int yearOfBirth, height, internationalGoals;
                if (getline(read, playerName, ',') &&
                    read >> yearOfBirth >> height >> internationalGoals >> nationality) {
                    players.emplace_back(playerName, yearOfBirth, height, internationalGoals, nationality);
                } else {
                    cerr << "Error: Invalid player information in file." << endl;
                    break;
                }
                read.ignore(numeric_limits<streamsize>::max(), '\n');
            }
        } else {
            cerr << "Error: File is closed." << endl;
        }
        return players;
    }
};
