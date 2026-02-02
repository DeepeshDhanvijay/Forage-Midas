# Forage Midas Core - Kafka Transaction Processing Service

This project simulates a **transaction processing service** for Midas, a financial application, built as part of the **Forage JPMorgan Software Engineering Program**. It demonstrates full-stack backend development with **Java, Spring Boot, Kafka, REST APIs, and SQL database integration**.

---

## Features

- Consumes high-volume transaction messages from a configurable **Kafka topic**.
- Validates transactions and updates **sender and recipient balances** in an **H2 SQL database**.
- Integrates with an external **Incentive API** to apply incentive amounts to recipient balances.
- Provides a **REST API** to query current user balances.
- Fully tested with Maven test suites (`TaskTwoTests` → `TaskFiveTests`).

---

## Prerequisites

- **Java 17+**
- **Maven 3.8+**
- **Git**
- IDE (IntelliJ recommended)
- Kafka (embedded Kafka used for tests)
- The Incentive API JAR file located at `services/transaction-incentive-api.jar`

---

## Setup & Run

### 1. Build the project
```bash
mvn clean install
2. Run the Incentive API
java -jar services/transaction-incentive-api.jar

3. Run Midas Core
mvn spring-boot:run

4. REST API for user balances
GET http://localhost:33400/balance?userId=<id>


Sample query using curl:

curl -X GET "http://localhost:33400/balance?userId=9"


Sample response:

{
  "amount": 3460.21
}

Project Structure
src/
├─ main/
│  ├─ java/com/jpmc/midascore/
│  │  ├─ component/       # Database interaction utilities
│  │  ├─ entity/          # JPA entities: UserRecord, TransactionRecord
│  │  ├─ foundation/      # Core DTOs: Transaction, Incentive, Balance
│  │  ├─ kafka/           # Kafka listener: TransactionListener
│  │  ├─ repository/      # Spring Data JPA repositories
│  │  └─ controller/      # REST controller: BalanceController
│  └─ resources/
│     └─ application.yml  # Configuration for Kafka and DB
├─ test/                  # Unit and integration tests

Example Workflow

Transaction message received from Kafka.

Validate sender and recipient, check balances.

Call Incentive API → get incentive amount.

Update sender and recipient balances in H2 database.

Record transaction in TransactionRecord entity.

User queries /balance?userId=<id> → returns current balance including incentives.

Test Coverage

TaskTwoTests → Kafka transaction consumption.

TaskThreeTests → Transaction validation and balance updates.

TaskFourTests → Incentive API integration and balance adjustments.

TaskFiveTests → User balance REST API endpoint.

Contributing

This repository is for learning and simulation purposes as part of the Forage JPMorgan program. Contributions are welcome for enhancements and bug fixes. Please follow standard GitHub workflow: fork → clone → branch → PR.

License

MIT License

Author

Deepesh Dhanvijay
