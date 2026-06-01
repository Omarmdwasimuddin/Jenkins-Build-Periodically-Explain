# Jenkins Build Periodically – Complete Guide

## What is Build Periodically?

**Build periodically** is a Jenkins build trigger that automatically runs a job at scheduled intervals using a cron expression.

It is useful for:

* Scheduled builds
* Automated testing
* Database backups
* Report generation
* Log cleanup
* Nightly deployments

---

## Where to Find It?

1. Open Jenkins Dashboard
2. Select a Job
3. Click **Configure**
4. Go to **Build Triggers**
5. Check **Build periodically**
6. Enter a cron schedule

---

## Jenkins Cron Format

Jenkins uses the following format:

```text
MINUTE HOUR DAY_OF_MONTH MONTH DAY_OF_WEEK
```

### Field Ranges

| Field        | Range                 |
| ------------ | --------------------- |
| Minute       | 0-59                  |
| Hour         | 0-23                  |
| Day of Month | 1-31                  |
| Month        | 1-12                  |
| Day of Week  | 0-7 (0 or 7 = Sunday) |

---

## Common Examples

### Every Minute

```cron
* * * * *
```

### Every 5 Minutes

```cron
*/5 * * * *
```

### Every 10 Minutes

```cron
*/10 * * * *
```

### Every Hour

```cron
0 * * * *
```

### Every Day at Midnight

```cron
0 0 * * *
```

### Every Day at 8:00 AM

```cron
0 8 * * *
```

### Every Day at 2:30 PM

```cron
30 14 * * *
```

### Every Sunday at 11:00 PM

```cron
0 23 * * 0
```

### First Day of Every Month

```cron
0 0 1 * *
```

---

## Understanding the H Symbol

Jenkins provides a special symbol called **H (Hash)**.

It helps distribute build load across the Jenkins server.

### Example

```cron
H * * * *
```

Meaning:

* Runs once every hour
* Jenkins automatically chooses the minute

Example distribution:

```text
Job-A → Minute 07
Job-B → Minute 31
Job-C → Minute 54
```

This prevents all jobs from starting at the same time.

---

## Recommended Usage

### Every 5 Minutes

```cron
H/5 * * * *
```

### Every 15 Minutes

```cron
H/15 * * * *
```

### Once Per Day

```cron
H H * * *
```

Jenkins automatically selects the hour and minute.

---

## Real CI/CD Example

### Schedule

```cron
H/15 * * * *
```

### Jenkins Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Pull Source') {
            steps {
                git 'https://github.com/user/project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build Application') {
            steps {
                bat 'npm run build'
            }
        }
    }
}
```

### What Happens?

Every 15 minutes Jenkins will:

1. Pull the latest source code
2. Install dependencies
3. Build the application

---

# Build Periodically vs Poll SCM

These two triggers are often confused.

## Build Periodically

```cron
H/5 * * * *
```

Behavior:

* Runs every 5 minutes
* Runs regardless of code changes

### Example

```text
09:00 → Build
09:05 → Build
09:10 → Build
09:15 → Build
```

Even if no changes are pushed to GitHub.

---

## Poll SCM

```cron
H/5 * * * *
```

Behavior:

* Checks source control every 5 minutes
* Builds only if changes are detected

### Example

```text
09:00 → Check Git
09:05 → Check Git
09:10 → New Commit Found → Build
09:15 → Check Git
```

No code changes = No build.

---

# Best Practices

For CI/CD pipelines, prefer:

### GitHub Webhook

* Instant build after push
* Most efficient option

### Poll SCM

* Useful when webhooks are unavailable
* Builds only on changes

Use **Build Periodically** for:

* Nightly builds
* Scheduled testing
* Database backups
* Log cleanup
* Health checks
* Report generation

---

# Useful Cron Examples

| Task             | Cron Expression |
| ---------------- | --------------- |
| Every Minute     | `* * * * *`     |
| Every 5 Minutes  | `H/5 * * * *`   |
| Every 15 Minutes | `H/15 * * * *`  |
| Every Hour       | `H * * * *`     |
| Every Day        | `H H * * *`     |
| Every Sunday     | `H H * * 0`     |
| Every Month      | `H H 1 * *`     |

---

## Validation Tip

Jenkins usually shows a preview below the Schedule box such as:

```text
Would last have run at ...
Would next run at ...
```

Use this preview to verify that your cron expression is correct before saving the job.
