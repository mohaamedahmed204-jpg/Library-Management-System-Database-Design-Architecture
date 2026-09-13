# 📚 Library Management System (Database Design & Architecture)

![Database Design](https://img.shields.io/badge/Database-Relational_Schema-blue?style=for-the-badge&logo=databricks)
![Tool](https://img.shields.io/badge/Design_Tool-Draw.io-orange?style=for-the-badge&logo=diagrams.net)
![DBMS](https://img.shields.io/badge/RDBMS-SQL_Server-red?style=for-the-badge&logo=microsoftsqlserver)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

Welcome to the **Library Management System** repository!
This project features a comprehensive, fully normalized relational database architecture and ERD, designed to handle complex library operations efficiently—from book inventory and user management to reservations, borrowings, and fine calculations.

---

## 📌 1. Overview
The **Library Management System (LMS)** database is engineered to streamline and automate the daily operations of a modern library. It offers a robust framework for managing multi-author books, physical book copies, registered users, borrowing transactions, reservation queues, and fine tracking. 

Built using sound relational database principles (up to **3NF/BCNF**), this architecture eliminates data redundancy, ensures data integrity via strict foreign key constraints, and supports scalable queries for real-world production environments.

💡 **Bonus Feature**: Included in this repository is a pre-populated, fully functional **SQL Server Database Backup File (`.bak`)**, allowing you to instantly restore, test, and query the system with real-world sample data!

---

## ⚙️ 2. Core Operations

The architecture is meticulously structured to handle the following core business processes in detail:

### 📖 A. Book & Author Management
* **Multi-Author Support**: Handles books written by single or multiple authors using an associative/junction table (`BookAuthor`).
* **Cataloging**: Stores comprehensive book details (ISBN, Title, Genre, Publication Date, and Additional Info) while keeping physical inventory separate.

### 📦 B. Inventory & Physical Copy Tracking
* **Physical vs. Logical Entity Separation**: Distinguishes between the "book concept" (`Books`) and the "physical copy" on the shelf (`BookCopies`).
* **Real-time Status Updates**: Tracks the availability status (`AvailabilityStatus`: Available / Checked Out / Reserved) of every physical copy independently.

### 👤 C. User & Contact Management
* **User Profiles**: Stores core user information with a unique `LibraryCardNumber`.
* **Flexible Contact Details**: Supports multiple contact numbers and email addresses per user through normalized 1:N relations (`Phones` and `Emails`).

### 🔄 D. Borrowing & Returning Lifecycle
* **Transaction Logging**: Tracks the entire lifecycle of a loan—capturing `BorrowingDate`, `DueDate`, and actual `ReturnDate`.
* **Late Return Detection**: Enables' automated calculation of overdue days by comparing `DueDate` with `ReturnDate`.

### 📑 E. Reservation System
* **Pre-booking Workflow**: Allows users to reserve books.
* **Status Tracking**: Tracks the lifecycle of a reservation (`Pending`, `Fulfilled`, `Cancelled`) with precise timestamps (`ReservationDate`).

### 💰 F. Fine Assessment & Payment Management
* **Dynamic Fine Configuration**: Centralized, configurable fine rules (e.g., `DefaultFinePerDay`, `DefaultBorrowDays`) via the `FineSettings` table for easy policy adjustments without code refactoring.
* **Overdue Fines**: Automatically links fines to specific borrowing transactions (`BorrowingRecordID`) and calculates amounts based on `NumberOfLateDays`.
* **Payment Tracking**: Manages payment statuses (`PaymentStatus`: Paid / Pending).

---

## 🧠 3. Key Concepts Demonstrated

This database model demonstrates advanced relational database engineering principles:

* 📐 **Normalization (1NF to 3NF/BCNF)**: 
  * **1NF**: Atomicity of fields (multivalued fields like emails/phones moved to separate tables).
  * **2NF**: Elimination of partial dependencies through composite key decomposition.
  * **3NF**: Removal of transitive dependencies; non-key attributes depend *only* on the primary key.
* 🔗 **Referential Integrity**: Strict Foreign Key constraints across all relationships prevent orphan records and maintain data consistency.
* 🔀 **Many-to-Many (N:M) Resolution**: Deconstructed N:M relationships (e.g., `Books` ↔️ `Authors`) using bridge/associative tables (`BookAuthor`).
* 🗂️ **Entity-Attribute-Relationship Modeling**: Clear distinction between strong entities (`Users`, `Books`) and weak/dependent entities (`BookCopies`, `Fines`, `Phones`, `Emails`).
* ⚙️ **Parameterization & Configurability**: Separated business logic variables (like daily fine rates) into a dedicated `FineSettings` table.

---

## 🏗️ 4. Architecture & Schema Design

Below is an overview of the relational schema depicting the tables and their primary/foreign key relationships:

                  +-------------------+
                  |   FineSettings    |
                  +-------------------+
                  | DefaultBorrowDays |
                  | DefaultFinePerDay |
                  +-------------------+

    +------------+       +-----------------+       +------------+
    |  Authors   |       |   BookAuthor    |       |   Books    |
    +------------+       +-----------------+       +------------+
    |*AuthorID   |<------|*BookAuthorID    |------>|*BookID     |
    | Name       |       |*FK_AuthorID     |       | Title      |
    +------------+       |*FK_BookID       |       | ISBN       |
                         +-----------------+       | Genre      |
                                                   +------------+
                                                         |
                                                         | 1:N
                                                         v
    +------------+                                 +------------+
    |   Phones   |                                 | BookCopies |
    +------------+                                 +------------+
    |*PhoneID    |                                 |*BookCopyID |
    |*FK_UserID  |--+                              |*FK_BookID  |
    | Phone      |  |                              | Status     |
    +------------+  |                              +------------+
                    |                                    |
                    v                                    v
            +---------------+                    +------------------+
            |     Users     |<-------------------| BorrowingRecords |
            +---------------+   (1:N Users)      +------------------+
            |*UserID        |                    |*BorrowingRecordID|
            | Name          |<---+               |*FK_BookCopyID    |
            | LibraryCardNum|    |               |*FK_UserID        |
            +---------------+    |               | BorrowingDate    |
                    ^            |               | DueDate          |
                    |            |               | ReturnDate       |
    +----------+    |            |               +------------------+
    |  Emails  |    |            |                        |
    +----------+    |            |                        | 1:N
    |*EmailID  |----+            |                        v
    |*FK_UserID|          +--------------+         +--------------+
    | Email    |          | Reservations |         |    Fines     |
    +----------+          +--------------+         +--------------+
                          |*ReservationID|         |*FineID       |
                          |*FK_UserID    |         |*FK_UserID    |
                          | Status       |         |*FK_BorrowID  |--+
                          +--------------+         | LateDays     |  |
                                                   | FineAmount   |  |
                                                   | PaymentStatus|  |
                                                   +--------------+  |
                                                          ^          |
                                                          |----------+

---

## 🛠️ 5. Technologies Used

* **Diagramming & Modeling**: [Draw.io](https://app.diagrams.net/)
* **Database Management System (DBMS)**: Microsoft SQL Server (RDBMS)
* **Query Language**: T-SQL (Transact-SQL)
* **Data Format**: `.drawio` (Source Diagram), `.bak` (Database Backup)

---

## 🚀 6. Getting Started & Testing

To test this database with the provided backup file:

1. **Clone the Repository**:
 ```bash
 git clone [https://github.com/YourUsername/Your-Repo-Name.git](https://github.com/YourUsername/Your-Repo-Name.git)
```
2. **Open the Diagram**:

       Open SimpleLibrary.drawio or SimpleLibrary_RelationalSchema.drawio in Draw.io.

3. **Restore Database in SQL Server**:

       Open SQL Server Management Studio (SSMS).
   
       Right-click on Databases -> Select Restore Database....
   
       Choose Device and select the provided .bak file (P2-Library-Full Database Backup.bak).
   
       Click OK and start executing your test queries!
---

## 👨‍🏫 Instructor

Dr. Mohammed Abu-Hadhoud
Founder & Instructor — Programming Advices

Programming Advices
https://programmingadvices.com
