# Prometheus

### 1. Infrastructure Setup

- Created new Linux EC2 instance (Ubuntu 24.04 LTS) for Prometheus server
- Instance type: t3.small (2 vCPU, 2GB RAM)
- Region: Different from Windows server (cross-region monitoring)
- Security Groups configured for ports: 22 (SSH), 9090 (Prometheus), 3000 (Grafana), 9100 (Node Exporter), 9182 (Windows Exporter)

### 2. Prometheus Server Installation

```bash
# Simple Ubuntu package installation
sudo apt update
sudo apt install prometheus
```

Ubuntu 24.04 package **c**ontains current Prometheus v2.45.3 (no need for manual installation

### 3. Node Exporter Setup (Linux Monitoring)

```bash
# Install Node Exporter for Linux server monitoring
sudo apt install prometheus-node-exporter

# Verify running
sudo systemctl status prometheus-node-exporter
curl [localhost:9100/metrics](http://localhost:9100/metrics)
```

**Configuration**: Ubuntu package pre-configured Prometheus to scrape Node Exporter at [`localhost:9100`](http://localhost:9100)

### 4. Windows Exporter Setup (Windows Server Monitoring)

**On Windows Server:**

- Downloaded Windows Exporter v0.31.3 (.exe)
- Ran directly from command line (console mode)
- Listens on port 9182
- Exposes Windows metrics: CPU, memory, disk, services

**Firewall Configuration Required:**

- AWS Security Group: Allow inbound TCP 9182
- Windows Firewall: `netsh advfirewall firewall add rule name="Prometheus Windows Exporter" dir=in action=allow protocol=TCP localport=9182`

### 5. Cross-Region Configuration

**Prometheus config** (`/etc/prometheus/prometheus.yml`):

```yaml
scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['[localhost:9090](http://localhost:9090)']

  - job_name: 'node'
    static_configs:
      - targets: ['[localhost:9100](http://localhost:9100)']

  - job_name: 'windows'
    static_configs:
      - targets: ['myip']  # Windows server public IP
```

### 6. Testing and Validation

![image19.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2019.png?raw=true)

![image20.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2020.png?raw=true)


## 7. Alerts (Alertmanager)

### Alertmanager Architecture Overview

**Alertmanager is the dedicated alerting component of the Prometheus ecosystem:**

- **Prometheus:** Collects metrics and evaluates alert rules
- **Alertmanager:** Handles alert routing, grouping, silencing, and notifications
- **Modular approach:** Each component specialized for its function

### 1. Alertmanager Installation

**Installed via Ubuntu package manager:**

```bash
sudo apt update
sudo apt install prometheus-alertmanager
```

**Service configuration:**

- **Service name:** `prometheus-alertmanager`
- **Config location:** `/etc/prometheus/alertmanager.yml`
- **Default port:** 9093
- **AWS Security Group:** Added TCP 9093 for management access

### 2. Prometheus Integration Configuration

**Updated `/etc/prometheus/prometheus.yml` to connect Prometheus with Alertmanager:**

```yaml
# Alert routing configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets: ['[localhost:9093](http://localhost:9093)']

# Alert rules file location
rule_files:
  - "/etc/prometheus/alerts.yml"
```

### 3. Alert Rules Creation

**Created `/etc/prometheus/alerts.yml` with production-ready alert rules**

### 4. Real-World Testing Experience

**Successfully tested complete alerting pipeline:**

**Test scenario:** Stopped Windows Exporter to simulate server failure

- **Alert trigger time:** 1 minute (as configured)
- **Alert status progression:** Inactive → Pending → Firing
- **Prometheus UI:** Displayed " Firing (1)" for TargetDown alert
- **Alert details:** Shows instance, job, severity, and description
- **Alert resolution:** Automatic when Windows Exporter restarted

### 5. Troubleshooting Experience

**Encountered and resolved common production issues:**

**YAML Configuration Challenges:**

- **Issue:** Alertmanager failed to start due to indentation errors
- **Root cause:** Debian package removes web UI, requires precise YAML formatting
- **Solution:** Used `amtool` CLI for management and corrected indentation
- **Learning:** Enterprise monitoring requires precise configuration management

![image21.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2021.png?raw=true)

![image22.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2022.png?raw=true)

![image23.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2023.png?raw=true)
