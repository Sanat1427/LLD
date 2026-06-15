# Elevator System UML

## Class Diagram

```mermaid
classDiagram

class Building{
    -String name
    -int totalFloors
    -ElevatorController controller
}

class Floor{
    -int floorNumber
}

class ElevatorController{
    -List~Elevator~ elevators
    -List~Floor~ floors
    -SchedulingStrategy strategy

    +requestElevator()
    +requestFloor()
    +step()
    +setSchedulingStrategy()
}

class Elevator{
    -int id
    -int currentFloor
    -Direction direction
    -ElevatorState state
    -Queue~ElevatorRequest~ requestQueue

    +addRequest()
    +moveTo()
    +completeArrival()
    +addObserver()
}

class ElevatorRequest{
    -int floor
    -Direction direction
    -boolean isInternalRequest
}

class ElevatorObserver{
    <<interface>>
    +onFloorChange()
    +onStateChange()
}

class ElevatorDisplay{
    +onFloorChange()
    +onStateChange()
}

class SchedulingStrategy{
    <<interface>>
    +getNextStop()
}

class FCFSSchedulingStrategy{
    +getNextStop()
}

class ScanSchedulingStrategy{
    +getNextStop()
}

class LookSchedulingStrategy{
    +getNextStop()
}

class Direction{
    <<enumeration>>
    UP
    DOWN
    IDLE
}

class ElevatorState{
    <<enumeration>>
    IDLE
    MOVING
    STOPPED
    MAINTENANCE
}

Building --> ElevatorController

ElevatorController --> Elevator
ElevatorController --> Floor

Elevator --> ElevatorRequest

Elevator --> Direction
Elevator --> ElevatorState

Elevator --> ElevatorObserver

ElevatorObserver <|.. ElevatorDisplay

SchedulingStrategy <|.. FCFSSchedulingStrategy
SchedulingStrategy <|.. ScanSchedulingStrategy
SchedulingStrategy <|.. LookSchedulingStrategy

ElevatorController --> SchedulingStrategy
```

---

# Sequence Diagram

## External Request Flow

Example:

* User is at Floor 5
* Presses UP button
* Controller assigns Elevator 1

```mermaid
sequenceDiagram

actor User

participant Controller
participant Elevator
participant Strategy
participant Display

User->>Controller: Request Elevator(Floor 5, UP)

Controller->>Elevator: addRequest()

Controller->>Strategy: getNextStop()

Strategy-->>Controller: Floor 5

Controller->>Elevator: moveTo(5)

Elevator->>Display: Floor Changed

Elevator->>Display: State Changed

Elevator-->>User: Door Opens
```

---

# State Diagram

## Elevator Lifecycle

```mermaid
stateDiagram-v2

[*] --> IDLE

IDLE --> MOVING : New Request

MOVING --> STOPPED : Reached Floor

STOPPED --> MOVING : More Requests

STOPPED --> IDLE : Queue Empty

IDLE --> MAINTENANCE : Maintenance Mode

MAINTENANCE --> IDLE : Maintenance Completed
```

---

# High-Level Architecture

```mermaid
flowchart TD

User[User]

External[Floor Panel]
Internal[Elevator Panel]

Controller[Elevator Controller]

Strategy[Scheduling Strategy]

FCFS[FCFS]
SCAN[SCAN]
LOOK[LOOK]

E1[Elevator 1]
E2[Elevator 2]
E3[Elevator N]

Display[Display / Monitoring]

User --> External
User --> Internal

External --> Controller
Internal --> Controller

Controller --> Strategy

Strategy --> FCFS
Strategy --> SCAN
Strategy --> LOOK

Controller --> E1
Controller --> E2
Controller --> E3

E1 --> Display
E2 --> Display
E3 --> Display
```

---

# Design Pattern UML

## Strategy Pattern

```mermaid
classDiagram

class SchedulingStrategy{
    <<interface>>
    +getNextStop()
}

class FCFSSchedulingStrategy
class ScanSchedulingStrategy
class LookSchedulingStrategy

SchedulingStrategy <|.. FCFSSchedulingStrategy
SchedulingStrategy <|.. ScanSchedulingStrategy
SchedulingStrategy <|.. LookSchedulingStrategy
```

---

## Observer Pattern

```mermaid
classDiagram

class Elevator{
    +notifyObservers()
}

class ElevatorObserver{
    <<interface>>
    +onFloorChange()
    +onStateChange()
}

class ElevatorDisplay

Elevator --> ElevatorObserver

ElevatorObserver <|.. ElevatorDisplay
```

---

## Command Pattern

```mermaid
classDiagram

class ElevatorRequest{
    floor
    direction
    isInternalRequest
}

class ElevatorController

class Elevator

ElevatorController --> ElevatorRequest
ElevatorController --> Elevator
```
