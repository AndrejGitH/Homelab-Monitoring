# Grafana


### 1. Installation Method

**Downloaded official Grafana OSS (free version):**

```bash
# Install dependencies
sudo apt-get install -y adduser libfontconfig1 musl

# Download latest Grafana package
wget [https://dl.grafana.com/oss/release/grafana_12.2.0_amd64.deb](https://dl.grafana.com/oss/release/grafana_12.2.0_amd64.deb)

# Install the package
sudo dpkg -i grafana_12.2.0_amd64.deb
```

**Start and configure service:**

```bash
# Start Grafana service
sudo systemctl start grafana-server

# Enable auto-start on boot
sudo systemctl enable grafana-server
```

### 2. Initial Configuration

- **Access:** [http://my-server-ip:3000](http://your-server-ip:3000)
- **Default login:** Admin/admin (changed on first login)
- **Port 3000** added to AWS Security Group

### 3. Data Source Integration

**Connected to Prometheus:**

- Data Sources → Add data source → Prometheus
- **URL:** [`http://localhost:9090`](http://localhost:9090)
- **Access:** Server (default)
- Test connection: ✅ "Data source is working"

### 4. Dashboard Implementation

**Imported pre-built dashboards:**

- **Node Exporter Full (ID: 1860)** - Linux server monitoring
- **Prometheus 2.0 Stats** - Prometheus internal metrics
- **Windows Dashboard (ID: 14694)** - Windows server monitoring

**Key metrics visualized:**

- **Linux Server:** CPU usage (1.8%), Memory (31%), Disk (42%)
- **Windows Server:** Cross-region monitoring, disk usage (59%)
- **Real-time updates** with professional visualization

### 5. Advanced Dashboard Development Experience

**Created custom "Windows Server Resource Overview" dashboard with professional enterprise layout:**

### **Resource Overview Section (Top Row):**

- **Server Instance Details:** Hostname, IP address, and connection status
- **CPU Utilization Gauge:** Real-time processor usage (0.42% - optimal performance)
- **RAM Utilization Gauge:** Memory consumption with visual thresholds (32.7% - healthy)
- **Disk Space Gauge:** Available storage with capacity visualization (13.0GB free)
- **Server Uptime Display:** System availability tracking (7 hours operational)
- **Hardware Specifications:** CPU cores count and system configuration (2 CPU cores)
- **Incident Management Integration:** Firing alerts timeline for operational response

### **Detailed Performance Analytics (Bottom Row):**

- **CPU Historical Trends:** Time-series showing processor utilization patterns
- **Network Traffic Analysis:** Upload/download bandwidth monitoring with real-time data
- **Disk I/O Performance:** Read/write operations tracking for storage optimization

### 6. Dashboard Customization and Problem-Solving Experience

**Overcame real-world technical challenges:**

### **Variable Management Solutions:**

- **Challenge:** Pre-built dashboards use template variables ($server, $instance, $job)
- **Solution:** Created hardcoded queries for reliable custom panel functionality
- **Learning:** Understanding Grafana templating system vs direct metric queries
- **Result:** Stable custom dashboards without variable dependencies

### **Metric Interpretation and Business Translation:**

- **Memory vs Storage Distinction:** `windows_memory_physical_total_bytes` = RAM (3.9GB)
- **Storage Capacity Analysis:** `windows_logical_disk_free_bytes` = Hard drive space (14GB)
- **Business Communication:** RAM (workspace) vs Disk (filing cabinet) analogy for stakeholders
- **Technical Accuracy:** Proper metric selection for meaningful business insights

### 7. Incident Management and Operational Integration

**Built comprehensive operational monitoring capabilities:**

### **Alert Visualization System:**

```
# Active incidents timeline visualization
count(ALERTS{alertstate="firing"})
```

### **Operational Business Value:**

- **Incident Timeline Visualization:** Clear problem identification and resolution tracking
- **Pattern Analysis Capability:** Historical alert data for trend identification and prevention
- **Response Time Optimization:** Visual incident duration and frequency analysis
- **Service Level Management:** Uptime and availability tracking for SLA compliance

### 8. Professional Skills Demonstrated and Portfolio Value

**Enterprise-grade monitoring competencies achieved:**

### **Technical Implementation:**

- **Multi-Platform Infrastructure Monitoring:** Linux and Windows servers across different AWS regions
- **Custom Dashboard Development:** Built specialized monitoring interfaces from scratch
- **PromQL Query Expertise:** Advanced metric queries for business-relevant insights
- **Visualization Strategy:** Multiple panel types (gauges, time-series, stats, tables) for different use cases

### **Business and Operational Understanding:**

- **Stakeholder Communication:** Technical metrics translated into actionable business insights
- **Capacity Planning Integration:** Resource utilization trending for infrastructure growth
- **Incident Response Capability:** Alert management integrated with visual dashboards
- **Cost Optimization Awareness:** Resource efficiency monitoring for budget management

![image24.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2024.png?raw=true)
