# 🚗 Car Rental System - UML Diagram

## Overview

The Car Rental System is designed using Object-Oriented Design principles and common design patterns such as:

* Factory Pattern (Vehicle Creation)
* Strategy Pattern (Payment Processing)
* Singleton Pattern (Rental System Management)

The system supports:

* Multiple Rental Stores
* Multiple Vehicle Types
* Vehicle Reservation Management
* Payment Processing
* Rental Lifecycle Tracking

---

# UML Class Diagram

```mermaid
classDiagram

%% ==========================
%% ENUMS
%% ==========================

class VehicleType {
    <<enumeration>>
    ECONOMY
    LUXURY
    SUV
    BIKE
    AUTO
}

class VehicleStatus {
    <<enumeration>>
    AVAILABLE
    RESERVED
    RENTED
    MAINTENANCE
    OUT_OF_SERVICE
}

class ReservationStatus {
    <<enumeration>>
    PENDING
    CONFIRMED
    IN_PROGRESS
    COMPLETED
    CANCELLED
}

%% ==========================
%% LOCATION
%% ==========================

class Location {
    +address : String
    +city : String
    +state : String
    +zipCode : String
}

%% ==========================
%% VEHICLE HIERARCHY
%% ==========================

class Vehicle {
    <<abstract>>
    +registrationNumber : String
    +model : String
    +type : VehicleType
    +status : VehicleStatus
    +baseRentalPrice : double

    +calculateRentalFee()
}

class EconomyVehicle
class LuxuryVehicle
class SUVVehicle
class BikeVehicle
class AutoVehicle

Vehicle <|-- EconomyVehicle
Vehicle <|-- LuxuryVehicle
Vehicle <|-- SUVVehicle
Vehicle <|-- BikeVehicle
Vehicle <|-- AutoVehicle

%% ==========================
%% FACTORY
%% ==========================

class VehicleFactory {
    +createVehicle()
}

VehicleFactory ..> Vehicle

%% ==========================
%% USER
%% ==========================

class User {
    +id : int
    +name : String
    +email : String

    +addReservation()
    +cancelReservation()
}

%% ==========================
%% RENTAL STORE
%% ==========================

class RentalStore {
    +id : int
    +name : String

    +addVehicle()
    +removeVehicle()
    +getAvailableVehicles()
    +isVehicleAvailable()
}

RentalStore *-- Location
RentalStore *-- Vehicle

%% ==========================
%% RESERVATION
%% ==========================

class Reservation {
    +id : int
    +startDate : Date
    +endDate : Date
    +status : ReservationStatus
    +totalAmount : double

    +confirmReservation()
    +startRental()
    +completeRental()
    +cancelReservation()
}

User "1" --> "*" Reservation
Reservation --> Vehicle
Reservation --> RentalStore

%% ==========================
%% RESERVATION MANAGER
%% ==========================

class ReservationManager {
    +createReservation()
    +modifyReservation()
    +cancelReservation()
}

ReservationManager --> Reservation

%% ==========================
%% STRATEGY PATTERN
%% ==========================

class PaymentStrategy {
    <<interface>>
    +processPayment()
}

class CreditCardPayment
class DebitCardPayment
class CashPayment

PaymentStrategy <|.. CreditCardPayment
PaymentStrategy <|.. DebitCardPayment
PaymentStrategy <|.. CashPayment

%% ==========================
%% PAYMENT PROCESSOR
%% ==========================

class PaymentProcessor {
    +processPayment()
}

PaymentProcessor --> PaymentStrategy

%% ==========================
%% RENTAL SYSTEM
%% ==========================

class RentalSystem {
    <<Singleton>>

    +getInstance()
}

RentalSystem o-- RentalStore
RentalSystem o-- ReservationManager
RentalSystem o-- PaymentProcessor
RentalSystem o-- VehicleFactory
```

---

# Relationship Explanation

## RentalSystem → RentalStore

A Rental System manages multiple rental stores.

```text
RentalSystem
    |
    +---- RentalStore
    +---- RentalStore
    +---- RentalStore
```

---

## RentalStore → Vehicle

Each store owns and manages multiple vehicles.

```text
RentalStore
    |
    +---- Vehicle
    +---- Vehicle
    +---- Vehicle
```

Composition is used because vehicles belong to a store.

---

## Vehicle Inheritance

All vehicle types inherit from the Vehicle base class.

```text
Vehicle
  |
  +-- EconomyVehicle
  +-- SUVVehicle
  +-- LuxuryVehicle
  +-- BikeVehicle
  +-- AutoVehicle
```

---

## User → Reservation

One user can create multiple reservations.

```text
User
  |
  +---- Reservation
  +---- Reservation
```

Cardinality:

```text
1 User → Many Reservations
```

---

## Reservation → Vehicle

A reservation is associated with exactly one vehicle.

```text
Reservation
      |
      +---- Vehicle
```

---

## Payment Strategy Pattern

Payment behavior is selected dynamically.

```text
PaymentProcessor
        |
        +---- PaymentStrategy
                    |
      --------------------------------
      |              |              |
CreditCard      DebitCard        Cash
```

This allows new payment methods to be added without modifying existing code.

---

# Design Patterns Used

| Pattern           | Purpose                        |
| ----------------- | ------------------------------ |
| Factory Pattern   | Vehicle Creation               |
| Strategy Pattern  | Payment Processing             |
| Singleton Pattern | Central Rental System Instance |

---

# System Workflow

```text
User
 |
 v
Search Vehicle
 |
 v
Rental Store
 |
 v
Available Vehicle
 |
 v
Reservation Manager
 |
 v
Reservation Created
 |
 v
Payment Processor
 |
 v
Reservation Confirmed
 |
 v
Rental Started
 |
 v
Vehicle Returned
 |
 v
Rental Completed
```

---

# Key Advantages

* Extensible Vehicle Types
* Modular Payment System
* Scalable Multi-Store Architecture
* Easy Maintenance
* Supports Future Enhancements
* Follows SOLID Principles
* Interview-Friendly LLD Design

```
```
