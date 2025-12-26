# GitHub Actions Self-Hosted Runner – Setup & DB Backup Notes

This document explains the sudo setup, basic package installation, GitHub Actions runner configuration, and **database backup location requirements** for the action runner.

---

## 1. Passwordless sudo configuration for runner user

User: `g2tech`

Create a sudoers file:

```bash
sudo tee /etc/sudoers.d/g2tech <<EOF
g2tech ALL=(ALL) NOPASSWD:ALL
EOF
```

Set correct permissions:

```bash
sudo chmod 440 /etc/sudoers.d/g2tech
```

Validate sudoers syntax:

```bash
sudo visudo -cf /etc/sudoers.d/g2tech
```

Test sudo access (should not ask for password):

```bash
sudo -n whoami
```

Expected output:

```
root
```

---

## 2. Install required packages

Install curl (required for GitHub runner setup and scripts):

```bash
sudo apt update
sudo apt install -y curl
```

---

## 3. GitHub Actions Runner Configuration

### Download runner

```bash
mkdir -p ~/actions-runner && cd ~/actions-runner
curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/latest/download/actions-runner-linux-x64.tar.gz
tar xzf actions-runner-linux-x64.tar.gz
```

### Configure runner

```bash
./config.sh --url https://github.com/<ORG_OR_USER>/<REPO> --token <RUNNER_TOKEN>
```

### Start runner

```bash
./run.sh
```

(Optional – run as a service)

```bash
sudo ./svc.sh install
sudo ./svc.sh start
```

---

## 4. Database Backup Requirement (IMPORTANT)

### Backup location

All database backups **must be stored inside the action runner path** so they are accessible during workflow execution.

**Recommended path:**

```
/home/g2tech/actions-runner/db-backup/
```

### Create backup directory

```bash
mkdir -p /home/g2tech/actions-runner/db-backup
chmod 750 /home/g2tech/actions-runner/db-backup
```

### GitHub Actions usage

Workflows can directly reference this path:

```yaml
- name: List DB backups
  run: ls -lh /home/g2tech/actions-runner/db-backup
```

---

## 5. Security Notes

* Do **not** commit database backups to GitHub repositories
* Restrict directory permissions
* Rotate and clean old backups using cron or workflow jobs

---

## Maintained by

G2 SA-Team /DevOps
