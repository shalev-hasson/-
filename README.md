# Shuttle Bus Vehicle & Driver Scheduling Solver

## Project

This solver was developed for the SSP 2026 Shuttle Bus Vehicle & Driver Scheduling term project.

The program receives an input instance in JSON format and produces a solution JSON file containing the list of driver duties. Each duty includes the assigned driver, vehicle, shift start and end times, meal break, and the full ordered activity sequence.

## Files

The submission contains:

```text
Solver.py
README.md
sol_small_01.json
sol_small_02.json
sol_medium_01.json
```

The following files were used for validation:

```text
check_solution.py
solution_schema.json
```

## Requirements

The solver uses only the Python standard library.

No external packages are required.

Recommended Python version:

```text
Python 3.10 or newer
```

The code was developed and tested using Anaconda Python on Windows.

## Build Instructions

No compilation or build step is required.

To verify that Python is installed, run:

```bash
python --version
```

or, when using Anaconda on Windows:

```bash
C:\Users\bensn\anaconda3\python.exe --version
```

## Run Instructions

### Running on a single instance

Use the following command:

```bash
python Solver.py instance.json solution.json
```

For example:

```bash
python Solver.py small_02.json solution_small_02.json
```

### Running from PyCharm without command-line arguments

The solver contains a default run configuration at the end of `Solver.py`:

```python
if len(sys.argv) == 1:
    sys.argv = ["Solver.py", "medium_01.json", "sol_medium_01.json"]
```

To run a different instance directly from PyCharm, change the two file names in this line.

For example:

```python
sys.argv = ["Solver.py", "small_02.json", "solution_small_02.json"]
```

Then press Run.

## Feasibility Checking

After each solution file is produced, it should be checked using the official checker:

```bash
python check_solution.py instance.json solution.json
```

For example:

```bash
python check_solution.py small_02.json solution_small_02.json
```

A feasible solution should pass the checker and print `OK`.

The official objective value is the total cost reported by `check_solution.py`.

## Hardware Used

The solutions were produced on the following machine:

```text
Operating system: Windows 11 home
Python distribution: Anaconda Python 3.11.15
CPU:  Intel(R) Core(TM) i7-1065G7 CPU @ 1.30GHz‏, ‎1498 Mhz
RAM: 16.0 GB
```

## Wall-Clock Time and Results

The following table reports the wall-clock time used to produce each solution and the objective value computed by the official checker.

| Instance | Output file | Wall-clock time | Checker result | Objective value |
|---|---:|---:|---:|---:|
| small_01 | sol_small_01.json | 15.78 | OK | 12,812.75 |
| small_02 |  sol_small_02.json | 15.99 | OK | 13,845.25 |
| medium_01 | sol_medium_01.json | 40.29 | OK | 20,335.25 |

## Measuring Runtime

Runtime was measured as wall-clock time.

## Notes

The solver prioritizes feasibility and checker compliance.

Each generated duty is constructed to satisfy the following main constraints:

- every service trip is covered exactly once
- each duty starts at the depot and ends at the depot
- each driver operates exactly one vehicle during the shift
- shift length is within the allowed range
- shift start and shift end are on quarter-hour marks
- each duty contains exactly one legal meal break
- service trips depart at their scheduled departure times
- deadhead trips are only between the depot and a terminal
- activity sequences are time-contiguous and location-contiguous
- terminal capacity is checked before accepting local improvements.

The algorithm is a heuristic, so it does not guarantee an optimal solution. However, it is designed to produce feasible solutions quickly and to reduce cost through greedy duty extension, local merging of duties, and reuse of physical vehicles across non-overlapping shifts.
