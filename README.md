# Group7_FinalProject

---

# Class Scheduling System

Graph Theory Final Project

## 1. Project Overview

This project solves the university class scheduling problem by modeling it as a bipartite graph matching problem, where:
- Courses represent left-side vertices
- Room–TimeSlot combinations represent right-side vertices
- Edges connect a course to every feasible room–slot (capacity fits)
- A maximum matching algorithm assigns each course to exactly one valid room and time

The system ensures:
- No double-booking of rooms
- Capacity constraints are respected
- If a slot is full, the algorithm finds an alternative slot automatically
- Course can be shifted intelligently using augmenting paths

---

## 2. Methodology

### Graph Representation

We represent the problem as a bipartite graph:

**Left Partition (U)**

All courses:

`C1, C2, …, Cn`

**Right Partition (V)**

All RoomSlot nodes (Room × TimeSlot):

```
RoomSlotID = slot * ROOMS + roomIndex
Total = 20 time slots × 8 rooms = 160 nodes
```
**Edges**

An edge exists if:

- The room capacity ≥ course capacity
- The course can be placed in that slot
- Priority is assigned based on:
  - Preferred slot first
  - Closest slot next
  - Minimal unused capacity last

This produces a weighted preference list for matching.

### Scheduling Logic

1. User inputs course name, capacity, day, and session.
2. Convert the preferred day/session into a slot (0–19).
3. For each course, generate edges to all feasible room–slot nodes
4. Sort edges by priority:
   - Preferred slot
   - Minimal slot distance
   - Minimal room waste
5. Apply Maximum Bipartite Matching (Kuhn DFS):
   - If a preferred slot is full, the algorithm tries to rearrange other courses
   - If rearrangement is not possible, it moves the course to another optimal slot
6. Final schedule is guaranteed to contain all courses assigned without conflict.

---

## 3. Comparison Between Graph Coloring Scheduling and Bipartite Matching Scheduling

### Graph Coloring Scheduling

The graph coloring approach treats each course as a vertex and every declared conflict between two courses as an edge. Two adjacent vertices cannot share the same color, meaning the corresponding courses cannot be scheduled in the same time slot.

The algorithm proceeds by:
1. Building a conflict graph using an adjacency matrix.
2. Computing the degree of each course to identify how many other courses it conflicts with.
3. Sorting courses by decreasing degree, ensuring the most constrained courses are scheduled first.
4. Assigning colors (time slots):
   - The first course receives color 1
   - Each next course receives the lowest available color that is not used by any of its neighbors
   - If none is available, a new color is created

The final result is a set of colors where each color represents a valid timeslot with no conflicts.

This method guarantees a conflict-free schedule but does not consider room capacity, room availability, or preferred times—it only prevents overlapping between conflicting courses.

### Comparison Table

| Feature                  | Graph Coloring                   | Bipartite Matching                  |
| ------------------------ | -------------------------------- | ----------------------------------- |
| Graph Type               | Conflict graph (Course ↔ Course) | Bipartite graph (Course ↔ RoomSlot) |
| Input Needed             | Conflict pairs only              | Course capacity, preferred time     |
| Output                   | Timeslot only                    | Timeslot + Room                     |
| Handles Room Capacity    | No                             | Yes                               |
| Resolves Room Conflicts  | No                             | Yes                               |
| Auto-Rescheduling        | No                             | Augmenting path                   |
| Optimization             | Min colors (min slots)           | Min waste + respect preferred slot  |
| Real-world Applicability | Medium                           | Very High                           |


---

## 4. Workflow

### Step 1: Input Data

User inputs:
- Course name
- Course capacity
- Day (1–5)
- Session (1–4)

### Step 2: Convert to Time Slot

The system calculates the slot index:

```
slot = (day - 1) * 4 + (session - 1)
```

### Step 3: Build Graph Edges

For each course:
- Connect to all room-slots that meet capacity
- Assign priority → later used in matching

### Step 4: Run Matching

- DFS-based augmenting path algorithm
- Produces a maximal matching
- Automatically performs rescheduling when conflicts arise
  
### Step 5: Output Timetable

Includes warnings when a class is moved from its preferred time.

---

## 5. Pseudocode

```
Input course list

For each course:
    preferred_slot = convert(day, session)
    Generate all feasible RoomSlot edges
    Assign priority to each edge
    Sort edges ascending by priority

Run BipartiteMatching:
    For each course:
        DFS attempt to match course to best room-slot
        If slot full, try to rearrange others via augmenting path

After matching:
    Convert RoomSlotID → day/session/room
    Print warnings for moved courses
    Print final schedule
```

---

## 6. How to Use

### Input Format

For each course:
```
Course name:
Capacity:
Day (1–5):
Session (1–4):
```
Example :
```
GraphTheory
40
1
1

```
The algorithm will:
- Try the preferred time
- Find optimal room
- Move the class if necessary

---

## 7. Example Scenario

### Scenario A (Simple Case)

Input :
```
3
TEGRAF
40
1
1
JARKOM
30
2
2
MATDIS
50
3
1
```

Output:

```
----- FINAL SCHEDULE -----
TEGRAF | Day 1 Session 1 | Room IF-108 (cap 40)
JARKOM | Day 2 Session 2 | Room IF-106 (cap 33)
MATDIS | Day 3 Session 1 | Room IF-101 (cap 50)
```

### Scenario B (Moderate Case)

Input :
```
4
TEGRAF
40
1
1
JARKOM
80
1
1
MATDIS
33
1
1
PBO
50
2
4
```

Output:

```
----- FINAL SCHEDULE -----
TEGRAF | Day 1 Session 1 | Room IF-108 (cap 40)
JARKOM | Day 1 Session 1 | Room IF-107 (cap 80)
MATDIS | Day 1 Session 1 | Room IF-106 (cap 33)
PBO | Day 2 Session 4 | Room IF-101 (cap 50)
```

### Scenario C (Complex Case)

Input
```
10
TEGRAF
60
1
1
JARKOM
80
1
1
MATDIS
40
1
1
SISOP
50
1
1
PBO
33
1
1
PWEB
100
2
2
STRUKDAT
45
3
3
DASPROG
70
1
1
ALIN
40
1
1
KALKULUS
60
5
4
```

Output :
```
TEGRAF moved from Day 1 Session 1 -> Day 1 Session 2

----- FINAL SCHEDULE -----
TEGRAF | Day 1 Session 2 | Room IF-107 (cap 80)
JARKOM | Day 1 Session 1 | Room IF-105 (cap 100)
MATDIS | Day 1 Session 1 | Room IF-102 (cap 50)
SISOP | Day 1 Session 1 | Room IF-101 (cap 50)
PBO | Day 1 Session 1 | Room IF-106 (cap 33)
PWEB | Day 2 Session 2 | Room IF-105 (cap 100)
STRUKDAT | Day 3 Session 3 | Room IF-101 (cap 50)
DASPROG | Day 1 Session 1 | Room IF-107 (cap 80)
ALIN | Day 1 Session 1 | Room IF-108 (cap 40)
KALKULUS | Day 5 Session 4 | Room IF-107 (cap 80)
```

---

## 8. Dataset

The dataset used in this project consists of two main components:

### 1. Room Dataset

Contains all available classrooms, each with a fixed capacity.
This dataset is static and represents real scheduling constraints.

| Room Name | Capacity |
| --------- | -------- |
| IF-101    | 50       |
| IF-102    | 50       |
| IF-103    | 50       |
| IF-104    | 50       |
| IF-105    | 100      |
| IF-106    | 33       |
| IF-107    | 80       |
| IF-108    | 40       |

Total rooms: 8

Used for all 20 timeslots: 20 × 8 = 160 RoomSlot nodes

### 2. Course Dataset
Contains test inputs used to evaluate the scheduling algorithm.

Each course includes:

- Course Name
- Required Capacity
- Preferred Day (1–5)
- Preferred Session (1–4)

The dataset is designed to test multiple scenarios, including:

- Light load (no conflicts)
- Medium load (partial conflicts)
- Heavy load (multiple courses requesting the same slot)

**Dataset A — Small / No Conflict Case**
| Course | Capacity | Day | Session |
| ------ | -------- | --- | ------- |
| TEGRAF | 40       | 1   | 1       |
| JARKOM | 30       | 2   | 2       |
| MATDIS | 50       | 3   | 1       |

**Dataset B — Moderate Conflict**
| Course | Capacity | Day | Session |
| ------ | -------- | --- | ------- |
| TEGRAF | 40       | 1   | 1       |
| JARKOM | 80       | 1   | 1       |
| MATDIS | 33       | 1   | 1       |
| PBO    | 50       | 2   | 4       |

**Dataset C — Heavy Conflict**
| Course   | Capacity | Day | Session |
| -------- | -------- | --- | ------- |
| TEGRAF   | 60       | 1   | 1       |
| JARKOM   | 80       | 1   | 1       |
| MATDIS   | 40       | 1   | 1       |
| SISOP    | 50       | 1   | 1       |
| PBO      | 33       | 1   | 1       |
| PWEB     | 100      | 2   | 2       |
| STRUKDAT | 45       | 3   | 3       |
| DASPROG  | 70       | 1   | 1       |
| ALIN     | 40       | 1   | 1       |
| KALKULUS | 60       | 5   | 4       |


---

## 9. Source Code

Final Implementation

```C

#include <stdio.h>
#include <string.h>

#define MAX 50
#define SLOTS 20
#define ROOMS 8
#define MAX_RNODES (SLOTS * ROOMS)

struct Room {
    char name[10];
    int capacity;
} rooms[ROOMS] = {
    {"IF-101", 50},
    {"IF-102", 50},
    {"IF-103", 50},
    {"IF-104", 50},
    {"IF-105", 100},
    {"IF-106", 33},
    {"IF-107", 80},
    {"IF-108", 40}
};

struct Course {
    char name[20];
    int capacity;
    int day;
    int session;
    int prefSlot;       
    int assignedRoom;
    int finalSlot;
} courses[MAX];

int adj[MAX][MAX_RNODES];
int adjCount[MAX];

int matchR[MAX_RNODES];    \
int visited[MAX_RNODES]; 

struct Edge {
    int nodeID;     
    int priority;   
};

struct Edge tempEdges[MAX_RNODES];
int tempCount;

int absVal(int x) { return x < 0 ? -x : x; }


int dfs(int c) {
    for (int i = 0; i < adjCount[c]; i++) {
        int v = adj[c][i]; 

        if (!visited[v]) {
            visited[v] = 1;

            if (matchR[v] == -1 || dfs(matchR[v])) {
                matchR[v] = c;
                return 1;
            }
        }
    }
    return 0;
}



int main() {
    int n;
    printf("Number of courses: ");
    scanf("%d", &n);

    for (int i = 0; i < n; i++) {
        printf("\nCourse %d name: ", i+1);
        scanf("%s", courses[i].name);

        printf("Capacity needed: ");
        scanf("%d", &courses[i].capacity);

        printf("Day (1-5): ");
        scanf("%d", &courses[i].day);

        printf("Session (1-4): ");
        scanf("%d", &courses[i].session);

        courses[i].prefSlot =
            (courses[i].day - 1) * 4 + (courses[i].session - 1);

        courses[i].assignedRoom = -1;
        courses[i].finalSlot = -1;
        adjCount[i] = 0;
    }

    for (int c = 0; c < n; c++) {
        int need = courses[c].capacity;
        int pref = courses[c].prefSlot;

        tempCount = 0;

        for (int slot = 0; slot < SLOTS; slot++) {
            int isPref = (slot == pref);
            int slotDist = absVal(slot - pref);

            for (int r = 0; r < ROOMS; r++) {

                if (rooms[r].capacity < need)
                    continue;   

                int waste = rooms[r].capacity - need;
                int id = slot * ROOMS + r;

                int priority =
                    (isPref ? waste : (10000 + slotDist * 200 + waste));

                tempEdges[tempCount].nodeID = id;
                tempEdges[tempCount].priority = priority;
                tempCount++;
            }
        }

        for (int i = 0; i < tempCount; i++) {
            for (int j = i + 1; j < tempCount; j++) {
                if (tempEdges[j].priority < tempEdges[i].priority) {
                    struct Edge t = tempEdges[i];
                    tempEdges[i] = tempEdges[j];
                    tempEdges[j] = t;
                }
            }
        }

        adjCount[c] = 0;
        for (int i = 0; i < tempCount; i++) {
            adj[c][adjCount[c]++] = tempEdges[i].nodeID;
        }
    }

    for (int i = 0; i < MAX_RNODES; i++) {
        matchR[i] = -1;
    }

    for (int c = 0; c < n; c++) {
        for (int i = 0; i < MAX_RNODES; i++)
            visited[i] = 0;

        dfs(c);
    }

    for (int v = 0; v < MAX_RNODES; v++) {
        if (matchR[v] != -1) {
            int c = matchR[v];

            int slot = v / ROOMS;
            int roomIndex = v % ROOMS;

            courses[c].finalSlot = slot;
            courses[c].assignedRoom = roomIndex;
        }
    }

    printf("\n");
    for (int c = 0; c < n; c++) {
        if (courses[c].finalSlot != courses[c].prefSlot) {
            int oldDay = courses[c].prefSlot / 4 + 1;
            int oldSess = courses[c].prefSlot % 4 + 1;

            int newDay = courses[c].finalSlot / 4 + 1;
            int newSess = courses[c].finalSlot % 4 + 1;

            printf("%s moved from Day %d Session %d -> Day %d Session %d\n",
                courses[c].name, oldDay, oldSess, newDay, newSess);
        }
    }

    printf("\n----- FINAL SCHEDULE -----\n");
    for (int c = 0; c < n; c++) {
        int slot = courses[c].finalSlot;
        int day = slot / 4 + 1;
        int session = slot % 4 + 1;

        printf("%s | Day %d Session %d | Room %s (cap %d)\n",
            courses[c].name,
            day,
            session,
            rooms[courses[c].assignedRoom].name,
            rooms[courses[c].assignedRoom].capacity
        );
    }

    return 0;
}

```

---

## 10. Conclusion

This project applies graph theory bipartite graph modeling and maximum matching to solve the university class scheduling problem. By treating courses and room–time slots as two distinct sets of vertices, the system ensures that each course is paired with exactly one valid room and schedule.

The use of the augmenting path algorithm (Kuhn’s Algorithm) is crucial, as it allows the system to automatically resolve conflicts when multiple courses request the same time slot. Instead of assigning rooms greedily, the algorithm can rearrange previous matches to create an optimal overall schedule. This guarantees that:

- No room is double-booked
- All courses receive valid placements
- Capacity constraints are upheld
- Preferred time slots are prioritized whenever possible

The priority-based sorting further improves practicality by selecting rooms with minimal wasted space and keeping courses close to their preferred time slots.

Overall, the system demonstrates how graph theory can be used to build a conflict-free, efficient, and fully automated scheduling solution, showing both the theoretical strength and real-world applicability of bipartite matching.

---
