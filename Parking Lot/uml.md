# 🚗 Parking Lot System - UML Class Diagram

```mermaid
classDiagram

%% =========================
%% ENUMS
%% =========================

class VehicleType {
    <<enumeration>>
    BIKE
    CAR
    TRUCK
}

class PaymentType {
    <<enumeration>>
    CASH
    CARD
    UPI
}

class TicketStatus {
    <<enumeration>>
    ACTIVE
    CLOSED
}

%% =========================
%% VEHICLE HIERARCHY
%% =========================

class Vehicle {
    <<abstract>>
    -String vehicleNumber
    -VehicleType vehicleType
    +getVehicleType()
}

class Bike
class Car
class Truck

Vehicle <|-- Bike
Vehicle <|-- Car
Vehicle <|-- Truck

%% =========================
%% VEHICLE FACTORY
%% =========================

class VehicleFactory {
    +createVehicle(type, vehicleNumber)
}

VehicleFactory ..> Vehicle

%% =========================
%% PARKING SPOTS
%% =========================

class ParkingSpot {
    <<abstract>>
    -int spotId
    -boolean occupied
    -Vehicle vehicle
    +isFree()
    +parkVehicle(vehicle)
    +vacateSpot()
    +canPark(vehicle)
}

class BikeSpot
class CarSpot
class TruckSpot

ParkingSpot <|-- BikeSpot
ParkingSpot <|-- CarSpot
ParkingSpot <|-- TruckSpot

ParkingSpot --> Vehicle

%% =========================
%% PARKING TICKET
%% =========================

class ParkingTicket {
    -String ticketId
    -Vehicle vehicle
    -ParkingSpot parkingSpot
    -DateTime entryTime
    -DateTime exitTime
    -TicketStatus status
}

ParkingTicket --> Vehicle
ParkingTicket --> ParkingSpot

%% =========================
%% FEE STRATEGY
%% =========================

class FeeStrategy {
    <<interface>>
    +calculateFee(duration)
}

class BikeFeeStrategy
class CarFeeStrategy
class TruckFeeStrategy

FeeStrategy <|.. BikeFeeStrategy
FeeStrategy <|.. CarFeeStrategy
FeeStrategy <|.. TruckFeeStrategy

class FeeStrategyFactory {
    +getFeeStrategy(vehicleType)
}

FeeStrategyFactory ..> FeeStrategy

%% =========================
%% PAYMENT STRATEGY
%% =========================

class PaymentStrategy {
    <<interface>>
    +pay(amount)
}

class CashPayment
class CardPayment
class UpiPayment

PaymentStrategy <|.. CashPayment
PaymentStrategy <|.. CardPayment
PaymentStrategy <|.. UpiPayment

%% =========================
%% PARKING FLOOR
%% =========================

class ParkingFloor {
    -int floorId
    -List~ParkingSpot~ spots
    +addSpot(spot)
    +findAvailableSpot(vehicle)
}

ParkingFloor *-- ParkingSpot

%% =========================
%% PARKING LOT
%% =========================

class ParkingLot {
    -List~ParkingFloor~ floors
    +addFloor(floor)
    +parkVehicle(vehicle)
    +exitVehicle(ticket, paymentStrategy)
}

ParkingLot *-- ParkingFloor

ParkingLot ..> ParkingTicket
ParkingLot ..> FeeStrategyFactory
ParkingLot ..> PaymentStrategy

```

---

## Design Patterns Used

### Factory Pattern
- VehicleFactory → Creates Vehicle objects.

### Strategy Pattern
- FeeStrategy → Parking fee calculation.
- PaymentStrategy → Payment processing.

### Composition
- ParkingLot contains multiple ParkingFloors.
- ParkingFloor contains multiple ParkingSpots.

### Inheritance
- Vehicle → Bike, Car, Truck.
- ParkingSpot → BikeSpot, CarSpot, TruckSpot.

---

## Parking Flow

```text
Vehicle Arrives
        |
        v
VehicleFactory
        |
        v
Create Vehicle
        |
        v
ParkingLot
        |
        v
Find Available Spot
        |
        v
Generate Ticket
        |
        v
Park Vehicle
        |
        v
Vehicle Exit
        |
        v
Calculate Fee
        |
        v
Process Payment
        |
        v
Vacate Spot
```
