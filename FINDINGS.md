# Security and robustness audit

The audit covered the tracked source, build configuration, documentation, and repository hygiene. No suspected credentials, tracked build artifacts, CI workflows, or vendored dependencies were found. Menu input stays inside the simulator. The program does not invoke shells or access files or networks based on user input. CMake, the C compiler, and the OpenMP runtime come from the local toolchain. The dependency review covered the repository configuration and local build, without a live vulnerability feed.

| Severity | Location | One-line risk | Fix applied |
| --- | --- | --- | --- |
| medium | `main.c:44`, `main.c:59` | Menu input outside the range of `int` could trigger undefined behavior during `%d` conversion. | Replaced both conversions with a bounded parser for whitespace-delimited menu choices. Invalid numbers and trailing junk produce the existing input errors. |
| medium | `main.c:107` | The benchmark mixed atomic increments and unsynchronized reads of its shared progress counter, causing an OpenMP data race. | Synchronized the increment, threshold check, and display in one OpenMP critical region. |
| medium | `main.c:83` | Seed-array allocation could fail or its manually computed byte size could wrap on a narrow platform, leading to invalid writes. | Used checked array allocation via `calloc` and return a failure status if allocation fails. |
| medium | `main.c:69`, `main.c:88`, `main.c:278`, `README.md:13` | Runs seeded in the same second can repeat, and per-thread seeds select offsets in the same 31-bit generator cycle. | report-only — changing random streams would change the experiment methodology and affect comparisons with prior results. |
| low | `main.c:69` | A failed clock read silently produced a fixed simulation seed. | Check the clock result before starting either run mode and report failure. |
| low | `main.c:175` | Buffered prompts could stall callers using pipes, and failed output could leave a successful exit status. | Flush and check output at both prompts and before successful exit. |
| low | `CMakeLists.txt:12` | Treating OpenMP as optional and hard-coding compiler flags could let configuration succeed when the build could not compile or link. | Require the C OpenMP component at configure time and use its imported target for compiler and linker settings. |
| low | `.gitignore:1` | Local environment files, CMake outputs, executables, and editor files could be added accidentally. | Added ignore rules for these files and allowed an environment template. |
| low | `RESULTS.md:15`, `main.c:78` | The reported experiment count gives two different totals, and both differ from the current program default. | report-only — the historical run size cannot be established from the tracked files. |
