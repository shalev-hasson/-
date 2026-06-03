# Shuttle Bus Vehicle & Driver Scheduling Solver

## Project

This solver was developed for the SSP 2026 Shuttle Bus Vehicle & Driver Scheduling term project.

The program receives an input instance in JSON format and produces a solution JSON file containing the list of driver duties. Each duty includes the assigned driver, vehicle, shift start and end times, meal break, and the full ordered activity sequence.

## Method Summary

The solver uses a greedy Sequential VSP -> CSP heuristic.

First, all scheduled service trips are sorted chronologically. Then, each trip is considered one by one. For every trip, the algorithm tries to append it to an existing feasible duty. A duty is considered feasible only if it respects the vehicle movement rules, driver shift rules, meal-break constraints, and activity-sequence requirements.

If the trip cannot be added to any existing duty, a new duty is opened.

After the initial solution is created, the solver performs a short local-improvement phase. In this phase, it attempts to merge compatible pairs of duties when doing so reduces the cost proxy and keeps the solution feasible.

Finally, physical vehicle IDs are assigned. Vehicles are reused across non-overlapping duties whenever possible, in order to reduce the fixed vehicle cost.

The method is heuristic and does not guarantee global optimality. The main goal is to produce feasible checker-approved solutions quickly, while reducing driver cost, deadhead cost, and vehicle usage.

## Files

The submission contains:

```text
Solver.py
README.md
solution_small_02.json
solution_medium_01.json
solution_medium_02.json
solution_large_01.json
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

The released benchmark instances can be solved using:

```bash
python Solver.py small_02.json solution_small_02.json
python Solver.py medium_01.json solution_medium_01.json
python Solver.py medium_02.json solution_medium_02.json
python Solver.py large_01.json solution_large_01.json
```

### Running on a directory of instances

The solver also supports running on a directory of JSON instances:

```bash
python Solver.py instances_dir output_dir
```

For example:

```bash
python Solver.py instances solutions
```

In this mode, the solver reads all `.json` files from the input directory, skips solution files and schema files, and writes one solution file per instance to the output directory.

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
Operating system: Windows [fill in version, e.g. Windows 11]
Python distribution: Anaconda Python [fill in version]
CPU: [fill in CPU model]
RAM: [fill in RAM size]
```

## Wall-Clock Time and Results

The following table reports the wall-clock time used to produce each solution and the objective value computed by the official checker.

| Instance | Output file | Wall-clock time | Checker result | Objective value |
|---|---:|---:|---:|---:|
| small_02 | solution_small_02.json | [fill in seconds] | [OK / failed] | [fill in checker total] |
| medium_01 | solution_medium_01.json | [fill in seconds] | [OK / failed] | [fill in checker total] |
| medium_02 | solution_medium_02.json | [fill in seconds] | [OK / failed] | [fill in checker total] |
| large_01 | solution_large_01.json | [fill in seconds] | [OK / failed] | [fill in checker total] |

Optional debugging instance:

| Instance | Output file | Wall-clock time | Checker result | Objective value |
|---|---:|---:|---:|---:|
| small_01 | sol_small_01.json | [fill in seconds] | [OK / failed] | [fill in checker total] |

## Measuring Runtime

Runtime was measured as wall-clock time.

On Windows PowerShell, the following command can be used:

```powershell
Measure-Command { python Solver.py small_02.json solution_small_02.json }
```

When using the full Anaconda Python path:

```powershell
Measure-Command { C:\Users\bensn\anaconda3\python.exe Solver.py small_02.json solution_small_02.json }
```

The value `TotalSeconds` should be recorded in the runtime table above.

## Notes

The solver prioritizes feasibility and checker compliance.

Each generated duty is constructed to satisfy the following main constraints:

- every service trip is covered exactly once;
- each duty starts at the depot and ends at the depot;
- each driver operates exactly one vehicle during the shift;
- shift length is within the allowed range;
- shift start and shift end are on quarter-hour marks;
- each duty contains exactly one legal meal break;
- service trips depart at their scheduled departure times;
- deadhead trips are only between the depot and a terminal;
- activity sequences are time-contiguous and location-contiguous;
- terminal capacity is checked before accepting local improvements.

The algorithm is a heuristic, so it does not guarantee an optimal solution. However, it is designed to produce feasible solutions quickly and to reduce cost through greedy duty extension, local merging of duties, and reuse of physical vehicles across non-overlapping shifts.
