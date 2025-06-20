# ATM-Simulator-
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

struct Account {
    int pin;
    double balance;
};

bool login(const Account& acc);
void showMenu();
void checkBalance(const Account& acc);
void deposit(Account& acc);
void withdraw(Account& acc);
void saveAccount(const Account& acc, const char* filename);
void loadAccount(Account& acc, const char* filename);
void logTransaction(const string& text, const char* filename);

int main() {
    Account user = {1234, 1000.0};
    const char* dataFile = "account.txt";
    const char* logFile = "log.txt";

    loadAccount(user, dataFile);

    cout << "==== Вітаємо в банкоматі ====\n";
    if (!login(user)) {
        cout << "❌ Невірний PIN. Доступ заборонено.\n";
        return 0;
    }

    int choice;
    do {
        showMenu();
        cout << "Ваш вибір: ";
        cin >> choice;

        switch (choice) {
            case 1:
                checkBalance(user);
                break;
            case 2:
                deposit(user);
                logTransaction("Поповнення рахунку", logFile);
                break;
            case 3:
                withdraw(user);
                logTransaction("Зняття коштів", logFile);
                break;
            case 0:
                saveAccount(user, dataFile);
                cout << "До побачення!\n";
                break;
            default:
                cout << "Невірний вибір.\n";
        }

    } while (choice != 0);

    return 0;
}

bool login(const Account& acc) {
    int inputPIN;
    cout << "Введіть PIN: ";
    cin >> inputPIN;
    return inputPIN == acc.pin;
}

void showMenu() {
    cout << "\n==== Меню ====\n";
    cout << "1. Перевірити баланс\n";
    cout << "2. Поповнити рахунок\n";
    cout << "3. Зняти кошти\n";
    cout << "0. Вихід\n";
}

void checkBalance(const Account& acc) {
    cout << "Поточний баланс: " << acc.balance << " грн\n";
}

void deposit(Account& acc) {
    double amount;
    cout << "Сума для поповнення: ";
    cin >> amount;
    if (amount <= 0) {
        cout << "Некоректна сума.\n";
        return;
    }
    acc.balance += amount;
    cout << "Рахунок поповнено. Новий баланс: " << acc.balance << " грн\n";
}

void withdraw(Account& acc) {
    double amount;
    cout << "Сума для зняття: ";
    cin >> amount;
    if (amount <= 0) {
        cout << "Некоректна сума.\n";
        return;
    }
    if (amount > acc.balance) {
        cout << "Недостатньо коштів.\n";
        return;
    }
    acc.balance -= amount;
    cout << "Кошти знято. Новий баланс: " << acc.balance << " грн\n";
}

void saveAccount(const Account& acc, const char* filename) {
    ofstream file(filename);
    if (file) {
        file << acc.pin << " " << acc.balance;
        file.close();
    }
}

void loadAccount(Account& acc, const char* filename) {
    ifstream file(filename);
    if (file) {
        file >> acc.pin >> acc.balance;
        file.close();
    }
}

void logTransaction(const string& text, const char* filename) {
    ofstream file(filename, ios::app);
    if (file) {
        file << text << "\n";
        file.close();
    }
}

