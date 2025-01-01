#include <stdio.h>
#include <stdlib.h>
#include<conio.h>
#include <string.h>

#define MAX_MENU_ITEMS 50
#define MAX_USERS 10
#define MAX_ORDERS 10
#define MAX_CATEGORIES 5

typedef struct {
    int id;
    char name[50];
    int categoryId;  // Stores the index of the category
    float price;
} MenuItem;

typedef struct {
    int id;
    char username[30];
    char password[20];
} User;

typedef struct {
    int id;
    int menuItemId;
    int userId;
    int quantity;  // New field for quantity
    int isAccepted;
} Order;

// Predefined Categories
const char *categories[MAX_CATEGORIES] = {
    "Main Course", "Snacks", "Desserts", "Drinks", "Others"
};

// Global Variables
MenuItem menu[MAX_MENU_ITEMS];
User users[MAX_USERS];
Order orders[MAX_ORDERS];
int menuCount = 0, userCount = 0, orderCount = 0;

// Predefined Admin Account
const char adminUsername[] = "admin";
const char adminPassword[] = "admin123";

// Function Declarations
void clearScreen();
void createAccount();
int logIn();
void addMenuItem();
void updateMenuItem();
void deleteMenuItem();
void searchMenu();
void bookOrder(int userId);
void viewOrders(int userId);
void acceptOrder();

// File Handling Functions
void writeMenu(MenuItem menu[], int menuCount);
void writeUsers(User users[], int userCount);
void writeOrders(Order orders[], int orderCount, MenuItem menu[], int menuCount);

int main() {
    int choice, userId;
    
    while (1) {

        printf("\n\033[1;31m=============== Restaurant Management System ===============\033[0m\n");
        printf("\033[33m1. Create Account\033[0m\n");
        printf("\033[33m2. Log In\033[0m\n");
        printf("\033[33m3. Exit\033[0m\n");
        printf("Choose an option: ");
        scanf("%d", &choice);
        

        switch (choice) {
            case 1:
                createAccount();
                writeUsers(users, userCount);  // Save updated users
                getch();
                clearScreen();
                break;
            case 2:
                userId = logIn();
                getch();
                clearScreen();
                if (userId != -1) {
                    if (userId == 0) { // Admin logged in
                        int adminChoice;
                        do {
                            printf("\n\033[1;32m<<================ Admin Menu ================>>\033[0m\n");
                            printf("\033[1;35m1. Add Menu Item\033[0m\n");
                            printf("\033[1;35m2. Update Menu Item\033[0m\n");
                            printf("\033[1;35m3. Delete Menu Item\033[0m\n");
                            printf("\033[1;35m4. Search Menu\033[0m\n");
                            printf("\033[1;35m5. Accept Order\033[0m\n");
                            printf("\033[1;35m6. Log Out\033[0m\n");
                            printf("Choose an option: ");
                            scanf("%d", &adminChoice);
                            printf("\n");
                            switch (adminChoice) {
                                case 1:
                                    addMenuItem();
                                    writeMenu(menu, menuCount);  // Save updated menu
                                    getch();
                                    clearScreen();
                                    break;
                                case 2:
                                    updateMenuItem();
                                    writeMenu(menu, menuCount);  // Save updated menu
                                    getch();
                                    clearScreen();
                                    break;
                                case 3:
                                    deleteMenuItem();
                                    writeMenu(menu, menuCount);  // Save updated menu
                                    getch();
                                    clearScreen();
                                    break;
                                case 4:
                                    searchMenu();
                                    getch();
                                    clearScreen();
                                    break;
                                case 5:
                                    acceptOrder();
                                    writeOrders(orders, orderCount, menu, menuCount);  // Save updated orders
                                    getch();
                                    clearScreen();
                                    break;
                                case 6:
                                    printf("Logging out...\n");
                                    getch();
                                    clearScreen();
                                    break;
                                default:
                                    printf("Invalid option.\n");
                                    getch();
                                    clearScreen();
                            }
                        } while (adminChoice != 6);
                        

                    } else {
                        int userChoice;
                        do {
                            printf("\n\033[1;32m<<=================== User Menu ===================>>\033[0m\n");
                            printf("\033[1;35m1. Search Menu\033[0m\n");
                            printf("\033[1;35m2. Book Order\033[0m\n");
                            printf("\033[1;35m3. View Orders\033[0m\n");
                            printf("\033[1;35m4. Log Out\033[0m\n");
                            printf("Choose an option: ");
                            scanf("%d", &userChoice);

                            switch (userChoice) {
                                case 1:
                                    searchMenu();
                                    getch();
                                    clearScreen();
                                    break;
                                case 2:
                                    bookOrder(userId);
                                    writeOrders(orders, orderCount, menu, menuCount);  // Save updated orders
                                    getch();
                                    clearScreen();
                                    break;
                                case 3:
                                    viewOrders(userId);
                                    getch();
                                    clearScreen();
                                    break;
                                case 4:
                                    printf("Logging out...\n");
                                    getch();
                                    clearScreen();
                                    break;
                                default:
                                    printf("Invalid option.\n");
                                    getch();
                                    clearScreen();
                            }
                        } while (userChoice != 4);
                    
                    }
                }
                
                break;
            case 3:
                printf("Exiting the program...\n");
                getch();
                clearScreen();
                return 0;
            default:
                printf("Invalid choice. Try again!!!\n");
                getch();
                clearScreen();
        }
    }

    return 0;
}

void clearScreen() {
#ifdef _WIN32
    system("cls");
#else
    system("clear");
#endif
}

// Function Implementations

void createAccount() {
    if (userCount >= MAX_USERS) {
        printf("User limit reached. Cannot create more accounts!!!\n");
        return;
    }

    printf("Enter username: ");
    fflush(stdin);
    gets(users[userCount].username);

    printf("Enter password: ");
    fflush(stdin);
    gets(users[userCount].password);

    users[userCount].id = userCount + 1; // ID starts from 1
    userCount++;
    printf("Account created successfully!!!\n");
}

int logIn() {
    char username[30], password[20];
    printf("Username: ");
    fflush(stdin);
    gets(username);

    printf("Password: ");
    fflush(stdin);
    gets(password);

    // Check for admin login
    if (strcmp(username, adminUsername) == 0 && strcmp(password, adminPassword) == 0) {
        printf("Logged in as Admin!\n");
        return 0; // Admin user ID is 0
    }

    // Check for user login
    for (int i = 0; i < userCount; i++) {
        if (strcmp(users[i].username, username) == 0 && strcmp(users[i].password, password) == 0) {
            printf("Logged in successfully!\n");
            return users[i].id;
        }
    }
    printf("Log In Failed!!!\n");
    return -1;
}


void addMenuItem() {
    if (menuCount >= MAX_MENU_ITEMS) {
        printf("Menu is full, cannot add more items.\n");
        return;
    }

    printf("Enter item name: ");
    fflush(stdin);
    gets(menu[menuCount].name);

    printf("Choose a category:\n");
    for (int i = 0; i < MAX_CATEGORIES; i++) {
        printf("%d. %s\n", i + 1, categories[i]);
    }
    printf("Enter category number: ");
    int categoryId;
    scanf("%d", &categoryId);

    if (categoryId < 1 || categoryId > MAX_CATEGORIES) {
        printf("Invalid category choice.\n");
    } else {
        menu[menuCount].categoryId = categoryId - 1;
        printf("Enter item price: ");
        scanf("%f", &menu[menuCount].price);
        menu[menuCount].id = menuCount + 1;
        menuCount++;
        printf("Menu item added successfully.\n");
    }
}

void updateMenuItem() {
    int id;
    printf("Enter the ID of the menu item to update: ");
    scanf("%d", &id);

    for (int i = 0; i < menuCount; i++) {
        if (menu[i].id == id) {
            printf("Enter new name: ");
            fflush(stdin);
            gets(menu[i].name);

            printf("Choose a new category:\n");
            for (int j = 0; j < MAX_CATEGORIES; j++) {
                printf("%d. %s\n", j + 1, categories[j]);
            }
            printf("Enter category number: ");
            int categoryId;
            scanf("%d", &categoryId);

            if (categoryId < 1 || categoryId > MAX_CATEGORIES) {
                printf("Invalid category choice.\n");
            } else {
                menu[i].categoryId = categoryId - 1;
                printf("Enter new price: ");
                scanf("%f", &menu[i].price);
                printf("Menu item updated successfully.\n");
            }
            return;
        }
    }
    printf("Menu item not found.\n");
}


void deleteMenuItem() {
    int id, found = 0;
    printf("Enter the ID of the menu item to delete: ");
    scanf("%d", &id);

    for (int i = 0; i < menuCount; i++) {
        if (menu[i].id == id) {
            // Shift all items after the deleted one
            for (int j = i; j < menuCount - 1; j++) {
                menu[j] = menu[j + 1];
            }
            menuCount--;

            // Update IDs to maintain sequential order
            for (int j = i; j < menuCount; j++) {
                menu[j].id = j + 1; // Assuming IDs are 1-based
            }

            found = 1;
            printf("Menu item deleted successfully.\n");
            break;
        }
    }

    if (!found) {
        printf("Menu item not found.\n");
    }
}


void searchMenu() {
    printf("\n............Menu Items............\n");

    for (int i = 0; i < MAX_CATEGORIES; i++) {
        printf("\nCategory: %s\n", categories[i]);
        for (int j = 0; j < menuCount; j++) {
            if (menu[j].categoryId == i) {
                printf("  ID: %d, Name: %s, Price: %.2f\n",
                       menu[j].id, menu[j].name, menu[j].price);
            }
        }
    }
}


void bookOrder(int userId) {
    int menuItemId, quantity;
    printf("Enter the menu item ID to order: ");
    scanf("%d", &menuItemId);

    // Check if the menu item exists
    int menuIndex = -1;
    for (int i = 0; i < menuCount; i++) {
        if (menu[i].id == menuItemId) {
            menuIndex = i;
            break;
        }
    }

    if (menuIndex == -1) {
        printf("Invalid menu item ID. Order cannot be placed.\n");
        return;
    }

    printf("Enter quantity: ");
    scanf("%d", &quantity);

    orders[orderCount].id = orderCount + 1;
    orders[orderCount].menuItemId = menuItemId;
    orders[orderCount].userId = userId;
    orders[orderCount].quantity = quantity;
    orders[orderCount].isAccepted = 0;

    float totalPrice = menu[menuIndex].price * quantity;
    printf("Order placed successfully. Total Price: %.2f\n", totalPrice);

    orderCount++;
}


void viewOrders(int userId) {
    float totalOrderedPrice = 0; // To accumulate the total price of all orders
    printf("\nOrders for User ID %d:\n", userId);

    for (int i = 0; i < orderCount; i++) {
        if (orders[i].userId == userId) {
            // Find the menu item by ID
            int menuIndex = -1;
            for (int j = 0; j < menuCount; j++) {
                if (menu[j].id == orders[i].menuItemId) {
                    menuIndex = j;
                    break;
                }
            }

            if (menuIndex != -1) {
                float totalPrice = orders[i].quantity * menu[menuIndex].price;
                totalOrderedPrice += totalPrice; // Accumulate total price
                printf("  Order ID: %d, Menu Item: %s, Quantity: %d, Total Price: %.2f, Status: %s\n",
                       orders[i].id, menu[menuIndex].name, orders[i].quantity, totalPrice,
                       orders[i].isAccepted ? "Accepted" : "Pending");
            } else {
                printf("  Order ID: %d, Menu Item ID: %d (Not Found), Quantity: %d, Status: %s\n",
                       orders[i].id, orders[i].menuItemId, orders[i].quantity,
                       orders[i].isAccepted ? "Accepted" : "Pending");
            }
        }
    }

    // Display the total ordered price at the end
    printf("\nTotal Ordered Price for User ID %d: %.2f\n", userId, totalOrderedPrice);
}


void acceptOrder() {
    int found = 0;

    printf("Accepting all pending orders...\n");

    for (int i = 0; i < orderCount; i++) {
        if (orders[i].isAccepted == 0) { // Check if order is still pending
            orders[i].isAccepted = 1;   // Accept the order
            printf("Order ID %d has been accepted.\n", orders[i].id);
            found = 1; // Flag to indicate at least one order was accepted
        }
    }

    if (!found) {
        printf("No pending orders to accept.\n");
    } else {
        printf("All pending orders have been accepted successfully.\n");
    }
}


// File Handling Implementations

void writeMenu(MenuItem menu[], int menuCount) {
    FILE *file = fopen("menu.txt", "w");
    if (file==NULL) {
        printf("Error opening menu file for writing.\n");
        return;
    }
    printf("Writing menu to file...\n");
    for (int i = 0; i < MAX_CATEGORIES; i++) {
        fprintf(file, "\nCategory: %s\n", categories[i]);
        for (int j = 0; j < menuCount; j++) {
            if (menu[j].categoryId == i) {
                fprintf(file, "ID: %d, Name: %s, Price: %.2f\n",
                        menu[j].id, menu[j].name, menu[j].price);
            }
        }
    }
    fclose(file);
    printf("Menu written successfully.\n");
}


void writeUsers(User users[], int userCount) {
    FILE *file = fopen("users.txt", "w");
    if (file == NULL) {
        printf("Error opening users file for writing.\n");
        return;
    }

    printf("Writing users to file...\n");
    for (int i = 0; i < userCount; i++) {
        fprintf(file, "%d. UserName: %s, Password: %s, Role: User.\n", users[i].id, users[i].username, users[i].password);
    }

    fclose(file);
    printf("Users written successfully.\n");
}


void writeOrders(Order orders[], int orderCount, MenuItem menu[], int menuCount) {
    FILE *file = fopen("orders.txt", "w");
    if (file==NULL) {
        printf("Error opening orders file for writing.\n");
        return;
    }
    printf("Writing orders to file...\n");
    for (int i = 0; i < orderCount; i++) {
        float price = 0.0f;
        for (int j = 0; j < menuCount; j++) {
            if (menu[j].id == orders[i].menuItemId) {
                price = menu[j].price * orders[i].quantity;
                break;
            }
        }
        fprintf(file, "Order ID: %d, Menu Item ID: %d, Quantity: %d, Total Price: %.2f, Status: %s\n",
                orders[i].id, orders[i].menuItemId, orders[i].quantity, price,
                orders[i].isAccepted ? "Accepted" : "Pending");
    }
    fclose(file);
    printf("Orders written successfully.\n");
}
