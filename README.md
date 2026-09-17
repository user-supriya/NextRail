# NextRail

## AI-Assisted Railway Traffic Management System

NextRail is a railway traffic management and scheduling system that combines an interactive railway simulation with priority-based scheduling, delay management, conflict resolution, signal simulation, and backend data storage.

The project models trains, stations, tracks, signals, schedules, and delays, and provides scheduling logic to resolve conflicts while maintaining configurable headway constraints.

---

## Overview

Railway networks can experience scheduling conflicts when multiple trains approach the same station or corridor within a short time interval. Delays can also propagate through subsequent stations and affect other trains.

NextRail provides a simulation and scheduling environment that:

* Models railway stations, tracks, signals, and trains.
* Represents train schedules, priorities, destinations, speeds, and delays.
* Detects and handles scheduling conflicts.
* Applies configurable headway constraints between train movements.
* Uses train priority to determine which train receives precedence during conflicts.
* Propagates schedule adjustments to subsequent stations.
* Simulates railway signal availability.
* Calculates and compares schedule throughput.
* Provides a FastAPI backend for scheduling and data operations.
* Integrates with PocketBase for storing train, delay, result, and schedule data.

---

## Key Features

### 1. Railway Traffic Simulation

The frontend simulation models:

* Stations and platforms
* Railway tracks
* Signals
* Passenger and freight trains
* Train priorities
* Train positions and speeds
* Train destinations
* Train schedules
* Train delays
* Train operating states

The simulation updates train movement and signal conditions based on the current simulation state.

### 2. Priority-Based Scheduling

Trains are assigned numerical priority levels, where a lower priority number represents a higher scheduling priority.

When two trains conflict, the scheduling engine uses train priority to determine which train receives precedence.

If two trains have the same priority, their originally scheduled time is used as the tie-breaker.

### 3. Headway-Constrained Conflict Resolution

The scheduling engine accepts a configurable headway value representing the minimum required time gap between conflicting train movements.

When the gap between two arrivals is smaller than the required headway:

1. The conflicting trains are identified.
2. Their priorities are compared.
3. The higher-priority train receives precedence.
4. The other train is shifted forward.
5. The schedules are re-evaluated.

The algorithm repeatedly scans the schedules after changes so that cascading conflicts can also be handled.

### 4. Delay Propagation

Input delays can be applied to individual trains.

When a train's schedule is shifted because of a conflict, the corresponding subsequent station timings are shifted as well.

This allows the system to represent how scheduling changes can propagate through a train's journey.

### 5. Signal Simulation

NextRail includes signal simulation based on rescheduled train departure times.

The simulation represents:

* Red signals when a train needs to wait.
* Green signals when the required movement becomes available.

This provides a simple visualization of signal availability during the scheduling process.

### 6. Throughput Calculation

The system calculates schedule throughput in trains per hour.

It determines:

* Number of trains
* Schedule time span
* Throughput before scheduling
* Throughput after scheduling
* Change in calculated throughput

This provides a way to compare the resulting schedules.

### 7. FastAPI Backend

The project contains a Python FastAPI backend for handling scheduling and data operations.

The backend provides endpoints for:

* Generating/rescheduling train schedules.
* Uploading data to PocketBase.

The scheduling backend processes train schedules, delays, priorities, and headway constraints before generating the final timetable.

### 8. PocketBase Integration

PocketBase is used for storing project data.

The system can send:

* Train records
* Delay records
* Per-train scheduling results
* Complete scheduling results

The frontend/backend architecture communicates with the data layer through HTTP requests.

---

## Scheduling Workflow

The scheduling process follows this general workflow:

```text
Train Schedules
       |
       v
Apply Input Delays
       |
       v
Group Train Arrivals by Station
       |
       v
Check Headway Constraints
       |
       v
Detect Scheduling Conflicts
       |
       v
Compare Train Priorities
       |
       v
Resolve Conflicts
       |
       v
Propagate Schedule Changes
       |
       v
Generate Final Timetable
       |
       v
Calculate Throughput
```

---

## Conflict Resolution Logic

The core scheduling algorithm uses priority-first conflict resolution.

For a scheduling conflict:

```text
If time gap >= required headway
        |
        v
    No conflict

If time gap < required headway
        |
        v
 Compare train priorities
        |
        +---- Higher priority → receives precedence
        |
        +---- Lower priority → shifted forward
        |
        v
Apply required headway
        |
        v
Re-check schedules
        |
        v
Handle cascading conflicts
```

When priorities are equal, the earlier originally scheduled time is used as the tie-breaker.

---

## System Architecture

NextRail consists of a frontend simulation layer, a Python backend, and a PocketBase data layer.

```text
                NextRail Frontend
              React + TypeScript
                       |
                       | HTTP Requests
                       v
                FastAPI Backend
                    Python
                       |
             +---------+---------+
             |                   |
             v                   v
       Scheduling Engine     PocketBase
       Conflict Resolution    Data Storage
       Delay Propagation
       Throughput Calculation
```

---

## Technologies Used

### Frontend

* React
* TypeScript
* Vite
* React Router
* React Leaflet
* Recharts
* Tailwind CSS

### Backend

* Python
* FastAPI
* Pydantic
* HTTPX

### Data Storage

* PocketBase

### Development Tools

* Git
* GitHub
* npm
* ESLint

---

## Project Structure

```text
NextRail/
│
├── .github/
│
├── fastapi_backend/
│   └── FastAPI backend and scheduling logic
│
├── public/
│   └── Public frontend assets
│
├── src/
│   ├── components/
│   ├── hooks/
│   ├── lib/
│   ├── pages/
│   ├── types/
│   └── application source files
│
├── ALTRAIN
│   └── Python scheduling and railway simulation logic
│
├── README.md
├── package.json
├── package-lock.json
├── vite.config.ts
├── tailwind.config.ts
├── eslint.config.js
├── postcss.config.js
└── LICENSE
```

---

## Important Backend Operations

### Schedule Generation

The FastAPI backend exposes a scheduling operation that accepts a headway constraint and processes train scheduling information.

The scheduling engine can:

* Apply train delays.
* Calculate effective scheduling priority.
* Detect station-level conflicts.
* Resolve conflicts using priority.
* Apply headway constraints.
* Propagate schedule changes.
* Generate final schedules.
* Calculate throughput.

### PocketBase Upload

The backend also provides an operation for uploading application data to PocketBase.

The project can store data associated with:

```text
trains
delays
results
schedule
```

---

## Example Scheduling Scenario

Consider several trains approaching the same station.

If two trains are scheduled closer together than the configured headway:

```text
Train A ────────────────>
              06:00

Train B ────────────────>
              06:05
```

With a 10-minute headway, the two movements conflict.

The scheduling engine compares their priorities.

If Train A has higher priority:

```text
Train A → 06:00
Train B → 06:10
```

The change is then propagated to the affected subsequent schedule entries for Train B.

The final timetable can subsequently be evaluated for throughput.

---

## Interactive Scheduling Demo

The Python scheduling component supports interactive input for:

* Selecting a train.
* Entering a delay in minutes.
* Configuring the required headway.

The program then:

1. Builds the delayed base schedules.
2. Resolves scheduling conflicts.
3. Simulates signals.
4. Prints the complete corridor timetable.
5. Calculates throughput before scheduling.
6. Calculates throughput after scheduling.
7. Reports the calculated throughput change.
8. Sends relevant data to PocketBase.

---

## AI-Assisted Control

The project also contains an AI-assisted traffic control layer based on rule-driven scheduling heuristics.

The controller considers factors such as:

* Train type
* Train priority
* Current delay
* Estimated completion time
* Platform conflicts
* Signal conditions

The current implementation uses **heuristic/rule-based decision logic rather than a trained machine-learning model**.

This approach provides an interpretable scheduling mechanism where decisions can be traced back to defined scheduling rules.

---

## Running the Project

### Frontend

Install the project dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

The Vite development server will provide the local URL for the frontend.

### FastAPI Backend

Navigate to the backend directory:

```bash
cd fastapi_backend
```

Install the required Python dependencies according to the backend configuration.

Then start the FastAPI application using an ASGI server such as:

```bash
uvicorn main:app --reload
```

The backend runs locally and can be configured through environment variables.

---

## Environment Configuration

The frontend can use an environment variable to specify the FastAPI backend URL.

Supported configuration includes:

```text
VITE_FASTAPI_URL
```

The backend can also be configured with a PocketBase URL through its environment configuration.

For local development, the project supports local backend/data-service URLs.

---

## API Endpoints

### `POST /schedule`

Used to process train scheduling information and generate rescheduled timetables.

The scheduling process considers:

* Train schedules
* Delays
* Train priorities
* Headway constraints

The response contains scheduling information including:

* Base schedules
* Final schedules
* Throughput
* Number of trains
* Schedule span

### `POST /upload_to_pocketbase`

Used to send application data to PocketBase.

The request contains:

```json
{
  "collection": "trains",
  "data": {}
}
```

The endpoint forwards the data to the corresponding PocketBase collection.

---

## Design Approach

The project focuses on combining:

* Simulation
* Scheduling algorithms
* Conflict resolution
* Delay management
* Backend APIs
* Persistent data storage

The scheduling logic is designed to remain deterministic and interpretable, making it possible to understand why a particular train receives precedence during a conflict.

---

## Future Improvements

Potential future improvements include:

* More advanced railway routing algorithms.
* Improved real-time scheduling.
* More detailed station and platform constraints.
* Advanced conflict prediction.
* Integration with live railway data.
* Machine-learning-based delay prediction.
* More sophisticated optimization techniques.
* Automated testing for scheduling scenarios.
* Improved deployment configuration.

---

## License

This project is licensed under the terms specified in the repository's `LICENSE` file.
