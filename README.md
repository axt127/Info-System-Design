#include <iostream>
#include <fstream>   // for file operations
#include <fcntl.h>   // for open() and O_RDONLY
#include <unistd.h>  // for read(), write(), close()
#include <string>
#include <cctype> 
#include <cstring>   // for strcpy and strcmp


using namespace std;

// Function to sort names using bubble sort
void bubbleSort(char names[][100], int count) {
    char temp[100];

    //Sort names alphabetically
    for (int one = 0; one < count - 1; ++one) { 
        for (int two = 0; two < count - 1; ++two)          
            {
            if (strcmp(names[two], names[two + 1]) > 0) {
                // Swap the names
                strcpy(temp, names[two]);
                strcpy(names[two], names[two + 1]);
                strcpy(names[two + 1], temp);
            }
        }
    }
}
// Function to reverse the name
string reverseName(const string& name) {
    string reverse = name;
    int n = reverse.length();
    
    // Reverse the characters in the name
    for (int i = 0; i < n / 2; ++i) {
        swap(reverse[i], reverse[n - i - 1]);
    }

    return reverse;  // Return the reversed name
}

// Function to check if the name is valid (only alphabetic characters)
// and then reverse and write the name to a file
void reverseAndWriteName(const string& name, ofstream& outfile) {
    // Check if the name contains only alphabetic characters
    for (char c : name) {
        if (!isalpha(c) && c != ' ') {  // allow spaces between names
            cout << "Invalid input: only alphabetic characters and spaces are allowed." << endl;
            return;  // Exit if non-alphabetic
        }
    }

    // Create a copy of the name to reverse it
    string reverse = name;

    // Manually reverse the characters in the name
    int n = reverse.length();
    for (int i = 0; i < n / 2; ++i) {
        swap(reverse[i], reverse[n - i - 1]);
    }

    // Write the reversed name to the file
    outfile << reverse << endl;
}


// Function to check if the name is valid (only alphabetic characters) and then reverse and output the name
void reverseAndPrintName(const string& name) {
    // Check if the name contains only alphabetic characters
    for (char c : name) {
        if (!isalpha(c)) {
            cout << "Invalid input: only alphabetic characters are allowed." << endl;
            return;  // Exit if non-alphabetic character is found
        }
    }

    // Create a copy of the name to reverse it
    string reversedName = name;

    // Manually reverse the characters in the name
    int n = reversedName.length();
    for (int i = 0; i < n / 2; ++i) {
        swap(reversedName[i], reversedName[n - i - 1]);
    }

    // Output the reversed name
    cout << reversedName << endl;
}


int main() {
    string name;

    //Question 1

    char c;
    int fd;
    char filename[100];
    cout<<"Question 1: "<<endl;
    cout << "Enter the name of the file to read text from: ";
    cin.getline(filename, 100);  // safer alternative to gets()

    fd = open(filename, O_RDONLY);  // open the file in read-only mode

    if (fd == -1) {
        cerr << "Error opening file!" << endl;
        return 1;
    }

    while (read(fd, &c, 1) != 0){  // read 1 byte at a time from the file
        write(STDOUT_FILENO, &c, 1);  // write to standard output
    }
    write(STDOUT_FILENO, "\n", 1);

    close(fd);  // close the file
    cout<<endl;
//------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    //Question 2
    //a
    cout<<"Question 2 "<<endl;
    cout<<"a.)"<<endl;
     // Loop to accept 5 names
    for (int i = 0; i < 5; ++i) {
        cout << "Enter name " << i + 1 << ": ";
        getline(cin, name);  // Read the name from input

        // Call the function to validate and reverse the name
        reverseAndPrintName(name);
    }
//------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    //b
    cout<<"b.)"<<endl;
    int numOfNames;

    // Open the file for writing
    ofstream outfile("file1.txt");
    if (!outfile) {
        cerr << "Error opening file for writing!" << endl;
        return 1;
    }

    // Prompt the user to enter the number of names
    cout << "How many names would you like to enter? ";
    cin >> numOfNames;
    cin.ignore();  // Ignore the newline character after the number input

    // Read each name and write the reversed version to the file
    for (int i = 0; i < numOfNames; ++i) {
        cout << "Enter name " << i + 1 << ": ";
        getline(cin, name);

        // Call the function to validate, reverse, and write the name to the file
        reverseAndWriteName(name, outfile);
    }

    // Close the file
    outfile.close();
    cout << "Names have been written to file1.txt with characters reversed." << endl;
//------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
//c
    cout<<"c.)"<<endl;

    // Open the input file (file1) for reading
    ifstream inputFile("file1.txt");
    if (!inputFile.is_open()) {
        cerr << "Error opening file1 for reading." << endl;
        return 1;
    }

    // Open the output file (file2) for writing
    ofstream outputFile("file2.txt");
    if (!outputFile.is_open()) {
        cerr << "Error opening file2 for writing." << endl;
        return 1;
    }

    // Read each name from file1
    while (getline(inputFile, name)) {
        // Reverse the name
        string reversedName = reverseName(name);

        // Write the re-reversed name to file2
        outputFile << reversedName << endl;
    }

    // Close the files
    inputFile.close();
    outputFile.close();

    cout << "Names have been read from file1, re-reversed, and written to file2." << endl;
//------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
//d
cout<<"d.)"<<endl;

// Open the output file (file2) for writing
    const int MAX_NAMES = 100;
    char names[MAX_NAMES][100];  // Array to store up to 100 names, each up to 100 characters
    int nameCount = 0;



    ifstream inputFile1("file2.txt");
    if (!inputFile1.is_open()) {
        cerr << "Error opening file2 for reading." << endl;
        return 1;
    }

    // Read each reversed name from file2 and store them in the names array
    while (getline(inputFile1, name) && nameCount < MAX_NAMES) {
        // Reverse the name again to restore the original name
        string originalName = reverseName(name);

        // Copy the original name into the array
        strncpy(names[nameCount], originalName.c_str(), 100);
        nameCount++;
    }

    // Close the input file
    inputFile1.close();

    // Sort the names array using bubble sort
    bubbleSort(names, nameCount);

    // Output the sorted names to the standard output
    cout << "Sorted names (after reversing back):" << endl;
    for (int i = 0; i < nameCount; ++i) {
        cout << names[i] << endl;
    }

    return 0;
}
