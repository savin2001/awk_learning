# Phase 1: The Tactical Diagnostician (Foundations)

As a **Solution Architect**, you often find yourself in environments—such as a secure jump box, a minimal Docker container, or a legacy server—where high-level languages like Python or Node.js are either unavailable or overkill. **Phase 1** focuses on the "tactical" use of AWK: the ability to perform zero-dependency data surgery to diagnose architectural issues on the fly.

---

### Core Learning Objectives
*   **The Data Stream Mindset:** Understand that AWK treats every file as a stream of records (lines) and fields (columns).
*   **Pattern-Action Logic:** Master the `pattern { action }` syntax to filter noise and target relevant data points.
*   **Environment Agnosticism:** Gain the skill to write scripts that work on any Unix-like OS (Linux, macOS, BSD) without installing packages.

### Validated Reference Materials (2025)
1.  **[Bruce Barnett’s Grymoire - AWK Tutorial](https://www.grymoire.com/Unix/Awk.html):** The gold standard for understanding the "Pattern-Action" paradigm and internal variables like `FS`, `RS`, and `NF`.
2.  **[The AWK Programming Language (awk.dev)](https://www.awk.dev/):** The official repository for the 2nd Edition (2023) of the original AWK book. Review the "Introduction" section for field manipulation basics.

---

### Detailed Project Breakdown

#### 1. IAM Policy Audit (TSV Parsing)
*   **Scenario:** You have a 50,000-line TSV export from a Cloud Identity Provider. You need to identify high-risk accounts.
*   **The Task:** Filter for users where the "Permissions" column contains "Admin" and the "Last Login" column is older than 90 days.
*   **Skill Gained:** Mastering the `-F` (Field Separator) flag and regex matching on specific columns.
*   **Why for SAs:** Quick security posture assessments during a discovery phase or audit.
*   **Implementation Note:** Use `-F'\t'` to handle tabs, and `$3 ~ /Admin/` to perform the regex check on the third column.

#### 2. Infrastructure Config Stripper
*   **Scenario:** You need to `diff` two Nginx configuration files across "Staging" and "Production" to find functional differences. The files are cluttered with different comments and spacing.
*   **The Task:** Create an AWK one-liner that removes all lines starting with `#` and all empty/whitespace-only lines.
*   **Skill Gained:** Using regex anchors (`^`, `$`) and the `next` statement to skip irrelevant data.
*   **Why for SAs:** Essential for "Configuration Drift" analysis when troubleshooting environment-specific bugs.
*   **Implementation Note:** `/^#/ || /^$/ {next} {print}` is the logic pattern to master here.

#### 3. The CSV/JSON Field Matcher
*   **Scenario:** You are dealing with an "unclean" log where the first 5 fields are CSV, but the 6th field is a raw JSON string (common in AWS CloudWatch or application logs).
*   **The Task:** Extract the CSV fields and only one specific key-value pair from the JSON blob in the 6th column.
*   **Skill Gained:** String manipulation using `split()` and nested separators.
*   **Why for SAs:** Handling hybrid data formats in modern distributed tracing and logging.
*   **Implementation Note:** Use `split($6, array, ":")` to break the JSON-like field without needing a heavy parser like `jq`.

#### 4. Resource Over-utilization Finder
*   **Scenario:** A server is lagging. You need to identify which processes are the "Architectural Villains"—consuming more resources than the microservice's allocated quota.
*   **The Task:** Parse the output of `ps aux`, skip the header line, and print the PID and Process Name only for those using more than 80% CPU.
*   **Skill Gained:** Using `NR` (Number of Records) to skip headers and performing numeric comparisons on fields.
*   **Why for SAs:** Real-time performance troubleshooting and capacity validation during load testing.
*   **Implementation Note:** Combine with a pipe: `ps aux | awk 'NR > 1 && $3 > 80.0 {print $2, $11}'`.

#### 5. Environment Variable Validator
*   **Scenario:** A CI/CD pipeline is failing because a `.env` file contains duplicate keys or empty values, causing container crashes.
*   **The Task:** Write a script that reads a `.env` file, checks for lines that don't follow the `KEY=VALUE` format, and flags duplicate keys.
*   **Skill Gained:** Basic use of **Associative Arrays** to track uniqueness and validating data integrity.
*   **Why for SAs:** Shifting security and reliability "left" by building pre-flight checks for Infrastructure-as-Code (IaC).
*   **Implementation Note:** Use an array `seen[$1]++` to detect if the first field (the key) has been encountered before.

---

### Phase 1 Summary Table

| Tool Feature | Architectural Application | Project Reference |
| :--- | :--- | :--- |
| **Field Separator (`-F`)** | Parsing multi-cloud metadata exports. | Project 1 |
| **Regex Patterns (`//`)** | Filtering logs for specific error codes. | Project 2 |
| **Internal Var `NR`** | Skipping headers in system reports. | Project 4 |
| **String `split()`** | Parsing hybrid JSON/CSV logs. | Project 3 |
| **Arrays (Basic)** | Validating IaC / `.env` integrity. | Project 5 |

### Recommended "Consult" Site for this Phase
*   **[ExplainShell.com](https://explainshell.com/):** Use this to paste your AWK one-liners. It will visually break down how the pattern and action work, which is vital for verifying your foundational logic.