# ATM Cash Dispensing System - UML Diagram

## Class Diagram (PlantUML)

```plantuml
@startuml

'========================
' Abstract Handler
'========================
abstract class MoneyHandler {
    - nextHandler : MoneyHandler
    + setNextHandler(next : MoneyHandler)
    + dispense(amount : int)
}

'========================
' Concrete Handlers
'========================
class ThousandHandler {
    - numNotes : int
    + dispense(amount : int)
}

class FiveHundredHandler {
    - numNotes : int
    + dispense(amount : int)
}

class HundredHandler {
    - numNotes : int
    + dispense(amount : int)
}

'========================
' Inheritance
'========================
MoneyHandler <|-- ThousandHandler
MoneyHandler <|-- FiveHundredHandler
MoneyHandler <|-- HundredHandler

'========================
' Chain Relationship
'========================
MoneyHandler --> MoneyHandler : nextHandler

@enduml
```

---

## UML Diagram (Mermaid)

```mermaid
classDiagram

class MoneyHandler{
    <<abstract>>
    -MoneyHandler nextHandler
    +setNextHandler(MoneyHandler)
    +dispense(amount)
}

class ThousandHandler{
    -int numNotes
    +dispense(amount)
}

class FiveHundredHandler{
    -int numNotes
    +dispense(amount)
}

class HundredHandler{
    -int numNotes
    +dispense(amount)
}

MoneyHandler <|-- ThousandHandler
MoneyHandler <|-- FiveHundredHandler
MoneyHandler <|-- HundredHandler

MoneyHandler --> MoneyHandler : nextHandler
```

---

# Sequence Diagram

```mermaid
sequenceDiagram

participant Client
participant ThousandHandler
participant FiveHundredHandler
participant HundredHandler

Client->>ThousandHandler: dispense(4600)

ThousandHandler->>ThousandHandler: Dispense 3 × ₹1000
ThousandHandler->>FiveHundredHandler: dispense(1600)

FiveHundredHandler->>FiveHundredHandler: Dispense 3 × ₹500
FiveHundredHandler->>HundredHandler: dispense(100)

HundredHandler->>HundredHandler: Dispense 1 × ₹100

HundredHandler-->>Client: Withdrawal Complete
```

---

# Chain of Responsibility Flow

```text
Client
   |
   v
+---------------+
| ₹1000 Handler |
+---------------+
        |
        v
+---------------+
| ₹500 Handler  |
+---------------+
        |
        v
+---------------+
| ₹100 Handler  |
+---------------+
```

## Design Pattern Used

### Chain of Responsibility

**Intent:**
Pass a withdrawal request through a chain of handlers. Each handler processes as much of the amount as possible and forwards the remaining amount to the next handler.

### Benefits

* Loose coupling between sender and receiver.
* Easy to add new denominations.
* Follows Open/Closed Principle.
* Handlers can be reordered dynamically.

### Example

Withdrawal Amount = ₹4600

1. ₹1000 Handler → Dispense 3 notes → Remaining ₹1600
2. ₹500 Handler → Dispense 3 notes → Remaining ₹100
3. ₹100 Handler → Dispense 1 note → Remaining ₹0

Output:

```text
Dispensing 3 x ₹1000 notes
Dispensing 3 x ₹500 notes
Dispensing 1 x ₹100 notes
```
