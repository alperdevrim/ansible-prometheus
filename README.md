# 🚀 Ansible Prometheus Docker Setup

This project uses Ansible to automate the installation of Docker and deployment of a Prometheus monitoring instance inside a Docker container.

It is built using:

- ✅ [geerlingguy.docker](https://galaxy.ansible.com/geerlingguy/docker): Official Ansible role to install Docker
- ✅ A custom role to deploy Prometheus in a Docker container
- ✅ Inventory-driven setup to configure remote hosts

---

## 📁 Project Structure

```
ansible-prometheus/
├── inventory/
│   └── hosts                  # Your target machine(s)
├── group_vars/
│   └── all.yml               # Variables for Docker (user, etc.)
├── playbook.yml              # Main playbook
├── roles/
│   └── prometheus/           # Custom role for Prometheus container
│       ├── tasks/
│       │   └── main.yml
│       └── templates/
│           └── prometheus.yml.j2
```

---

## 💡 What It Does

- Installs Docker (CE) using a well-maintained official role
- Adds your specified user to the `docker` group
- Deploys Prometheus as a Docker container
- Maps a custom Prometheus configuration file into the container
- Exposes Prometheus on port `9090`

---

## 🧰 Prerequisites

- A remote Ubuntu host
- Ansible installed on your control machine
- SSH access to the target machine

---

## 🔧 Setup Instructions

### 1. Clone the repository

```bash
git clone https://github.com/alperdevrim/ansible-prometheus.git
cd ansible-prometheus
```

### 2. Install the Docker role from Ansible Galaxy

```bash
ansible-galaxy install geerlingguy.docker
```

### 3. Define your inventory

Edit `inventory/hosts`:

```ini
[prometheus]
your_target_host ansible_user=your_user ansible_ssh_pass=your_password ansible_become=true
```

> 💡 It's recommended to use Ansible Vault to securely store the SSH password.

---

### 4. Set group variables

Edit `group_vars/all.yml`:

```yaml
docker_users:
  - your_user
```

---

### 5. Run the Playbook

```bash
ansible-playbook -i inventory/hosts playbook.yml
```

---

## 🔍 Access Prometheus

Once the playbook completes, you can access Prometheus in your browser at:

```
http://your_target_host:9090
```

---

## 📦 Prometheus Configuration

The Prometheus config is defined in:

```
roles/prometheus/templates/prometheus.yml.j2
```

Default contents:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
```

You can customize this to scrape additional targets, add alert rules, etc.

---

## 🧼 Cleanup

To stop and remove the Prometheus container:

```bash
docker stop prometheus && docker rm prometheus
```

To uninstall Docker (if needed):

```bash
sudo apt purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker /etc/docker
```

---

## 🛠 Troubleshooting

- ❌ **Permission denied?** — Make sure the user is in the `docker` group.
- ❌ **Mount issues?** — Use `/opt/prometheus` instead of `/etc/prometheus` to avoid Snap Docker restrictions.
- ❌ **SSH fails?** — Consider using SSH keys or Ansible Vault for passwords.

---

## 📈 Optional Enhancements

- Add **Grafana** using another Docker container
- Monitor **host metrics** with `node_exporter`
- Setup **persistent storage** using Docker volumes
- Add **Prometheus rules and alerts**

---

