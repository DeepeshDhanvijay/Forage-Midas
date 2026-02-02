# Forage Midas Core - Kafka Transaction Processing Service

[![Java](https://img.shields.io/badge/Java-17+-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Maven](https://img.shields.io/badge/Maven-3.8+-blue.svg)](https://maven.apache.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This project simulates a **transaction processing service** for Midas, a financial application, built as part of the **Forage JPMorgan Software Engineering Program**. It demonstrates full-stack backend development with **Java, Spring Boot, Kafka, REST APIs, and SQL database integration**.

---

## 📋 Table of Contents

- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Setup & Installation](#-setup--installation)
- [Running the Application](#-running-the-application)
- [API Documentation](#-api-documentation)
- [Project Structure](#-project-structure)
- [Architecture & Workflow](#-architecture--workflow)
- [Test Coverage](#-test-coverage)
- [Technologies Used](#-technologies-used)
- [Contributing](#-contributing)
- [License](#-license)
- [Author](#-author)

---

## 🚀 Features

- ✅ Consumes high-volume transaction messages from a configurable **Kafka topic**
- ✅ Validates transactions and updates **sender and recipient balances** in an **H2 SQL database**
- ✅ Integrates with an external **Incentive API** to apply incentive amounts to recipient balances
- ✅ Provides a **REST API** to query current user balances
- ✅ Fully tested with Maven test suites (`TaskTwoTests` → `TaskFiveTests`)
- ✅ Spring Data JPA for database operations
- ✅ Embedded Kafka for testing

---

## 🛠️ Prerequisites

Before running this project, ensure you have the following installed:

- **Java 17+** - [Download](https://www.oracle.com/java/technologies/downloads/)
- **Maven 3.8+** - [Download](https://maven.apache.org/download.cgi)
- **Git** - [Download](https://git-scm.com/downloads)
- **IDE** (IntelliJ IDEA recommended) - [Download](https://www.jetbrains.com/idea/)
- The Incentive API JAR file located at `services/transaction-incentive-api.jar`

---

## 📦 Setup & Installation

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/forage-midas.git
cd forage-midas
```

### 2. Build the project

```bash
mvn clean install
```

This will:
- Download all dependencies
- Compile the source code
- Run all tests
- Package the application

---

## 🎯 Running the Application

### Step 1: Start the Incentive API

In a separate terminal, run:

```bash
java -jar services/transaction-incentive-api.jar
```

The Incentive API will start on its default port and must be running before starting Midas Core.

### Step 2: Start Midas Core

```bash
mvn spring-boot:run
```

The application will start on `http://localhost:33400`

### Step 3: Verify the application

Check the logs for successful startup messages indicating:
- Spring Boot application started
- Kafka listener initialized
- Database connection established

---

## 📡 API Documentation

### Get User Balance

Retrieves the current balance for a specific user.

**Endpoint:** `GET /balance`

**Query Parameters:**
- `userId` (required) - The unique identifier of the user

**Example Request:**

```bash
curl -X GET "http://localhost:33400/balance?userId=9"
```

**Example Response:**

```json
{
  "amount": 3460.21
}
```

**Status Codes:**
- `200 OK` - Balance retrieved successfully
- `404 Not Found` - User not found
- `400 Bad Request` - Invalid userId parameter

---

## 📁 Project Structure

```
forage-midas/
├── src/
│   ├── main/
│   │   ├── java/com/jpmc/midascore/
│   │   │   ├── component/          # Database interaction utilities
│   │   │   │   └── DatabaseConduit.java
│   │   │   ├── controller/         # REST controllers
│   │   │   │   └── BalanceController.java
│   │   │   ├── entity/             # JPA entities
│   │   │   │   ├── TransactionRecord.java
│   │   │   │   └── UserRecord.java
│   │   │   ├── foundation/         # Core DTOs
│   │   │   │   ├── Balance.java
│   │   │   │   ├── Incentive.java
│   │   │   │   └── Transaction.java
│   │   │   ├── kafka/              # Kafka message processing
│   │   │   │   └── TransactionListener.java
│   │   │   ├── repository/         # Spring Data JPA repositories
│   │   │   │   └── TransactionRecordRepository.java
│   │   │   └── MidasCoreApplication.java
│   │   └── resources/
│   │       └── application.properties    # Configuration
│   └── test/
│       ├── java/com/jpmc/midascore/      # Test classes
│       │   ├── TaskTwoTests.java
│       │   ├── TaskThreeTests.java
│       │   ├── TaskFourTests.java
│       │   ├── TaskFiveTests.java
│       │   └── ...
│       └── resources/
│           └── test_data/                 # Test transaction files
├── services/
│   └── transaction-incentive-api.jar     # External incentive service
├── target/                                # Build output
├── pom.xml                                # Maven configuration
├── application.yml                        # Application configuration
└── README.md
```

---

## 🏗️ Architecture & Workflow

### Transaction Processing Flow

1. **Message Reception** 📥  
   Transaction message received from Kafka topic

2. **Validation** ✔️  
   Validate sender and recipient exist, check sender has sufficient balance

3. **Incentive Calculation** 💰  
   Call external Incentive API → retrieve incentive amount for the transaction

4. **Balance Updates** 💳  
   - Deduct amount from sender's balance
   - Add amount + incentive to recipient's balance
   - Update balances in H2 database

5. **Transaction Recording** 📝  
   Record transaction details in `TransactionRecord` entity

6. **Balance Query** 🔍  
   User queries `GET /balance?userId=<id>` → returns current balance including all incentives

### Component Interaction Diagram

```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│   Kafka     │────▶│ Transaction  │────▶│   Database      │
│   Topic     │     │   Listener   │     │   (H2 SQL)      │
└─────────────┘     └──────────────┘     └─────────────────┘
                           │                       ▲
                           │                       │
                           ▼                       │
                    ┌──────────────┐              │
                    │  Incentive   │              │
                    │     API      │              │
                    └──────────────┘              │
                                                  │
                    ┌──────────────┐              │
                    │   Balance    │──────────────┘
                    │  Controller  │
                    └──────────────┘
```

---

## 🧪 Test Coverage

The project includes comprehensive test suites covering all major functionality:

| Test Suite | Description |
|------------|-------------|
| `TaskOneTests` | Basic project setup and configuration |
| `TaskTwoTests` | Kafka transaction message consumption |
| `TaskThreeTests` | Transaction validation and balance updates |
| `TaskFourTests` | Incentive API integration and balance adjustments |
| `TaskFiveTests` | User balance REST API endpoint |

### Running Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=TaskFiveTests

# Run with coverage report
mvn clean test jacoco:report
```

---

## 🔧 Technologies Used

- **Java 17** - Core programming language
- **Spring Boot 3.x** - Application framework
- **Apache Kafka** - Message streaming platform
- **Spring Data JPA** - Database ORM layer
- **H2 Database** - Embedded SQL database
- **Maven** - Build and dependency management
- **JUnit 5** - Testing framework
- **REST API** - HTTP web services

---

## 🤝 Contributing

This repository is for learning and simulation purposes as part of the **Forage JPMorgan Software Engineering Program**. Contributions are welcome for enhancements and bug fixes!

### How to Contribute

1. Fork the repository
2. Clone your fork: `git clone https://github.com/yourusername/forage-midas.git`
3. Create a feature branch: `git checkout -b feature/amazing-feature`
4. Make your changes and commit: `git commit -m 'Add amazing feature'`
5. Push to the branch: `git push origin feature/amazing-feature`
6. Open a Pull Request

Please ensure:
- All tests pass before submitting PR
- Code follows existing style conventions
- Add tests for new functionality

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2026 Deepesh Dhanvijay

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 👤 Author

**Deepesh Dhanvijay**

- GitHub: [@deepeshdhanvijay](https://github.com/deepeshdhanvijay)
- LinkedIn: [Deepesh Dhanvijay](https://linkedin.com/in/deepeshdhanvijay)
- Email: deepesh.dhanvijay@example.com

---

## 🌟 Acknowledgments

- **JPMorgan Chase & Co.** for the Forage Software Engineering Program
- **Forage** for providing this hands-on learning experience
- The Spring Boot and Apache Kafka communities

---

## 📚 Additional Resources

- [Forage JPMorgan Program](https://www.theforage.com/virtual-internships/prototype/R5iK7HMxJGBgaSbvk/JP-Morgan-Software-Engineering-Virtual-Experience)
- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)

---

<p align="center">Made with ❤️ for learning and skill development</p>
