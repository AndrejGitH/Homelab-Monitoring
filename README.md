# Infrastructure Monitoring Lab

For detailed documentation, see: [docs/](docs/)  


## What I Built
I set up a complete monitoring system using AWS EC2 instances to learn how real companies track server performance. The project combines traditional monitoring (Zabbix) with modern tools (Prometheus + Grafana) to understand different approaches used in enterprise environments.

## Why This Project
I'm passionate about understanding how systems work under the hood and wanted hands-on experience with the monitoring tools that keep production environments running. This project taught me how to spot problems before they impact users.

## Technology Stack
- **Prometheus** - Modern metrics collection and alerting
- **Grafana** - Building custom dashboards
- **Zabbix** - Traditional monitoring to understand enterprise legacy systems
- **AWS EC2** - Linux server running the monitoring tools, Windows Server 2022 as the target
- **PromQL** - Writing queries to extract meaningful data from metrics
- **Docker/ECR** - Transitioned Prometheus from traditional system service to Docker container

## What I Actually Did
- **Set up cross-region monitoring** between Linux and Windows servers on AWS
- **Built custom Grafana dashboards** that show what matters for troubleshooting
- **Created alerting rules** that fire when actual problems happen 
- **Learned to troubleshoot** when things broke - YAML configs, service dependencies, networking issues
- **Made it secure** with PSK encryption and proper firewall rules
- **Implemented AWS Elastic Container Registry (ECR)** to create a centralized private container repository for container portability across multiple servers

## Challenges I Overcame
- **Grafana dashboard variables** kept breaking when I copied queries - learned the hard way about templating
- **Alertmanager wouldn't start** due to YAML indentation errors - spent some time debugging config files  
- **Windows Exporter networking** required specific firewall rules across AWS security groups
- **Alert rule testing** by deliberately breaking services to see if monitoring actually works

## What This Taught Me
Understanding monitoring isn't just about installing tools - it's about knowing what normal looks like so you can spot when something's wrong. Every spike in CPU or memory tells a story about what the server is doing.

![My Custom Grafana Dashboard](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/Dashboard_readme.png?raw=true)

![Win22_Zabbix](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%201.png?raw=true) 

![Prometheus](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2020.png?raw=true)

