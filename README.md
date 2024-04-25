#include <iostream>
#include <string>
#include <fstream>
#include <limits>

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
