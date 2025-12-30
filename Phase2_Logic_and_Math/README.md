# Phase 2: The Automation Engineer (Logic & Math)

In **Phase 2**, the focus shifts from simple data extraction to **computational analysis**. As a Solution Architect, your role often involves "FinOps" (Cloud Financial Operations) and Capacity Planning. This phase teaches you how to turn raw text streams into actionable business intelligence by using AWK’s built-in arithmetic capabilities and control-flow logic.

---

### Core Learning Objectives
*   **State Management:** Learning to use variables that persist across lines (accumulators, counters, and flags).
*   **The END Block:** Mastering the post-processing phase where final calculations (averages, percentages, forecasts) are performed.
*   **Formatted Output:** Using `printf` to generate professional, C-style formatted reports rather than raw text dumps.
*   **Conditional Logic:** Implementing `if-else` structures to handle data anomalies or specific business rules.

### Validated Reference Materials (2025)
1.  **[awk.dev](https://www.awk.dev/):** The primary resource for the **2nd Edition (2023)** of *The AWK Programming Language*. Specifically, review the "Data Processing" chapter for updated math examples.
2.  **[GNU AWK Manual: Arithmetic Ops](https://www.gnu.org/software/gawk/manual/html_node/Arithmetic-Ops.html):** A deep dive into how AWK handles floating-point numbers and basic operators.
3.  **[Baeldung: Formatting Output with AWK](https://www.baeldung.com/linux/awk-format-output):** A guide to mastering `printf`, which is essential for architectural reporting.

---

### Detailed Project Breakdown

#### 1. Cloud Bill Forecaster (Cost Management)
*   **Scenario:** You receive a daily usage export (CSV) from a cloud provider with columns: `Date, Service, Quantity, UnitPrice`. You need to know if the project will exceed its $5,000 monthly budget.
*   **The Task:** Calculate the total cost of each record (`Quantity * UnitPrice`), keep a running total, and in the `END` block, extrapolate the current spending to a 30-day forecast.
*   **Skill Gained:** Floating-point arithmetic and cross-block variable persistence (calculating in the main body, reporting in `END`).
*   **Why for SAs:** Vital for FinOps visibility. It allows you to catch "cost leaks" (e.g., a runaway NAT Gateway) early in the billing cycle.
*   **Implementation Note:** `total += ($3 * $4); END { printf "Projected Monthly Spend: $%.2f\n", (total / days_elapsed) * 30 }`.

#### 2. API Latency Reporter (Performance Engineering)
*   **Scenario:** You are auditing a Microservice’s performance. You have a log containing response times in milliseconds.
*   **The Task:** Process the log to find the **Minimum**, **Maximum**, and **Average** latency.
*   **Skill Gained:** Using conditional logic to update `min/max` values and using `END` for aggregate math.
*   **Why for SAs:** Identifying performance bottlenecks. As an architect, you need to know if your system is meeting its Service Level Objectives (SLOs).
*   **Implementation Note:** Initialize `min` with a high value (e.g., 99999). Use `if($1 > max) max=$1` and `sum += $1`.

#### 3. Storage Quota Estimator (Capacity Planning)
*   **Scenario:** You are designing a multi-tenant storage solution. You need to analyze the output of `du -b` (disk usage in bytes) across hundreds of user directories.
*   **The Task:** Convert bytes into human-readable GB or TB. Flag any directory that is using more than 80% of a 50GB architectural limit.
*   **Skill Gained:** Constant-based math (division by $1024^3$) and string concatenation for "Status" labels.
*   **Why for SAs:** Essential for capacity planning and preventing "noisy neighbor" issues in shared infrastructure.
*   **Implementation Note:** `gb = $1/1024/1024/1024; if (gb > 40) print $2, gb "GB [WARNING]"`

#### 4. The Transaction Tally (Data Integrity)
*   **Scenario:** A financial system outputs a log of "Credit" and "Debit" events. You need to verify that the final balance matches the reported "Footer" value in the file.
*   **The Task:** Use `if-else` logic to add or subtract from a `balance` variable based on a "Type" column. Print an error if the computed balance doesn't match the file's final line.
*   **Skill Gained:** Logical branching and record-type handling.
*   **Why for SAs:** Ensuring data consistency across distributed systems. This is a classic "Reconciliation" pattern in architecture.
*   **Implementation Note:** `$2 == "CR" ? bal += $3 : bal -= $3`. Use the `END` block to compare `bal` with a target variable passed via `-v`.

#### 5. Multi-Log Synchronizer (Log Aggregation)
*   **Scenario:** You are troubleshooting a distributed system with two different logs. Log A uses Unix Epoch (1735560000) and Log B uses a standard string (2025-12-30).
*   **The Task:** Use math and built-in functions to convert Log A’s Epoch time into a comparable format, then print both logs in a unified, chronological order.
*   **Skill Gained:** Working with system time functions and multi-file variable management.
*   **Why for SAs:** Critical for "Root Cause Analysis" (RCA). Architects must be able to correlate events across different layers of the stack (e.g., App logs vs. DB logs).
*   **Implementation Note:** In GAWK, use `strftime("%Y-%m-%d", $1)` to normalize the timestamp for comparison.

---

### Phase 2 Summary Table

| Tool Feature | Architectural Application | Project Reference |
| :--- | :--- | :--- |
| **Accumulators (`sum += x`)** | Forecasting monthly cloud budgets. | Project 1 |
| **Conditional Math** | Tracking Min/Max latency for SLOs. | Project 2 |
| **Unit Conversion** | Transforming raw bytes into GB/TB. | Project 3 |
| **Ternary Operators** | Rapid Credit/Debit reconciliation logic. | Project 4 |
| **Time Functions** | Correlating logs from different services. | Project 5 |

### Recommended "Consult" Site for this Phase
*   **[GNU AWK: Time Functions](https://www.gnu.org/software/gawk/manual/html_node/Time-Functions.html):** Necessary for Project 5. It explains how to handle timestamps, which is one of the most common hurdles in architectural data analysis.