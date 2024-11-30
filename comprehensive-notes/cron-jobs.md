# Jobs in Kubernetes

## Overview
- **Jobs** are used for running tasks that execute once or a specified number of times and then terminate.
- Ideal for **batch processing**, **data analytics**, or workloads not meant to run indefinitely.

## Key Features of Jobs

### 1. Short-Lived Workloads
- **Purpose**: Designed to perform specific tasks and exit upon completion.
- **Examples**:
  - Image processing.
  - Analytics tasks.
  - Report generation.

### 2. Restart Policy
- **Default Behavior**: Kubernetes tries to keep pods running (`restartPolicy: Always`).
- **For Jobs**:
  - Set `restartPolicy: Never` or `restartPolicy: OnFailure` to match job requirements.

### 3. Parallelism and Completions
- **Parallelism**: Controls how many pods can run concurrently.
  - **Field**: `parallelism`
  - Example: If `parallelism: 3`, three pods can run simultaneously.
- **Completions**: Specifies how many successful pods are needed for the job to complete.
  - **Field**: `completions`
  - Example: If `completions: 5`, the job runs until five successful completions are achieved.

### Single Instance Job
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: math-add-job
spec:
  template:
    metadata:
      name: add-task
    spec:
      restartPolicy: Never
      containers:
      - name: math-container
        image: busybox
        command: ["sh", "-c", "echo $((2+3))"]
```
### Parallel Job with Retries
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-job
spec:
  completions: 3
  parallelism: 2
  backoffLimit: 4
  template:
    metadata:
      name: random-task
    spec:
      restartPolicy: Never
      containers:
      - name: random-container
        image: random-error
```
# CronJobs in Kubernetes

CronJobs are used to schedule Jobs to run at specific times or intervals, similar to the cron utility in Linux.

## Key Features of CronJobs

1. **Scheduling**:
   - The `schedule` field defines the time using a cron format (`minute hour day month day-of-week`).
   - **Example**: `*/5 * * * *` runs every 5 minutes.

2. **Job Template**:
   - The `jobTemplate` field contains the Job specification to run.

3. **Concurrency Policies**:
   - **Allow (default)**: Allows jobs to run concurrently.
   - **Forbid**: Prevents new jobs from starting if a previous job is still running.
   - **Replace**: Stops the current job and replaces it with a new one.

4. **Time Management**:
   - Use `startingDeadlineSeconds` to specify the maximum time a job can start after its scheduled time.
   - Use `successfulJobsHistoryLimit` and `failedJobsHistoryLimit` to manage completed and failed job history.

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: reporting-cronjob
spec:
  schedule: "0 9 * * *" # Every day at 9 AM
  concurrencyPolicy: Forbid
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: Never
          containers:
          - name: report-container
            image: busybox
            command: ["sh", "-c", "echo Generating daily report"]
```
### Commands
```bash
kubectl apply -f job.yaml
kubectl apply -f cronjob.yaml

kubectl get jobs
kubectl describe job <job-name>

kubectl get pods -l job-name=<job-name>

kubectl logs <pod-name>

kubectl delete job <job-name>

kubectl get cronjobs
kubectl describe cronjob <cronjob-name>

kubectl delete cronjob <cronjob-name>

kubectl create job --from=cronjob/<cronjob-name> <new-job-name>
```





