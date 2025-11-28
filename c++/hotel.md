#include <iostream>
#include <vector>
using namespace std;

void firsttime(string name){
    cout << "This is your first visit to your hostel sir " << name << ". ";
    cout << "How may I help you?" << endl;
}

void secondtime(string name){
    cout << "Welcome back sir " << name << "....\nHow may I help you?" << endl;
}

int main() {
    vector<string> user_list;  // Stores users who visited

    string user;
   

    bool found = false;

    // Check if user is already in the list
    
    for (int c=0;c<=4;c++){
        
        cout << "Enter the name: ";
    cin >> user;
    for (string name : user_list) {
        if (name == user) {
            found = true;
            break;
        }
    }

    if (found) {
        secondtime(user);
    } else {
        firsttime(user);
        user_list.push_back(user);  // Add to list for next visit
    }
    }
    return 0;
}
