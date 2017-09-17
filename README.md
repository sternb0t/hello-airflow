# hello airflow

This is a simple demo of [Apache Airflow](https://airflow.incubator.apache.org/). Below are the steps I took to get it running on my machine, a MacBook Pro running macOS sierra 10.12.6.

## Install

I like to use `virtualenv` still, even though I know there's newer stuff.

```bash
$ virtualenv --python=python3 hellow-airflow
$ cd hello-airflow
$ source bin/activate
(hello-airflow)$ pip install airflow
(hello-airflow)$ mkdir hello-airflow
(hello-airflow)$ cd hello-airflow
(hello-airflow)$ pip freeze > requirements.txt
(hello-airflow)$ echo "# hello airflow" >> README.md
(hello-airflow)$ git init
(hello-airflow)$ git add .
(hello-airflow)$ git commit -m "initial commit"
```

## Quick Start

Adapted from [the tutorial doc](https://airflow.incubator.apache.org/start.html), but setting `AIRFLOW_HOME` to the `airflow` subfolder:

```bash
(hello-airflow)$ mkdir airflow
(hello-airflow)$ export AIRFLOW_HOME=~/Code/hello-airflow/hello-airflow/
(hello-airflow)$ airflow initdb
(hello-airflow)$ airflow webserver -p 8080
```

Browse to your http://localhost:8080 and Airflow's web server should be there!

## Tutorial DAG

Here are the steps I took to try out the tutorial, with outputs. Activating the virtualenv is assumed.

### Install

Get the `tutorial.py` DAG, save it in the DAGs folder, and confirm Airflow can find it:

```bash
$ wget https://raw.githubusercontent.com/apache/incubator-airflow/master/airflow/example_dags/tutorial.py \
    -O dags/tutorial.py
$ airflow list_dags
[2017-09-17 14:40:40,804] {__init__.py:57} INFO - Using executor SequentialExecutor
[2017-09-17 14:40:41,091] {models.py:167} INFO - Filling up the DagBag from /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/dags


-------------------------------------------------------------------
DAGS
-------------------------------------------------------------------
example_bash_operator
example_branch_dop_operator_v3
example_branch_operator
example_http_operator
example_passing_params_via_test_command
example_python_operator
example_short_circuit_operator
example_skip_dag
example_subdag_operator
example_subdag_operator.section-1
example_subdag_operator.section-2
example_trigger_controller_dag
example_trigger_target_dag
example_xcom
latest_only
latest_only_with_trigger
test_utils
tutorial
```

### List tasks

List the tasks in the `tutorial.py` DAG:

```bash
$ airflow list_tasks tutorial
[2017-09-17 14:41:55,796] {__init__.py:57} INFO - Using executor SequentialExecutor
[2017-09-17 14:41:56,062] {models.py:167} INFO - Filling up the DagBag from /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/dags
print_date
sleep
templated
```

### Test DAG commands

Test the `tutorial.py` `sleep` command:

```bash
$ airflow test tutorial sleep 2017-09-17
[2017-09-17 14:44:08,008] {__init__.py:57} INFO - Using executor SequentialExecutor
[2017-09-17 14:44:08,284] {models.py:167} INFO - Filling up the DagBag from /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/dags
/Users/jeffrey.sternberg/Code/hello-airflow/lib/python3.5/site-packages/airflow/ti_deps/deps/base_ti_dep.py:94: PendingDeprecationWarning: generator '_get_dep_statuses' raised StopIteration
  for dep_status in self._get_dep_statuses(ti, session, dep_context):
/Users/jeffrey.sternberg/Code/hello-airflow/lib/python3.5/site-packages/airflow/models.py:1140: PendingDeprecationWarning: generator 'get_dep_statuses' raised StopIteration
  dep_context):
[2017-09-17 14:44:08,617] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.sleep 2017-09-17 00:00:00 [None]>
[2017-09-17 14:44:08,620] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.sleep 2017-09-17 00:00:00 [None]>
[2017-09-17 14:44:08,620] {models.py:1318} INFO - 
--------------------------------------------------------------------------------
Starting attempt 1 of 2
--------------------------------------------------------------------------------

[2017-09-17 14:44:08,620] {models.py:1342} INFO - Executing <Task(BashOperator): sleep> on 2017-09-17 00:00:00
[2017-09-17 14:44:08,632] {bash_operator.py:71} INFO - tmp dir root location: 
/var/folders/lw/5_wx8g5d5dz2fm319zjyt4jm0000gn/T
[2017-09-17 14:44:08,633] {bash_operator.py:80} INFO - Temporary script location :/var/folders/lw/5_wx8g5d5dz2fm319zjyt4jm0000gn/T/airflowtmp6i6d_9bp//var/folders/lw/5_wx8g5d5dz2fm319zjyt4jm0000gn/T/airflowtmp6i6d_9bp/sleepujlx8tzc
[2017-09-17 14:44:08,633] {bash_operator.py:81} INFO - Running command: sleep 5
[2017-09-17 14:44:08,637] {bash_operator.py:90} INFO - Output:
[2017-09-17 14:44:13,646] {bash_operator.py:97} INFO - Command exited with return code 0
```

Test the `tutorial.py` `templated` command. Here we can see the results of iteration inside the Jinja template:

```bash
$ airflow test tutorial templated 2017-09-17
[2017-09-17 14:48:26,771] {__init__.py:57} INFO - Using executor SequentialExecutor
[2017-09-17 14:48:27,055] {models.py:167} INFO - Filling up the DagBag from /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/dags
/Users/jeffrey.sternberg/Code/hello-airflow/lib/python3.5/site-packages/airflow/models.py:1140: PendingDeprecationWarning: generator 'get_dep_statuses' raised StopIteration
  dep_context):
/Users/jeffrey.sternberg/Code/hello-airflow/lib/python3.5/site-packages/airflow/ti_deps/deps/base_ti_dep.py:94: PendingDeprecationWarning: generator '_get_dep_statuses' raised StopIteration
  for dep_status in self._get_dep_statuses(ti, session, dep_context):
[2017-09-17 14:48:27,408] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.templated 2017-09-17 00:00:00 [None]>
[2017-09-17 14:48:27,411] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.templated 2017-09-17 00:00:00 [None]>
[2017-09-17 14:48:27,411] {models.py:1318} INFO - 
--------------------------------------------------------------------------------
Starting attempt 1 of 2
--------------------------------------------------------------------------------

[2017-09-17 14:48:27,411] {models.py:1342} INFO - Executing <Task(BashOperator): templated> on 2017-09-17 00:00:00
[2017-09-17 14:48:27,428] {bash_operator.py:71} INFO - tmp dir root location: 
/var/folders/lw/5_wx8g5d5dz2fm319zjyt4jm0000gn/T
[2017-09-17 14:48:27,429] {bash_operator.py:80} INFO - Temporary script location :/var/folders/lw/5_wx8g5d5dz2fm319zjyt4jm0000gn/T/airflowtmpk15k5wjw//var/folders/lw/5_wx8g5d5dz2fm319zjyt4jm0000gn/T/airflowtmpk15k5wjw/templated325lnkmq
[2017-09-17 14:48:27,429] {bash_operator.py:81} INFO - Running command: 

    echo "2017-09-17"
    echo "2017-09-24"
    echo "Parameter I passed in"

    echo "2017-09-17"
    echo "2017-09-24"
    echo "Parameter I passed in"

    echo "2017-09-17"
    echo "2017-09-24"
    echo "Parameter I passed in"

    echo "2017-09-17"
    echo "2017-09-24"
    echo "Parameter I passed in"

    echo "2017-09-17"
    echo "2017-09-24"
    echo "Parameter I passed in"

[2017-09-17 14:48:27,434] {bash_operator.py:90} INFO - Output:
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-17
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-24
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - Parameter I passed in
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-17
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-24
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - Parameter I passed in
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-17
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-24
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - Parameter I passed in
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-17
[2017-09-17 14:48:27,439] {bash_operator.py:94} INFO - 2017-09-24
[2017-09-17 14:48:27,440] {bash_operator.py:94} INFO - Parameter I passed in
[2017-09-17 14:48:27,440] {bash_operator.py:94} INFO - 2017-09-17
[2017-09-17 14:48:27,440] {bash_operator.py:94} INFO - 2017-09-24
[2017-09-17 14:48:27,440] {bash_operator.py:94} INFO - Parameter I passed in
[2017-09-17 14:48:27,440] {bash_operator.py:97} INFO - Command exited with return code 0
```

### Run a DAG backfill

The next step is to run the DAG as a "backfill", meaning to simulate what it would have done if executed in the past.

```bash
$ airflow backfill tutorial -s 2017-09-01 -e 2017-09-02
[2017-09-17 14:50:37,105] {__init__.py:57} INFO - Using executor SequentialExecutor
[2017-09-17 14:50:37,367] {models.py:167} INFO - Filling up the DagBag from /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/dags
/Users/jeffrey.sternberg/Code/hello-airflow/lib/python3.5/site-packages/airflow/ti_deps/deps/base_ti_dep.py:94: PendingDeprecationWarning: generator '_get_dep_statuses' raised StopIteration
  for dep_status in self._get_dep_statuses(ti, session, dep_context):
[2017-09-17 14:50:37,788] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.print_date 2017-09-01 00:00:00 [scheduled]>
[2017-09-17 14:50:37,791] {base_executor.py:50} INFO - Adding to queue: airflow run tutorial print_date 2017-09-01T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:37,796] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.print_date 2017-09-02 00:00:00 [scheduled]>
[2017-09-17 14:50:37,799] {base_executor.py:50} INFO - Adding to queue: airflow run tutorial print_date 2017-09-02T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:37,805] {models.py:1120} INFO - Dependencies not met for <TaskInstance: tutorial.sleep 2017-09-02 00:00:00 [scheduled]>, dependency 'Trigger Rule' FAILED: Task's trigger rule 'all_success' requires all upstream tasks to have succeeded, but found 1 non-success(es). upstream_tasks_state={'upstream_failed': 0, 'successes': 0, 'failed': 0, 'skipped': 0, 'done': 0}, upstream_task_ids=['print_date']
[2017-09-17 14:50:37,811] {models.py:1120} INFO - Dependencies not met for <TaskInstance: tutorial.sleep 2017-09-01 00:00:00 [scheduled]>, dependency 'Trigger Rule' FAILED: Task's trigger rule 'all_success' requires all upstream tasks to have succeeded, but found 1 non-success(es). upstream_tasks_state={'upstream_failed': 0, 'successes': 0, 'failed': 0, 'skipped': 0, 'done': 0}, upstream_task_ids=['print_date']
[2017-09-17 14:50:37,816] {models.py:1120} INFO - Dependencies not met for <TaskInstance: tutorial.templated 2017-09-02 00:00:00 [scheduled]>, dependency 'Trigger Rule' FAILED: Task's trigger rule 'all_success' requires all upstream tasks to have succeeded, but found 1 non-success(es). upstream_tasks_state={'upstream_failed': 0, 'successes': 0, 'failed': 0, 'skipped': 0, 'done': 0}, upstream_task_ids=['print_date']
[2017-09-17 14:50:37,822] {models.py:1120} INFO - Dependencies not met for <TaskInstance: tutorial.templated 2017-09-01 00:00:00 [scheduled]>, dependency 'Trigger Rule' FAILED: Task's trigger rule 'all_success' requires all upstream tasks to have succeeded, but found 1 non-success(es). upstream_tasks_state={'upstream_failed': 0, 'successes': 0, 'failed': 0, 'skipped': 0, 'done': 0}, upstream_task_ids=['print_date']
[2017-09-17 14:50:42,733] {sequential_executor.py:40} INFO - Executing command: airflow run tutorial print_date 2017-09-02T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:42,987] {__init__.py:57} INFO - Using executor SequentialExecutor
Logging into: /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/logs/tutorial/print_date/2017-09-02T00:00:00
[2017-09-17 14:50:48,721] {sequential_executor.py:40} INFO - Executing command: airflow run tutorial print_date 2017-09-01T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:48,960] {__init__.py:57} INFO - Using executor SequentialExecutor
Logging into: /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/logs/tutorial/print_date/2017-09-01T00:00:00
[2017-09-17 14:50:54,682] {models.py:4024} INFO - Updating state for <DagRun tutorial @ 2017-09-01 00:00:00: backfill_2017-09-01T00:00:00, externally triggered: False> considering 3 task(s)
[2017-09-17 14:50:54,701] {models.py:4024} INFO - Updating state for <DagRun tutorial @ 2017-09-02 00:00:00: backfill_2017-09-02T00:00:00, externally triggered: False> considering 3 task(s)
/Users/jeffrey.sternberg/Code/hello-airflow/lib/python3.5/site-packages/sqlalchemy/sql/default_comparator.py:161: SAWarning: The IN-predicate on "dag_run.dag_id" was invoked with an empty sequence. This results in a contradiction, which nonetheless can be expensive to evaluate.  Consider alternative strategies for improved performance.
  'strategies for improved performance.' % expr)
[2017-09-17 14:50:54,712] {jobs.py:1978} INFO - [backfill progress] | finished run 0 of 2 | tasks waiting: 4 | succeeded: 2 | kicked_off: 0 | failed: 0 | skipped: 0 | deadlocked: 0 | not ready: 4
[2017-09-17 14:50:54,718] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.sleep 2017-09-02 00:00:00 [scheduled]>
[2017-09-17 14:50:54,721] {base_executor.py:50} INFO - Adding to queue: airflow run tutorial sleep 2017-09-02T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:54,728] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.sleep 2017-09-01 00:00:00 [scheduled]>
[2017-09-17 14:50:54,731] {base_executor.py:50} INFO - Adding to queue: airflow run tutorial sleep 2017-09-01T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:54,739] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.templated 2017-09-02 00:00:00 [scheduled]>
[2017-09-17 14:50:54,741] {base_executor.py:50} INFO - Adding to queue: airflow run tutorial templated 2017-09-02T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:54,750] {models.py:1126} INFO - Dependencies all met for <TaskInstance: tutorial.templated 2017-09-01 00:00:00 [scheduled]>
[2017-09-17 14:50:54,753] {base_executor.py:50} INFO - Adding to queue: airflow run tutorial templated 2017-09-01T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:54,770] {sequential_executor.py:40} INFO - Executing command: airflow run tutorial sleep 2017-09-02T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:50:55,025] {__init__.py:57} INFO - Using executor SequentialExecutor
Logging into: /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/logs/tutorial/sleep/2017-09-02T00:00:00
[2017-09-17 14:51:05,746] {sequential_executor.py:40} INFO - Executing command: airflow run tutorial templated 2017-09-02T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:51:05,999] {__init__.py:57} INFO - Using executor SequentialExecutor
Logging into: /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/logs/tutorial/templated/2017-09-02T00:00:00
[2017-09-17 14:51:11,710] {sequential_executor.py:40} INFO - Executing command: airflow run tutorial templated 2017-09-01T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:51:11,958] {__init__.py:57} INFO - Using executor SequentialExecutor
Logging into: /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/logs/tutorial/templated/2017-09-01T00:00:00
[2017-09-17 14:51:17,660] {sequential_executor.py:40} INFO - Executing command: airflow run tutorial sleep 2017-09-01T00:00:00 --local -sd DAGS_FOLDER/tutorial.py
[2017-09-17 14:51:17,913] {__init__.py:57} INFO - Using executor SequentialExecutor
Logging into: /Users/jeffrey.sternberg/Code/hello-airflow/hello-airflow/logs/tutorial/sleep/2017-09-01T00:00:00
[2017-09-17 14:51:28,643] {models.py:4024} INFO - Updating state for <DagRun tutorial @ 2017-09-01 00:00:00: backfill_2017-09-01T00:00:00, externally triggered: False> considering 3 task(s)
[2017-09-17 14:51:28,644] {models.py:4070} INFO - Marking run <DagRun tutorial @ 2017-09-01 00:00:00: backfill_2017-09-01T00:00:00, externally triggered: False> successful
[2017-09-17 14:51:28,663] {models.py:4024} INFO - Updating state for <DagRun tutorial @ 2017-09-02 00:00:00: backfill_2017-09-02T00:00:00, externally triggered: False> considering 3 task(s)
[2017-09-17 14:51:28,665] {models.py:4070} INFO - Marking run <DagRun tutorial @ 2017-09-02 00:00:00: backfill_2017-09-02T00:00:00, externally triggered: False> successful
[2017-09-17 14:51:28,677] {jobs.py:1978} INFO - [backfill progress] | finished run 2 of 2 | tasks waiting: 0 | succeeded: 6 | kicked_off: 0 | failed: 0 | skipped: 0 | deadlocked: 0 | not ready: 0
[2017-09-17 14:51:28,677] {jobs.py:2023} INFO - Backfill done. Exiting.
```
