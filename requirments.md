# Dinner Party Scheduler App

<!-- TOC tocDepth:2..3 chapterDepth:2..6 -->

- [1. Core Concept](#1-core-concept)
- [2. Requirements](#2-requirements)
- [3. Definitions](#3-definitions)
    - [3.1. `Couple` Class](#31-couple-class)
    - [3.2. `Series` Class](#32-series-class)
    - [3.3. `Dinner` Class](#33-dinner-class)
    - [3.4. `Population`](#34-population)
- [4. Key Algorithm](#4-key-algorithm)
    - [4.1. Scheduling](#41-scheduling)
    - [4.2. Series Generation (General Steps)](#42-series-generation-general-steps)
    - [4.3. Maximizing the Number of `Series` Before `Couple`s Repeat](#43-maximizing-the-number-of-series-before-couples-repeat)
    - [4.4. Combinatorial Check](#44-combinatorial-check)
    - [4.5. Improved Process Overview](#45-improved-process-overview)

<!-- /TOC -->

## 1. Core Concept
- **Objective:** Schedule monthly dinner parties where couples don't meet the same couples more than once, over a period of 3-4 months.
- **Constraints:** Ensure no repeat couples per `dinner`, while maintaining a fixed group size of 4 `couple`s per `dinner`.

## 2. Requirements
1. Each `dinner` includes 4 `couple`s.
2. No `couple`s should repeat a `dinner` with another `couple`.
3. The program needs to work for 3 or 4 `series` (months).

## 3. Definitions

### 3.1. `Couple` Class
- This is the basic data structure representing each individual or household.
- Each `couple` attends exactly one `dinner` per `series`.
- **MVP Data Structure:**
    - `id` (unique identifier for each `couple`)
    - `met_couples[]` (an array of foreign IDs representing other `couple`s they’ve already dined with, conveying a relational database approach)
- **Later Iterations:**
    - `bool hosting` (track if they've willing to host a `dinner`)
    - `string address` (address of the household)
    - `string names` (could later break down to individuals if needed)
   
### 3.2. `Series` Class
- Represents one month of `dinner` events.
- Each `series` will contain multiple `dinners` (15 `dinners` for a population of 60 `couple`s, with 4 `couple`s per `dinner`).
- **MVP Data Structure:**
    - `dinners[]` (an array of `dinner` objects)
- **Key Function:**
    - `newSeries()` - Generates a new `series` with fresh `dinner` groupings, ensuring no `couple` repeats a previous pairing.

### 3.3. `Dinner` Class
- Represents a single `dinner` event attended by 4 `couple`s.
- No `couple` can attend a `dinner` with a `couple` they have already met in previous `series`.
- **MVP Data Structure:**
    - `id` (unique identifier for the `dinner`)
    - `group_size` (fixed at 4 `couple`s for now)
    - `couple`s[] (an array of `couple` objects attending this `dinner`)

### 3.4. `Population`
- Represents the total number of `couple`s signed up for the Church's Dinner Party `series`.
- This is the dataset from which you will form the `dinners`.

## 4. Key Algorithm

### 4.1. Scheduling

#### 4.1.1. For Each `Series`, Schedule `Dinner`s:
1. **Initialize Empty `Dinner`s:**
   - For each `series`, create 15 empty `dinner` slots (assuming 60 couples) where each dinner will host 4 `couple`s.
   
2. **Assign Couples to `Dinner`s:**
   - **Step-by-Step Assignment:**
     - **Iterate through each `couple`:** Loop over the list of `couple`s in the population.
     - **Find a valid `dinner`:** For each `couple`, check available dinners in the current `series` where:
       1. The dinner has fewer than 4 `couple`s.
       2. The `couple` has not dined with any other `couple` in that dinner (by checking the `met_couples[]` array).
     - **Assign `couple` to `dinner`:** If a valid dinner is found, add the `couple` to the dinner.
     - **Handle conflicts:** If no valid dinner is found for a `couple`, use a **backtracking strategy** (reshuffle previous assignments or swap `couple`s in different dinners to resolve conflicts).
     
3. **Repeat for All `Series`:**
   - Repeat the process across all 3-4 `series`, ensuring that in each new series, no `couple` repeats a `dinner` with a `couple` they’ve already met in previous series.
   
4. **Conflict Detection:**
   - After assigning couples to dinners, run a final check to ensure no couple has been paired with another couple they’ve previously met. If a conflict is detected, apply reshuffling or backtracking to correct the groupings.

### 4.2. Series Generation (General Steps)

1. **Create and Populate `Dinner`s:**
   - For each `series`, generate the required number of dinners (e.g., 15 dinners for a population of 60 couples). 
   
2. **Check for Valid Pairings:**
   - For every dinner, ensure that each `couple` has not met any of the other couples in the current dinner. Use the `met_couples[]` array to verify that no couple repeats a pairing with another couple from previous series.

3. **Backtrack if Necessary:**
   - If a `couple` cannot be assigned without violating the "no repeat" rule, implement a **backtracking mechanism**: 
     - Reassign couples from earlier dinners, or swap participants between dinners, to resolve the conflict.
   
4. **Repeat Across Multiple Series:**
   - Continue the process of creating, assigning, and validating dinners across 3-4 series. The goal is to maximize unique groupings in each series.

### 4.3. Maximizing the Number of `Series` Before `Couple`s Repeat

1. **Calculate Maximum Possible Series:**
   - Before running the algorithm, estimate how many series are possible before couples inevitably repeat pairings.
   
2. **Consider the Population and Group Size:**
   - Given the population size (`p`) the total number of `couple`s registered and the group size (4 `couple`s per `dinner`), calculate the maximum number of `series` that can be created where no couples repeat dinners.
   - **Example:** If the population is 60 couples, calculate how many unique groupings of 4 couples are possible before repeats occur.

3. **Combinatorics to Maximize Unique Pairings:**
   - This is a combinatorics problem: determining how many unique groupings of 4 couples can be made from the population.
   
4. **Optimize for Maximum Series:**
   - The goal is to maximize the number of unique series before a `couple` repeats a `dinner` with another `couple`. Use a combination of heuristics (like prioritizing couples with the fewest pairings) and backtracking to extend the number of series.

---

### 4.4. Combinatorial Check

- **Formula:** The total number of unique combinations for a `dinner` (group of 4 `couple`s) from the population of `p` `couple`s is calculated using combinatorics:
  
$$
\text{Combinations} = \binom{p}{4}
$$

- This formula gives the total number of possible unique groupings of 4 couples that can be formed from the population. This combinatorial limit helps determine how many unique dinners (and therefore series) can be created before repeat pairings become inevitable.

---

### 4.5. Improved Process Overview

1. **Initialization:**
   - Start by creating empty dinners for each series.
   
2. **Couple Assignment:**
   - Assign couples to dinners using a heuristic approach to reduce conflict. Prioritize couples with fewer existing connections to maximize unique groupings.

3. **Validation and Conflict Handling:**
   - Validate the groupings by checking the `met_couples[]` array and apply backtracking or reshuffling if conflicts are found.

4. **Repeat for Multiple Series:**
   - Repeat the process across 3-4 series, ensuring no couple repeats a dinner with someone they've already met.