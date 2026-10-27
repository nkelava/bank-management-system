# Bank Management System

A small console-based bank management application written in C. Bank employees log in to create and manage customer accounts, handle deposits and withdrawals, and list or look up customers. Administrators get an additional panel for managing employee accounts.

All records are kept in memory as singly linked lists and persisted to plain text files in [`src/Data`](src/Data), so the data survives between runs without needing a database.

## Features

- Employee and admin login with masked password input
- Customer accounts: create, view, edit name/surname, delete, list all
- Deposits and withdrawals, with a check for insufficient balance
- Admin panel (Manage employees): add, view, edit (name, surname, role, password), delete and list employees
- Randomly generated, unique account IDs (100–10000) for new customers and employees
- Changes are written back to the data files immediately after each modifying action

## Prerequisites

The application uses Windows-specific APIs (`windows.h`, `conio.h`, `system("cls")`), so it **builds and runs on Windows only**.

- Windows 10 or later
- [Visual Studio 2019 or 2022](https://visualstudio.microsoft.com/) with the **Desktop development with C++** workload (the project targets the v142/v143 toolsets)

## Getting started

1. Clone the repository:

   ```bash
   git clone https://github.com/nkelava/bank-management-system.git
   cd bank-management-system
   ```

2. Open `src/BankManagementSystem.sln` in Visual Studio.
3. Pick a configuration (e.g. `Debug | x64`) and press **F5** (or **Ctrl+F5** to run without debugging).

The data files are opened with paths relative to the working directory (`Data/Customers.txt`, `Data/Employees.txt`), so the program must run with `src/` as its working directory. Visual Studio does this by default. If you run the built `.exe` yourself, start it from inside `src/`, otherwise you will see an "error opening file" message.

## Usage

When the app starts you are asked for an employee ID and password. A default admin account is included:

| ID  | Password |
|-----|----------|
| 0   | admin    |

After logging in, choose an option by pressing its number key:

```
1. Add customer              6. Get customer information
2. Delete customer           7. View all customers
3. Edit customer information 8. Manage employees
4. Deposit money             9. EXIT
5. Withdraw money
```

**Manage employees** asks you to log in again and only accepts an admin account. The admin menu offers:

```
1. Add employee   2. Delete employee   3. Edit employee information
4. Get employee information   5. List of employees   6. EXIT
```

On result screens, press **Backspace** to return to the menu. After a failed lookup you can press **Space** to try again or any other key to go back.

## Data files

Both files are whitespace-separated, one record per line. Names and passwords cannot contain spaces.

**`src/Data/Employees.txt`** — `EmployeeId FirstName LastName Password RoleId`

```
0 Nikola Kelava admin 1
4095 James Howlet employee 2
```

**`src/Data/Customers.txt`** — `CustomerId FirstName LastName Balance`

```
1763 Bruce Wayne 134.00
```

Role IDs are defined in [`src/Enums/RoleEnum.h`](src/Enums/RoleEnum.h): `1` = admin, `2` = employee. You can add accounts by editing these files directly, as long as you keep this format. Name fields hold up to 49 characters and passwords up to 15.

## Project structure

```
src/
├── main.c                      # Entry point: loads data, login, main menu loop
├── Display.h                   # Header/clock, menu rendering, console helpers
├── Auth/Login/Login.h          # Login screen and credential validation
├── Menus/
│   ├── MainMenu.h              # Customer operations (employee menu)
│   └── AdminMenu.h             # Employee management (admin menu)
├── Entities/EntityStructures.h # Customer/Employee structs and linked-list globals
├── Enums/RoleEnum.h            # ADMIN, EMPLOYEE, CUSTOMER roles
├── Errors/Errors.h             # Error messages shown to the user
├── Shared/SharedFunctions.h    # Input handling, node allocation, ID generation
├── Data/
│   ├── Handlers/FileHandler.h  # Reading and writing the data files
│   ├── Customers.txt           # Customer records
│   └── Employees.txt           # Employee records
├── BankManagementSystem.sln    # Visual Studio solution
└── BankManagementSystem.vcxproj
```

The modules are header-only: each `.h` file contains its function implementations and is pulled in through `main.c`, so the whole program compiles as a single translation unit.

## Notes

This is a learning project. Passwords are stored in plain text and there is no encryption or concurrency handling, so it is not meant for real banking data.
