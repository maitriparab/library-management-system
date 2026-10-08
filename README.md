# Library-Management-System

## Introduction

A **Library Management System** built using **Java and Spring Boot** to manage students, authors, books, library cards, and book issue/return transactions in a college library.

The application provides **REST APIs** for managing students, authors, books, library cards, and library transactions. It follows a **layered backend architecture** consisting of **Controllers, Services, Repositories, and JPA Entities**, with **Spring Data JPA and Hibernate** used for database interaction.

The system allows students to register with the library and automatically receive a library card. Students can issue available books using their library card and return them later. During book returns, the system calculates the applicable overdue fine based on the borrowing duration and configured library rules.

The backend follows a **monolithic architecture** and uses **Spring Boot, Spring Data JPA, Hibernate, and MySQL** for application development and persistent data storage.

---

## Technologies and Dependencies Used

- **Java 21** — Primary programming language.
- **Spring Boot** — Used to build the backend application and REST APIs.
- **Spring Data JPA** — Used for database interaction and repository abstraction.
- **Hibernate** — Used as the ORM framework for mapping Java entities to relational database tables.
- **MySQL** — Used as the relational database for persistent data storage.
- **Maven** — Used as the build and dependency management tool.
- **JUnit / Spring Boot Test** — Used for application testing.
- **Lombok** — Used to reduce boilerplate code in Java classes.
- **REST APIs** — Used for communication between clients and the backend.

---

## Backend Design

### Architecture

The application follows a layered backend architecture:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

JPA entities are used to represent the main objects involved in the library management system.

### Entities

### 1. Student

The **Student** entity stores student information and maintains a one-to-one relationship with a library card.

Main attributes include:

- `id`
- `emailId`
- `name`
- `age`
- `country`
- `card`
- `createdOn`
- `updatedOn`

### 2. Card

The **Card** entity represents a student's library card.

Main attributes include:

- `id`
- `student`
- `createdOn`
- `updatedOn`
- `cardStatus`
- `transactions`
- `books`

The card status can be:

- `ACTIVATED`
- `DEACTIVATED`

### 3. Book

The **Book** entity stores information about books available in the library.

Main attributes include:

- `id`
- `name`
- `genre`
- `author`
- `card`
- `available`
- `transactions`

Book availability is represented using the `available` field.

### Supported Book Genres

The system supports the following book genres:

- `FICTIONAL`
- `NON_FICTIONAL`
- `GEOGRAPHY`
- `HISTORY`
- `POLITICAL_SCIENCE`
- `BOTANY`
- `CHEMISTRY`
- `MATHEMATICS`
- `PHYSICS`

### 4. Author

The **Author** entity stores author information and maintains a relationship with books written by the author.

Main attributes include:

- `id`
- `name`
- `email`
- `age`
- `country`
- `books_written`

An author can be associated with multiple books.

### 5. Transaction

The **Transaction** entity records book issue and return operations.

Main attributes include:

- `id`
- `transactionId`
- `card`
- `book`
- `isIssueOperation`
- `transactionStatus`
- `transactionDate`
- `fineAmount`

The `isIssueOperation` field identifies the type of transaction:

- `true` → `ISSUE`
- `false` → `RETURN`

Transaction status supports:

- `SUCCESSFUL`
- `PENDING`
- `FAILED`

---

# Functionalities Exposed

## Student Controller

The **Student Controller** provides REST APIs for managing students.

### Create Student

```http
POST /createStudent
```

Creates a new student and automatically creates and associates a **library card** with the student.

### Update Student

```http
PUT /updateStudent
```

Updates the information of an existing student.

### Delete Student

```http
DELETE /deleteStudent?id=_
```

Deletes the student and deactivates the associated library card instead of permanently removing the card.

---

## Book Controller

The **Book Controller** provides REST APIs for creating and searching books.

```http
POST /createBook
GET  /getBooks
```

Books can be filtered using:

- Genre
- Author
- Availability

Book availability is automatically updated during issue and return operations.

When a book is issued, it becomes unavailable. When the book is returned, it becomes available again.

---

## Author Controller

The **Author Controller** provides REST APIs for managing authors.

```http
POST   /createAuthor
PUT    /updateAuthor
DELETE /deleteAuthor?id=_
```

Authors can be associated with multiple books.

---

## Transaction Controller

The **Transaction Controller** handles book issue and return operations.

### Issue Book

```http
POST /issueBook?bookId=_&cardId=_
```

Before issuing a book, the system validates:

1. The library card exists.
2. The library card is activated.
3. The book exists.
4. The book is available.
5. The maximum borrowing limit has not been exceeded.

If all validations are successful:

```text
Book → Unavailable
Book → Associated with Card
Transaction → ISSUE
Transaction ID → Returned to Client
```

A successful issue transaction is stored in the database.

### Return Book

```http
POST /returnBook?bookId=_&cardId=_
```

When a book is returned, the system:

1. Validates the book and library card.
2. Retrieves the corresponding successful issue transaction.
3. Calculates the borrowing duration.
4. Calculates the applicable overdue fine.
5. Marks the book as available.
6. Creates a new return transaction.
7. Stores the calculated fine in the return transaction.
8. Returns the transaction ID to the client.

---

## Fine Calculation

The fine is calculated using the configured borrowing period and per-day fine.

```text
Allowed borrowing period = 14 days
Fine per overdue day     = ₹5
```

For example:

```text
Borrowing duration = 18 days
Allowed duration   = 14 days
Overdue duration   = 4 days

Fine = 4 × ₹5
     = ₹20
```
The calculated fine is stored in the corresponding **RETURN transaction**.

---

## Transaction Flow
```text
ISSUE BOOK
    ↓
Validate Card & Book
    ↓
Create ISSUE Transaction
    ↓
Book Becomes Unavailable
    ↓
RETURN BOOK
    ↓
Retrieve Issue Transaction
    ↓
Calculate Borrowing Duration
    ↓
Calculate Overdue Fine
    ↓
Create RETURN Transaction
    ↓
Store Fine Amount
    ↓
Book Becomes Available
```
Transaction records are persisted in the database, allowing the system to maintain a record of book issue and return operations.

---

## Future Improvements
Possible future improvements include:
- Frontend application using React
- JWT-based authentication and authorization
- Admin dashboard
- Student dashboard
- Book reservation functionality
- Pagination and sorting
- Advanced book search
- Email notifications for overdue books
- Online fine payment
- Swagger/OpenAPI documentation
- Docker support
- More unit and integration tests
- Global exception handling
- Input validation using Bean Validation
- Dedicated transaction-history APIs
---

## Author
**Developed by Maitri Parab**
