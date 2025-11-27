# Group7_FinalProject

---

# Class Scheduling System

Graph Theory Final Project

## 1. Project Overview

This project focuses on solving the class scheduling problem by modeling it as a graph coloring problem. Each course is represented as a vertex, and an edge is created between two courses if they have a scheduling conflict, such as the same lecturer, the same classroom, or the same group of students. By applying the Welsh–Powell graph coloring algorithm, the system assigns time slots so that no conflicting classes overlap. The goal is to generate a conflict-free and efficient class timetable.

Reference: Group 7 Final Project Proposal


---

## 2. Methodology

### Graph Representation

* Vertices represent classes.
* Edges represent conflicts (same lecturer, same room, or same group of students).
* The objective is to assign a color to each vertex so that adjacent vertices do not share the same color. Each color corresponds to one time slot.

### Graph Coloring Concept

The project uses the Welsh–Powell Algorithm.
Steps:

1. Sort vertices in descending order based on their degree.
2. Color the highest-degree vertex first.
3. For each next vertex, assign the lowest color that does not conflict with its colored neighbors.
4. Continue until all vertices are assigned a color.

---

## 3. Workflow

### Step 1: Input Data

The system accepts course information including course name, lecturer, room, and any scheduling constraints.

### Step 2: Build Conflict Graph

A graph is created where each course is a vertex. An edge is added between two vertices if the courses cannot be held at the same time.

### Step 3: Sort Vertices by Degree

Courses with the highest number of conflicts are prioritized.

### Step 4: Apply Coloring Algorithm

The Welsh–Powell algorithm assigns time slots based on conflict patterns.

### Step 5: Generate Final Timetable

Each color is mapped to one available time slot.

---

## 4. Pseudocode

```
Input: List of courses with lecturers, rooms, and student groups
Output: Time slot assignment for each course

1. Build graph G where each course is a vertex
2. Add edges between vertices that have conflicts
3. Sort vertices in descending order of degree
4. Initialize color = 1
5. For each vertex v in sorted order:
       If v is not colored:
           Assign color to v
           For each other vertex u:
               If u is not colored and u is not adjacent to v:
                   Assign color to u
       Increase color by 1
6. Return color assignment for all vertices
```

---

## 5. How to Use

### Input Format

Provide a dataset containing:

* List of courses
* Lecturer for each course
* Room for each course
* Optional student-group information
* Conflict rules if any custom constraints apply


```
Course, Lecturer, Room
A, Dr. X, R101
B, Dr. X, R202
C, Dr. Y, R101
D, Dr. Z, R303
```

---

## 6. Example Output

### Scenario A (4 courses)

Courses: A, B, C, D
Conflicts: 3
Time slots used: 2
Chromatic number: 2

Output:

```
A: 1
B: 2
C: 2
D: 1
```

### Scenario B (8 courses)

Time slots used: 3

Example:

```
A: 1
B: 2
C: 1
D: 3
E: 2
F: 1
G: 3
H: 2
```

### Scenario C (12 courses)

Time slots used: 4

Example:

```
A: 1
B: 2
C: 3
D: 1
E: 2
F: 3
G: 4
H: 2
I: 3
J: 1
K: 2
L: 4
```

---

## 7. Dataset

Datasets may include small, medium, and complex scheduling cases (for example: 4, 8, and 12-course scenarios).
If your repository contains a dataset folder, link it here.


---

## 8. Source Code

Provide your implementation in the repository.
Typical structure:

```
/src
    main.py or main.c
    graph_builder.py
    welsh_powell.py
README.md
dataset/
```

---

## 9. Conclusion

The class scheduling problem can be effectively solved using graph coloring. By representing each course as a vertex and each scheduling conflict as an edge, the Welsh–Powell algorithm generates a time-efficient and conflict-free schedule. The experiments confirm that the number of colors used corresponds directly to the minimum time slots required for the schedule.

---
