# Group7_FinalProject

---

# Class Scheduling System

Graph Theory Final Project

## 1. Project Overview

This project focuses on solving the class scheduling problem by modeling it as a graph coloring problem. Each course is represented as a vertex, and an edge is created between two courses if they have a scheduling conflict, such as the same lecturer, the same classroom, or the same group of students. By applying the Welsh–Powell graph coloring algorithm, the system assigns time slots so that no conflicting classes overlap. The goal is to generate a conflict-free and efficient class timetable.


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
/* 
   Class Scheduling using Welsh–Powell Algorithm
   --------------------------------------------
   - Each course is a vertex
   - Edge = conflict between two courses
   - Color = time slot

   Input format:
   1. Number of courses (n)
   2. n course labels (for example: A B C D)
   3. Adjacency matrix n x n with 0 or 1

   Example for Scenario A (4 courses):

   n = 4
   labels: A B C D

   Example adjacency matrix (symmetric, 0 on diagonal):
   If conflicts are:
   A with B
   A with C
   C with D

   Then matrix:
   0 1 1 0
   1 0 0 0
   1 0 0 1
   0 0 1 0
*/

#include <stdio.h>

#define MAX 20

int main() {
    int n;                              // number of courses (vertices)
    char label[MAX];                    // labels of courses (A, B, C, etc) 
    int adj[MAX][MAX];                  // adjacency matrix                
    int degree[MAX];                    // degree of each vertex             
    int order[MAX];                     // indices sorted by degree         
    int color[MAX];                     // color (time slot) for each vertex 
    int i, j;

    printf("Number of courses: ");
    scanf("%d", &n);

    if (n <= 0 || n > MAX) {
        printf("Invalid number of courses.\n");
        return 1;
    }

    // read course labels
    printf("Enter %d course labels (single characters, e.g. A B C D):\n", n);
    for (i = 0; i < n; i++) {
        scanf(" %c", &label[i]);
    }

    // read adjacency matrix
    printf("Enter adjacency matrix %d x %d (0 or 1):\n", n, n);
    for (i = 0; i < n; i++) {
        for (j = 0; j < n; j++) {
            scanf("%d", &adj[i][j]);
        }
    }

    // compute degree of each vertex
    for (i = 0; i < n; i++) {
        degree[i] = 0;
        for (j = 0; j < n; j++) {
            degree[i] += adj[i][j];
        }
    }

    // initialize order array with indices
    for (i = 0; i < n; i++) {
        order[i] = i;
    }

    // sort vertices in descending order of degree (simple bubble sort)
    for (i = 0; i < n - 1; i++) {
        for (j = 0; j < n - 1 - i; j++) {
            int u = order[j];
            int v = order[j + 1];
            if (degree[u] < degree[v]) {
                int temp = order[j];
                order[j] = order[j + 1];
                order[j + 1] = temp;
            }
        }
    }

    // initialize all colors to 0 (uncolored)
    for (i = 0; i < n; i++) {
        color[i] = 0;
    }

    int currentColor = 0;

    // Welsh–Powell coloring
    for (i = 0; i < n; i++) {
        int v = order[i];
        if (color[v] == 0) {
            currentColor++;            // start a new color
            color[v] = currentColor;   // color this vertex

            // try to color other vertices with the same color
            for (j = i + 1; j < n; j++) {
                int u = order[j];
                int k;
                int canUseColor = 1;   // assume we can use the color

                if (color[u] != 0) {
                    continue;         // already colored
                }

                // check adjacency with all vertices that already have this color
                for (k = 0; k < n; k++) {
                    if (color[k] == currentColor && adj[u][k] == 1) {
                        canUseColor = 0;
                        break;
                    }
                }

                if (canUseColor) {
                    color[u] = currentColor;
                }
            }
        }
    }

    // print result
    printf("\nScheduling result (time slots):\n");
    for (i = 0; i < n; i++) {
        printf("Course %c -> time slot %d\n", label[i], color[i]);
    }

    printf("\nTotal time slots used: %d\n", currentColor);

    return 0;
}

```

---

## 9. Conclusion

The class scheduling problem can be effectively solved using graph coloring. By representing each course as a vertex and each scheduling conflict as an edge, the Welsh–Powell algorithm generates a time-efficient and conflict-free schedule. The experiments confirm that the number of colors used corresponds directly to the minimum time slots required for the schedule.

---
