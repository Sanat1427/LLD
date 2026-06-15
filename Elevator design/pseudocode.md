# Elevator System - Complete Pseudocode 

```text
/******************************************************************
 * ELEVATOR SYSTEM DESIGN
 *
 * Goal:
 * Efficiently manage multiple elevators in a building.
 *
 * Features:
 * 1. Multiple Elevators
 * 2. Multiple Floors
 * 3. Internal Requests
 * 4. External Requests
 * 5. Different Scheduling Algorithms
 * 6. Real-Time Display Updates
 *
 * Design Patterns:
 * 1. Observer Pattern  -> Display Updates
 * 2. Command Pattern   -> ElevatorRequest
 * 3. Strategy Pattern  -> FCFS / SCAN / LOOK
 *****************************************************************/


/******************************************************************
 * ENUMS
 ******************************************************************/

// Direction in which elevator can move
ENUM Direction
{
    UP,
    DOWN,
    IDLE
}


// Current operational state of elevator
ENUM ElevatorState
{
    IDLE,          // Waiting for requests
    MOVING,        // Currently moving
    STOPPED,       // Reached a floor
    MAINTENANCE    // Out of service
}



/******************************************************************
 * COMMAND PATTERN
 *
 * Every request becomes an object.
 *
 * Example:
 * Floor 5 UP
 * Floor 10 DOWN
 * Internal Floor 15
 *
 ******************************************************************/

CLASS ElevatorRequest
{
    floor

    direction

    isInternalRequest

    CONSTRUCTOR(floor,direction,isInternalRequest)
    {
        this.floor = floor

        this.direction = direction

        this.isInternalRequest = isInternalRequest
    }
}



/******************************************************************
 * OBSERVER PATTERN
 *
 * Display panels should automatically update whenever
 * elevator changes floor or state.
 *
 ******************************************************************/

INTERFACE ElevatorObserver
{
    onFloorChange(elevator,floor)

    onStateChange(elevator,state)
}


/******************************************************************
 * DISPLAY PANEL
 *
 * Concrete Observer
 *
 ******************************************************************/

CLASS ElevatorDisplay IMPLEMENTS ElevatorObserver
{
    onFloorChange(elevator,floor)
    {
        PRINT
        "Elevator",
        elevator.id,
        "Reached Floor",
        floor
    }

    onStateChange(elevator,state)
    {
        PRINT
        "Elevator",
        elevator.id,
        "Changed State To",
        state
    }
}



/******************************************************************
 * ELEVATOR
 *
 * Core entity of the system.
 *
 * Responsibilities:
 * 1. Store current position.
 * 2. Store pending requests.
 * 3. Move between floors.
 * 4. Notify observers.
 * 5. Maintain state.
 *
 ******************************************************************/

CLASS Elevator
{
    id

    currentFloor

    direction

    state

    requestQueue

    observers



    CONSTRUCTOR(id)
    {
        this.id = id

        currentFloor = 1

        direction = IDLE

        state = IDLE

        requestQueue = EMPTY_QUEUE

        observers = EMPTY_LIST
    }



    /**************************************************************
     * Register Display / Monitoring Systems
     **************************************************************/
    METHOD addObserver(observer)
    {
        observers.ADD(observer)
    }



    /**************************************************************
     * Notify all observers whenever floor changes
     **************************************************************/
    METHOD notifyFloorChange()
    {
        FOR EACH observer IN observers
        {
            observer.onFloorChange(
                this,
                currentFloor
            )
        }
    }



    /**************************************************************
     * Notify all observers whenever state changes
     **************************************************************/
    METHOD notifyStateChange()
    {
        FOR EACH observer IN observers
        {
            observer.onStateChange(
                this,
                state
            )
        }
    }



    /**************************************************************
     * Add New Request
     *
     * If elevator is idle,
     * determine initial direction.
     **************************************************************/
    METHOD addRequest(request)
    {
        requestQueue.ADD(request)

        IF state == IDLE
        {
            IF request.floor > currentFloor
                direction = UP

            ELSE
                direction = DOWN

            state = MOVING

            notifyStateChange()
        }
    }



    /**************************************************************
     * Move Elevator To Target Floor
     *
     * Example:
     * Current Floor = 3
     * Target Floor  = 7
     *
     * 3 -> 4 -> 5 -> 6 -> 7
     **************************************************************/
    METHOD moveTo(nextFloor)
    {
        WHILE currentFloor != nextFloor
        {
            IF direction == UP
            {
                currentFloor++
            }
            ELSE
            {
                currentFloor--
            }

            notifyFloorChange()
        }

        completeArrival()
    }



    /**************************************************************
     * Called when elevator reaches destination
     **************************************************************/
    METHOD completeArrival()
    {
        state = STOPPED

        notifyStateChange()



        // Remove completed request

        REMOVE ALL REQUESTS
        WHERE request.floor == currentFloor



        // No more work left

        IF requestQueue IS EMPTY
        {
            direction = IDLE

            state = IDLE
        }

        // More requests pending

        ELSE
        {
            state = MOVING
        }

        notifyStateChange()
    }
}



/******************************************************************
 * STRATEGY PATTERN
 *
 * Allows changing scheduling algorithm at runtime.
 *
 ******************************************************************/

INTERFACE SchedulingStrategy
{
    getNextStop(elevator)
}



/******************************************************************
 * FCFS STRATEGY
 *
 * First Come First Serve
 *
 * Example:
 *
 * Queue:
 * 10
 * 5
 * 15
 *
 * Output:
 * 10 -> 5 -> 15
 *
 ******************************************************************/

CLASS FCFSSchedulingStrategy
IMPLEMENTS SchedulingStrategy
{
    METHOD getNextStop(elevator)
    {
        IF elevator.requestQueue IS EMPTY
            RETURN elevator.currentFloor

        RETURN FIRST_REQUEST.floor
    }
}



/******************************************************************
 * SCAN STRATEGY
 *
 * Elevator continues serving requests
 * in current direction.
 *
 * Only reverses direction after all requests
 * in that direction are completed.
 *
 * Example:
 *
 * Current Floor = 10
 * Direction = DOWN
 *
 * Requests:
 * 8
 * 7
 * 5
 * 2
 *
 * Execution:
 *
 * 10 -> 8 -> 7 -> 5 -> 2
 *
 ******************************************************************/

CLASS ScanSchedulingStrategy
IMPLEMENTS SchedulingStrategy
{
    METHOD getNextStop(elevator)
    {
        currentFloor =
            elevator.currentFloor


        CREATE upQueue

        CREATE downQueue



        // Divide requests into
        // upward and downward buckets

        FOR EACH request
        IN elevator.requestQueue
        {
            IF request.floor > currentFloor
                upQueue.ADD(request.floor)

            ELSE
                downQueue.ADD(request.floor)
        }



        // Continue moving UP

        IF elevator.direction == UP
        {
            IF upQueue NOT EMPTY
            {
                RETURN SMALLEST(upQueue)
            }

            elevator.direction = DOWN

            RETURN LARGEST(downQueue)
        }



        // Continue moving DOWN

        ELSE
        {
            IF downQueue NOT EMPTY
            {
                RETURN LARGEST(downQueue)
            }

            elevator.direction = UP

            RETURN SMALLEST(upQueue)
        }
    }
}



/******************************************************************
 * LOOK STRATEGY
 *
 * Optimized SCAN
 *
 * Instead of travelling till building end,
 * it only travels till last pending request.
 *
 * Example:
 *
 * Current Floor = 5
 *
 * Requests:
 * 8
 * 10
 * 12
 *
 * Execution:
 *
 * 5 -> 8 -> 10 -> 12
 *
 * Reverse Immediately
 *
 ******************************************************************/

CLASS LookSchedulingStrategy
IMPLEMENTS SchedulingStrategy
{
    METHOD getNextStop(elevator)
    {
        currentFloor =
            elevator.currentFloor


        primaryRequest =
            FIRST_REQUEST(
                elevator.requestQueue
            )


        primaryFloor =
            primaryRequest.floor


        candidate = NULL



        // Elevator moving UP

        IF primaryFloor > currentFloor
        {
            FOR EACH request
            IN elevator.requestQueue
            {
                IF request.floor > currentFloor
                   AND request.floor <= primaryFloor
                {
                    IF candidate IS NULL
                       OR request.floor < candidate
                    {
                        candidate =
                            request.floor
                    }
                }
            }
        }



        // Elevator moving DOWN

        ELSE
        {
            FOR EACH request
            IN elevator.requestQueue
            {
                IF request.floor < currentFloor
                   AND request.floor >= primaryFloor
                {
                    IF candidate IS NULL
                       OR request.floor > candidate
                    {
                        candidate =
                            request.floor
                    }
                }
            }
        }



        IF candidate EXISTS
            RETURN candidate


        RETURN primaryFloor
    }
}



/******************************************************************
 * FLOOR
 *
 * Represents one floor in building.
 *
 ******************************************************************/

CLASS Floor
{
    floorNumber

    CONSTRUCTOR(number)
    {
        floorNumber = number
    }
}



/******************************************************************
 * ELEVATOR CONTROLLER
 *
 * Brain Of The System
 *
 * Responsibilities:
 * 1. Manage all elevators.
 * 2. Receive requests.
 * 3. Apply scheduling strategy.
 * 4. Simulate movement.
 *
 ******************************************************************/

CLASS ElevatorController
{
    elevators

    floors

    schedulingStrategy



    CONSTRUCTOR(
        numberOfElevators,
        numberOfFloors
    )
    {
        elevators = EMPTY_LIST

        floors = EMPTY_LIST



        // Default Scheduling Algorithm

        schedulingStrategy =
            new ScanSchedulingStrategy()



        FOR i = 1 TO numberOfElevators
        {
            elevators.ADD(
                new Elevator(i)
            )
        }



        FOR i = 1 TO numberOfFloors
        {
            floors.ADD(
                new Floor(i)
            )
        }
    }



    METHOD setSchedulingStrategy(strategy)
    {
        schedulingStrategy = strategy
    }



    /**************************************************************
     * External Request
     *
     * Example:
     * Floor 5 presses UP
     **************************************************************/
    METHOD requestElevator(
        elevatorId,
        floor,
        direction
    )
    {
        elevator =
            getElevatorById(
                elevatorId
            )

        request =
            new ElevatorRequest(
                floor,
                direction,
                FALSE
            )

        elevator.addRequest(request)
    }



    /**************************************************************
     * Internal Request
     *
     * Example:
     * Passenger presses Floor 12
     **************************************************************/
    METHOD requestFloor(
        elevatorId,
        floor
    )
    {
        elevator =
            getElevatorById(
                elevatorId
            )

        direction =
            floor > elevator.currentFloor
            ? UP
            : DOWN


        request =
            new ElevatorRequest(
                floor,
                direction,
                TRUE
            )

        elevator.addRequest(request)
    }



    METHOD getElevatorById(id)
    {
        FOR EACH elevator IN elevators
        {
            IF elevator.id == id
                RETURN elevator
        }

        RETURN NULL
    }



    /**************************************************************
     * Simulation Tick
     *
     * Each step:
     * 1. Choose next stop.
     * 2. Move elevator.
     **************************************************************/
    METHOD step()
    {
        FOR EACH elevator IN elevators
        {
            IF elevator.requestQueue
               NOT EMPTY
            {
                nextStop =
                    schedulingStrategy
                    .getNextStop(
                        elevator
                    )

                elevator.moveTo(
                    nextStop
                )
            }
        }
    }
}



/******************************************************************
 * BUILDING
 *
 * Top Level Object
 *
 ******************************************************************/

CLASS Building
{
    name

    totalFloors

    controller



    CONSTRUCTOR(
        name,
        totalFloors,
        totalElevators
    )
    {
        this.name = name

        this.totalFloors =
            totalFloors


        controller =
            new ElevatorController(
                totalElevators,
                totalFloors
            )
    }
}



/******************************************************************
 * MAIN
 *
 * Complete Flow
 *
 ******************************************************************/

building =
    new Building(
        "Office Tower",
        20,
        4
    )

controller =
    building.controller


display =
    new ElevatorDisplay()


FOR EACH elevator
IN controller.elevators
{
    elevator.addObserver(
        display
    )
}



/**************************************************************
 * EXTERNAL REQUEST
 *
 * User at Floor 5
 * presses UP button.
 **************************************************************/

controller.requestElevator(
    elevatorId = 1,
    floor = 5,
    direction = UP
)



/**************************************************************
 * INTERNAL REQUEST
 *
 * Passenger enters elevator
 * and selects Floor 12.
 **************************************************************/

controller.requestFloor(
    elevatorId = 1,
    floor = 12
)



/**************************************************************
 * CONTINUOUS SYSTEM LOOP
 **************************************************************/

WHILE TRUE
{
    controller.step()
}
```
