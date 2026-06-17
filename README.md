# Autosubmit Tutorial: Creating and Running a Simple Workflow

## Objective

Learn the basic Autosubmit workflow by creating a simple experiment consisting of three dependent jobs : JOB_A  JOB_B  JOB_C
```

Each job prints a message and exits successfully.

---

# Prerequisites

Verify Autosubmit installation:

```bash
autosubmit --version
```

Example output:

```text
4.1.15
```

---

# Step 1: Create a New Experiment

Create a dummy Autosubmit experiment:

```bash
autosubmit expid -dm -d "a2_workflow"
```

Autosubmit creates a new experiment ID:

```text
a007
```

Move to the experiment directory:

```bash
cd /scratch/gananawp1/autosubmit/a007
```

Directory structure:

```text
a007/
├── conf/
├── pkl/
├── plot/
├── status/
└── tmp/
```

---

# Step 2: Create a Project Directory

Create a directory to store workflow scripts:

```bash
mkdir proj
cd proj
```

---

# Step 3: Create Workflow Scripts

## JOB_A.sh

```bash
cat > JOB_A.sh << 'EOF'
#!/bin/bash
echo "================================="
echo "Hello from JOB_A"
date
sleep 5
echo "JOB_A completed"
echo "================================="
EOF
```

## JOB_B.sh

```bash
cat > JOB_B.sh << 'EOF'
#!/bin/bash
echo "================================="
echo "Hello from JOB_B"
date
sleep 5
echo "JOB_B completed"
echo "================================="
EOF
```

## JOB_C.sh

```bash
cat > JOB_C.sh << 'EOF'
#!/bin/bash
echo "================================="
echo "Hello from JOB_C"
date
sleep 5
echo "JOB_C completed"
echo "================================="
EOF
```

Make scripts executable:

```bash
chmod +x *.sh
```

---

# Step 4: Test Scripts Manually

Execute the scripts:

```bash
./JOB_A.sh
./JOB_B.sh
./JOB_C.sh
```

Expected output:

```text
Hello from JOB_A
JOB_A completed

Hello from JOB_B
JOB_B completed

Hello from JOB_C
JOB_C completed
```

---

# Step 5: Configure Experiment Definition

Edit:

```bash
vi conf/expdef_a007.yml
```

Modify the LOCAL section:

```yaml
LOCAL:
  PROJECT_PATH: /scratch/gananawp1/autosubmit/a007/proj
```

This tells Autosubmit where the project scripts are stored.

---

# Step 6: Configure Workflow Jobs

Edit:

```bash
vi conf/jobs_a007.yml
```

Replace the default workflow with:

```yaml
JOBS:
  JOB_A:
    FILE: JOB_A.sh
    PLATFORM: LOCAL
    RUNNING: once

  JOB_B:
    FILE: JOB_B.sh
    PLATFORM: LOCAL
    DEPENDENCIES: JOB_A
    RUNNING: once

  JOB_C:
    FILE: JOB_C.sh
    PLATFORM: LOCAL
    DEPENDENCIES: JOB_B
    RUNNING: once
```

Workflow graph:

```text
JOB_A
  ↓
JOB_B
  ↓
JOB_C
```

---

# Step 7: Create Workflow Graph

Generate the Autosubmit workflow:

```bash
autosubmit create a007
```

Autosubmit performs:

1. Configuration validation
2. Dependency graph creation
3. Job list generation
4. Workflow plotting
5. Status file generation

Generated files:

```text
pkl/job_list_a007.pkl
plot/a007_*.pdf
status/a007_*.txt
```

---

# Step 8: Verify Workflow

Check generated status file:

```bash
cat status/*.txt
```

Check workflow plot:

```bash
ls plot
```

Example:

```text
a007_20260617_1551.pdf
```

---

# Step 9: Run Workflow

Launch the experiment:

```bash
autosubmit run a007
```

Autosubmit executes jobs according to dependencies:

```text
JOB_A
  ↓
JOB_B
  ↓
JOB_C
```

---

# Step 10: Monitor Workflow

Monitor job status:

```bash
autosubmit monitor a007
```

Typical states:

```text
READY
RUNNING
COMPLETED
FAILED
```

Expected result:

```text
JOB_A COMPLETED
JOB_B COMPLETED
JOB_C COMPLETED
```

---

# Step 11: Inspect Logs

List log files:

```bash
ls tmp/LOG_a007/
```

View output:

```bash
cat tmp/LOG_a007/*.out
```

Example:

```text
=================================
Hello from JOB_A
JOB_A completed
=================================

=================================
Hello from JOB_B
JOB_B completed
=================================

=================================
Hello from JOB_C
JOB_C completed
=================================
```

Check error logs:

```bash
cat tmp/LOG_a007/*.err
```

---

# Useful Commands

## Show Experiment

```bash
autosubmit describe a007
```

## Create Workflow

```bash
autosubmit create a007
```

## Run Workflow

```bash
autosubmit run a007
```

## Monitor Workflow

```bash
autosubmit monitor a007
```

## View Logs

```bash
ls tmp/LOG_a007/
```

## View Workflow Graph

```bash
ls plot/
```

---

# Workflow Execution Flow

```text
JOB_A
  ↓
JOB_B
  ↓
JOB_C
```

Execution sequence:

```text
1. JOB_A starts
2. JOB_A completes
3. JOB_B starts
4. JOB_B completes
5. JOB_C starts
6. JOB_C completes
```

---

# Key Learning Outcome

Autosubmit manages workflow dependencies automatically.

Instead of manually running:

```bash
bash JOB_A.sh
bash JOB_B.sh
bash JOB_C.sh
```

Autosubmit automatically:

1. Runs JOB_A
2. Waits for completion
3. Runs JOB_B
4. Waits for completion
5. Runs JOB_C

The same concept can later be applied to real HPC workflows such as:

```text
GEOGRID
  ↓
UNGRIB
  ↓
METGRID
  ↓
REAL
  ↓
WRF
```

or

```text
Preprocessing
  ↓
Simulation
  ↓
Post-processing
  ↓
Archiving
```

making Autosubmit a workflow manager for complex scientific applications.
