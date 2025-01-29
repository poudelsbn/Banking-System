

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Structure to store account details
struct Account {
    char name[50];   // Account holder's name
    float balance;   // Account balance
};

// Function declarations
void createAccount(); // Function to create a new account
void depositMoney(char name[]); // Function to deposit money into an account
void withdrawMoney(char name[]); // Function to withdraw money from an account
void checkBalance(char name[]); // Function to check the balance of an account
void ensureFileExists(); // Function to ensure the accounts file exists

// File to store account data
char filename[] = "accounts.txt";

int main() {
    char hasAccount[4]; // Input to check if user has an account (yes/no)
    char accountName[50]; // Store the name of the account holder
    int choice; // Store the user's menu choice

    printf("Welcome to 7 Members Bank\n"); // Greeting message

    ensureFileExists(); // Ensure the file exists before any operation

    while (1) { // Main program loop
        // Ask if the user has an account
        printf("\nDo you have an account? (yes/no): ");
        scanf("%s", hasAccount);

        if (strcmp(hasAccount, "yes") == 0) { // If the user has an account
            // Ask for the account name
            printf("Enter your account name: ");
            scanf("%s", accountName);

            // Banking menu for existing users
            while (1) {
                printf("\n=== Banking Options ===\n");
                printf("1. Deposit Money\n");
                printf("2. Withdraw Money\n");
                printf("3. Check Balance\n");
                printf("4. Exit\n");
                printf("Enter your choice: ");
                scanf("%d", &choice);

                if (choice == 1) {
                    depositMoney(accountName); // Call function to deposit money
                } else if (choice == 2) {
                    withdrawMoney(accountName); // Call function to withdraw money
                } else if (choice == 3) {
                    checkBalance(accountName); // Call function to check balance
                } else if (choice == 4) {
                    printf("Exiting the program. Goodbye!\n");
                    exit(0); // Terminate the program
                } else {
                    printf("Invalid choice. Please try again.\n"); // Handle invalid input
                }
            }
        } else if (strcmp(hasAccount, "no") == 0) { // If the user does not have an account
            createAccount(); // Prompt to create a new account
        } else {
            printf("Invalid input. Please enter 'yes' or 'no'.\n"); // Handle invalid input
        }
    }

    return 0; // End of the program
}

// Function to ensure the accounts file exists
void ensureFileExists() {
    FILE *file = fopen(filename, "a"); // Open the file in append mode to create it if it does not exist
    if (file == NULL) { // Check if the file could not be opened
        printf("Error: Unable to create accounts file.\n");
        exit(1); // Exit the program if file creation fails
    }
    fclose(file); // Close the file immediately after ensuring its existence
}

// Function to create a new account
void createAccount() {
    FILE *file;
    struct Account account;

    // Open the file to append the new account
    file = fopen(filename, "a");
    if (file == NULL) { // Check if the file could not be opened
        printf("Error: Unable to open file.\n");
        return;
    }

    // Take the account holder's name
    printf("Enter your name to create an account: ");
    scanf("%s", account.name);
    account.balance = 0.0; // Initialize balance to 0

    // Save the account to the file
    fprintf(file, "%s %.2f\n", account.name, account.balance); // Write account details to file
    fclose(file); // Close the file

    printf("Account created successfully for %s!\n", account.name); // Confirmation message
}

// Function to deposit money into an account
void depositMoney(char name[]) {
    FILE *file, *tempFile;
    struct Account account;
    float amount; // Amount to deposit
    int found = 0; // Flag to check if the account exists

    // Open files for reading and writing
    file = fopen(filename, "r"); // Open the original file in read mode
    tempFile = fopen("temp.txt", "w"); // Open a temporary file in write mode

    if (file == NULL || tempFile == NULL) { // Check if files could not be opened
        printf("Error: Unable to open file.\n");
        return;
    }

    printf("Enter amount to deposit: ");
    scanf("%f", &amount); // Take the deposit amount from the user

    // Read all accounts and update the balance for the matching account
    while (fscanf(file, "%s %f", account.name, &account.balance) != EOF) {
        if (strcmp(account.name, name) == 0) { // Check if the account matches the given name
            account.balance = account.balance + amount; // Update balance as a = a + b
            found = 1; // Mark account as found
        }
        fprintf(tempFile, "%s %.2f\n", account.name, account.balance); // Write updated account details to temp file
    }

    fclose(file); // Close the original file
    fclose(tempFile); // Close the temporary file

    // Replace original file with updated file
    remove(filename); // Delete the original file
    rename("temp.txt", filename); // Rename the temp file to the original file name

    if (found) { // Check if the account was found
        printf("Deposit successful!\n");
    } else {
        printf("Account not found.\n");
    }
}

// Function to withdraw money from an account
void withdrawMoney(char name[]) {
    FILE *file, *tempFile;
    struct Account account;
    float amount; // Amount to withdraw
    int found = 0; // Flag to check if the account exists

    // Open files for reading and writing
    file = fopen(filename, "r"); // Open the original file in read mode
    tempFile = fopen("temp.txt", "w"); // Open a temporary file in write mode

    if (file == NULL || tempFile == NULL) { // Check if files could not be opened
        printf("Error: Unable to open file.\n");
        return;
    }

    printf("Enter amount to withdraw: ");
    scanf("%f", &amount); // Take the withdrawal amount from the user

    // Read all accounts and update the balance for the matching account
    while (fscanf(file, "%s %f", account.name, &account.balance) != EOF) {
        if (strcmp(account.name, name) == 0) { // Check if the account matches the given name
            if (account.balance >= amount) { // Ensure sufficient balance is available
                account.balance = account.balance - amount; // Update balance as a = a - b
                found = 1; // Mark account as found
            } else {
                printf("Insufficient balance.\n"); // Display error if balance is insufficient
            }
        }
        fprintf(tempFile, "%s %.2f\n", account.name, account.balance); // Write updated account details to temp file
    }

    fclose(file); // Close the original file
    fclose(tempFile); // Close the temporary file

    // Replace original file with updated file
    remove(filename); // Delete the original file
    rename("temp.txt", filename); // Rename the temp file to the original file name

    if (found) { // Check if the account was found
        printf("Withdrawal successful!\n");
    } else {
        printf("Account not found.\n");
    }
}

// Function to check the balance of an account
void checkBalance(char name[]) {
    FILE *file;
    struct Account account;
    int found = 0; // Flag to check if the account exists

    file = fopen(filename, "r"); // Open the file in read mode
    if (file == NULL) { // Check if the file could not be opened
        printf("Error: Unable to open file.\n");
        return;
    }

    // Read all accounts and display the balance for the matching account
    while (fscanf(file, "%s %f", account.name, &account.balance) != EOF) {
        if (strcmp(account.name, name) == 0) { // Check if the account matches the given name
            printf("Account Name: %s\n", account.name); // Display the account name
            printf("Balance: %.2f\n", account.balance); // Display the account balance
            found = 1; // Mark account as found
            break;
        }
    }

    fclose(file); // Close the file

    if (!found) { // Check if the account was not found
        printf("Account not found.\n");
    }
}
