#include <iostream>
#include <fstream>
#include <sstream>
#include <windows.h>
using namespace std;

class Bill {
private:
    string Item;
    int Rate, Quantity;
public:
    Bill() : Item(""), Rate(0), Quantity(0) {}
    void setItem(const string& item) { Item = item; }
    void setRate(int rate) { Rate = rate; }
    void setQuant(int quant) { Quantity = quant; }
    string getItem() const { return Item; }
    int getRate() const { return Rate; }
    int getQuant() const { return Quantity; }
};

void pressEnter() {
    cout << "\n\tPress Enter to continue...";
    cin.ignore();
    cin.get();
}

void addItem(Bill& b) {
    bool close = false;
    while (!close) {
        int choice;
        cout << "\t1. Add Item." << endl;
        cout << "\t2. Close." << endl;
        cout << "\tEnter Choice: ";
        cin >> choice;

        if (choice == 1) {
            system("cls");
            string item;
            int rate, quant;
            cout << "\tEnter Item Name: ";
            cin >> item;
            b.setItem(item);
            cout << "\tEnter Rate Of Item: ";
            cin >> rate;
            b.setRate(rate); 
            cout << "\tEnter Quantity Of Item: ";
            cin >> quant;
            b.setQuant(quant);

            ofstream out("Bill.txt", ios::app);
            if (!out) {
                cout << "\tError: File Can't Open!" << endl;
            } else {
                out << b.getItem() << " : " << b.getRate() << " : " << b.getQuant() << endl;
                cout << "\tItem Added Successfully" << endl;
            }
            out.close();
            Sleep(1000);
        }
        else if (choice == 2) {
            system("cls");
            close = true;
            cout << "\tBack To Main Menu!" << endl;
            Sleep(1000);
        }
    }
}

void printBill() {
    system("cls");
    int totalAmount = 0;
    bool close = false;
    while (!close) {
        system("cls");
        int choice;
        cout << "\t1. Add Bill." << endl;
        cout << "\t2. Close Session." << endl;
        cout << "\tEnter Choice: ";
        cin >> choice;

        if (choice == 1) {
            string item;
            int quant;
            cout << "\tEnter Item: ";
            cin >> item;
            cout << "\tEnter Quantity: ";
            cin >> quant;

            ifstream in("Bill.txt");
            ofstream out("BillTemp.txt");
            string line;
            bool found = false;

            while (getline(in, line)) {
                stringstream ss(line);
                string itemName;
                int itemRate, itemQuant;
                char delimiter;
                ss >> itemName >> delimiter >> itemRate >> delimiter >> itemQuant;

                if (item == itemName) {
                    found = true;
                    if (quant <= itemQuant) {
                        int amount = itemRate * quant;
                        cout << "\t Item | Rate | Quantity | Amount" << endl;
                        cout << "\t" << itemName << "\t " << itemRate << "\t " << quant << "\t " << amount ;
                        int newQuant = itemQuant - quant;
                        totalAmount += amount;
                        out << itemName << " : " << itemRate << " : " << newQuant << endl;
                    } else {
                        cout << "\tSorry, " << item << " Out of Stock!" << endl;
                    }
                } else {
                    out << line << endl;
                }
            }

            if (!found) {
                cout << "\tItem Not Available!" << endl;
            }
            out.close();
            in.close();
            remove("Bill.txt");
            rename("BillTemp.txt", "Bill.txt");
            Sleep(1000);
        }
        else if (choice == 2) {
            close = true;
            cout << "\tCounting Total Bill..." << endl;
            Sleep(1000);
        }
    }
    system("cls");
    cout << endl << endl;
    cout << "\t Total Bill ----------------- : " << totalAmount << endl << endl;
    cout << "\tThanks For Shopping!" << endl;
    Sleep(2000);
}

void displayFullBill() {
    system("cls");
    ifstream in("Bill.txt");
    if (!in) {
        cout << "\tError: Could not open the bill file!" << endl;
        Sleep(2000);
        return;
    }
    cout << "\tDisplaying Full Bill:" << endl;
    cout << "\tItem | Rate | Quantity" << endl;
    cout << "\t-----------------------" << endl;
    string line;
    int totalAmount = 0, totalItems = 0;
    while (getline(in, line)) {
        cout << "\t" << line << endl;
        stringstream ss(line);
        string itemName;
        int rate, quant;
        char delimiter;
        ss >> itemName >> delimiter >> rate >> delimiter >> quant;
        totalAmount += rate * quant;
        totalItems += quant;
    }
    in.close();
    cout << "\n\tTotal Amount: " << totalAmount << endl; 
    cout << "\tTotal Products Purchased: " << totalItems << endl;
    pressEnter();
}

void clearItems() {
    ofstream out("Bill.txt", ios::trunc);
    if (!out) {
        cout << "\tError: Could not clear the bill file!" << endl;
    } else {
        cout << "\tAll old items cleared successfully. Ready for new items!" << endl;
    }
    out.close();
    Sleep(1000);
}

int main() {
    Bill b;
    bool exit = false;
    while (!exit) {
        system("cls");
        int val;
        cout << "\tWelcome To Super Market Billing System" << endl;
        cout << "\t**" << endl;
        cout << "\t\t1. Add Item." << endl;
        cout << "\t\t2. Print Bill." << endl;
        cout << "\t\t3. Exit." << endl;
        cout << "\t\t4. Display Full Bill." << endl;
        cout << "\t\t5. Clear Old Items & Start Fresh." << endl;
        cout << "\t\tEnter Choice: ";
        cin >> val;
        if (val == 1) addItem(b);
        else if (val == 2) printBill();
        else if (val == 3) exit = true;
        else if (val == 4) displayFullBill();
        else if (val == 5) clearItems();
        else {
            cout << "\tInvalid Choice! Try Again." << endl;
            Sleep(1000);
        }
    }
    return 0;
}
