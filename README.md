# Kind Cluster with Multiple Laravel Applications

This project sets up a local Kubernetes cluster using Kind with two Laravel applications running on PHP 8.3 with Nginx.

## Prerequisites
- Docker
- Kind (Kubernetes IN Docker)
- kubectl
- Laravel applications in `app1` and `app2` directories

## Setup Instructions
Just run the records in Taskfile.yaml like below one by one in order
```bash
task {name-of-the-task}
```

### Add these two records in /etc/hosts
sudo cat /etc/hosts
127.0.0.1 app1.local
127.0.0.1 app2.local

### Access in browser
Then we can access in browser with http://app1.local and https://app2.local