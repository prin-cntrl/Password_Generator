#include <iostream>
#include <fstream>
#include <string>
#include <random>
#include <limits>
#include <vector>

using namespace std;

// Function to generate a secure random password
string generatePassword(int length) {
    const string characters = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789~!@#$%^&*()_+?:/;,";
    random_device rd;                             // Seed for randomness
    mt19937 generator(rd());                      // Mersenne Twister RNG
    uniform_int_distribution<> distribution(0, characters.size() - 1);

    string password;
    for (int i = 0; i < length; ++i) {
        password += characters[distribution(generator)];
    }
    return password;
}

// Function to XOR encrypt/decrypt file content
void xorEncryptDecrypt(string& content, const string& key) {
    for (size_t i = 0; i < content.size(); ++i) {
        content[i] ^= key[i % key.size()];
    }
}

// Function to load and decrypt the file
string loadAndDecryptFile(const string& filename, const string& key) {
    ifstream fileIn(filename, ios::binary);
    if (!fileIn) {
        return ""; // Return an empty string if the file doesn't exist
    }

    string content((istreambuf_iterator<char>(fileIn)), istreambuf_iterator<char>());
    fileIn.close();

    xorEncryptDecrypt(content, key); // Decrypt content
    return content;
}

// Function to encrypt and save content back to the file
void encryptAndSaveFile(const string& filename, const string& content, const string& key) {
    string encryptedContent = content;
    xorEncryptDecrypt(encryptedContent, key);

    ofstream fileOut(filename, ios::binary);
    fileOut.write(encryptedContent.c_str(), encryptedContent.size());
    fileOut.close();
}

// Function to validate integer input
int getValidatedInput(int min, int max) {
    int choice;
    while (true) {
        cin >> choice;
        if (cin.fail() || choice < min || choice > max) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid input. Please try again: ";
        } else {
            return choice;
        }
    }
}

// Function to display file content securely
void viewAndReEncryptFile(const string& filename, const string& key) {
    string content = loadAndDecryptFile(filename, key);
    if (content.empty()) {
        cout << "The file is empty or doesn't exist." << endl;
        return;
    }

    // Display decrypted content in memory (not saving to disk)
    cout << "Decrypted content:\n" << content << endl;

    // Re-encrypt the file
    encryptAndSaveFile(filename, content, key);
    cout << "The file has been re-encrypted." << endl;
}

// Function to generate and save a password
void generatePasswordAndSave(const string& filename, const string& key) {
    vector<string> accountNames = {"Facebook", "Twitter", "LinkedIn", "Other"};
    cout << "Select the account type:\n";
    for (size_t i = 0; i < accountNames.size(); ++i) {
        cout << i + 1 << ". " << accountNames[i] << endl;
    }
    cout << "Your choice: ";
    int account = getValidatedInput(1, accountNames.size());
    string name = accountNames[account - 1];

    cout << "Choose password length:\n";
    cout << "1. Use a randomized length (16 characters)\n";
    cout << "2. Enter your desired length\n";
    cout << "Your choice: ";
    int choice = getValidatedInput(1, 2);

    int passwordLength = 16; // Default length
    if (choice == 2) {
        cout << "Enter desired password length (8-128): ";
        passwordLength = getValidatedInput(8, 128);
    }

    string password = generatePassword(passwordLength);
    cout << name << " password: " << password << endl;

    cout << "Do you want to save this password to the file? (y/n): ";
    char saveOption;
    cin >> saveOption;
    if (saveOption == 'y' || saveOption == 'Y') {
        string content = loadAndDecryptFile(filename, key);
        content += name + " password: " + password + "\n";
        encryptAndSaveFile(filename, content, key);
        cout << "Password saved and file re-encrypted." << endl;
    }
}

int main() {
    // Prompt user to input a secure encryption key
    cout << "Enter a secure encryption key (minimum 8 characters): ";
    string key;
    while (true) {
        cin >> key;
        if (key.size() >= 8) {
            break;
        } else {
            cout << "Key too short. Please enter at least 8 characters: ";
        }
    }

    // Main menu
    while (true) {
        cout << "\nChoose an option:\n";
        cout << "1. Generate a new password\n";
        cout << "2. View and re-encrypt the password file\n";
        cout << "3. Exit\n";
        cout << "Your choice: ";
        int choice = getValidatedInput(1, 3);

        if (choice == 1) {
            generatePasswordAndSave("password.txt", key);
        } else if (choice == 2) {
            viewAndReEncryptFile("password.txt", key);
        } else {
            cout << "Exiting program. Goodbye!" << endl;
            break;
        }
    }

    return 0;
}
