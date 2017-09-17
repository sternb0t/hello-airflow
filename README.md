# hello airflow

This is a simple demo of [Apache Airflow](https://airflow.incubator.apache.org/). . Below are the steps I took to get it running on my machine, a MacBook Pro running macOS sierra 10.12.6.

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
