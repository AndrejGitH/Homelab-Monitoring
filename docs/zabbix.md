# Zabbix

## 1. AWS EC2 Setup

- Launched WinServer22
- Launched Linux server for Zabbix core server

## 2. Key Pairs and Initial Connectivity

- Generated/downloaded a **.pem key pair** during EC2 launch for secure instance access.
- For **Linux**:
    - Converted the .pem file to .ppk using PuTTYgen to use with PuTTY.
    - Opened PuTTY, set Host Name to public EC2 IP, selected the .ppk under SSH → Auth.
    - Connected via SSH for all administrative tasks.

## 3. Configuring Linux Zabbix Server

- After SSH login with PuTTY, installed necessary packages:
    - Used **`sudo apt-get update && sudo apt-get install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf mysql-server`** to set up core services.
- **MySQL** was added specifically to provide Zabbix with a required backend database.
    - Configured MySQL: created database and user for Zabbix, updated zabbix_server.conf with DB credentials.
- Confirmed all connectivity (checked nginx/apache status, allowed HTTP/S ports in the EC2 security group).
- Completed Zabbix frontend setup in browser using the public EC2 IP.

## 4. Configuring Windows Server for Monitoring

- Accessed the WinServer22 via Microsoft Remote Desktop using RDP and password decrypted with PEM key.
- Downloaded and ran the Zabbix agent MSI installer.
    - Set **`Server=<Linux_Zabbix_Server_IP>`** in zabbix_agentd.conf
    - Used hostname "WinServer22" for consistency.
    - Restarted Zabbix Agent service.
    - Set Windows firewall and EC2 security group to allow outbound to Zabbix server TCP/10051.

## 5. Integrating Windows Host in Zabbix

- In Zabbix frontend, added "WinServer22" as new host.
- Linked "Windows by Zabbix agent active" template for monitoring.
- Set host interface to WinServer22 public IP.
- Host group assigned for filtering/tracking.
- Verified metrics in Zabbix (Latest Data): ensured agent reporting core Windows stats.

## 6. Network and Security Group Management

- Server and agent EC2 security groups restricted:
    - RDP/SSH only from trusted IPs.
    - Zabbix server port 10051 only open from agent’s public IP with /32 mask.

## 7. PSK Setup - Win server (zabbix agent) and Linux server (zabbix-server)

- Generated a 32‑byte hex PSK and saved it to `C:\Security\secret.psk`.
- Set strict permissions on `secret.psk` so only the Zabbix Agent account had read access.
- Edited `C:\Program Files\Zabbix Agent\zabbix_agent2.conf` and updated PSK fields:
    - `TLSConnect=psk`
    - `TLSAccept=psk`
    - `TLSPSKFile=C:\Security\secret.psk`
    - `TLSPSKIdentity=WinServer22`
- Restarted the “Zabbix Agent 2” Windows service to apply changes.
- In Zabbix UI: Data collection → Hosts → WinServer22 → Encryption (PSK) — set Identity to `WinServer22` and pasted the PSK from `secret.psk`.
- Verified host connectivity and confirmed no encryption errors were reported.

## 8. Creating Item

![image.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image.png?raw=true)

![image1.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%201.png?raw=true)

## 9. Creating Trigger

![image2.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%202.png?raw=true)

Compare the most recent value of the item to 20,000,000,000 bytes (~18.6 GiB) and raise a problem if it’s below.

![image3.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%203.png?raw=true)

Monitoring → hosts and trigger is successfully alerting low free space.

## 10. Creating Alerts (Gmail)

- Turned on 2‑step verification in the Gmail account.
- Created an App Password named “Zabbix” and saved the 16‑character value.
- Configured the sender in Zabbix: Alerts → Media types → Gmail → entered the Gmail address and pasted the App Password; saved and (optionally) ran a test.
- Assigned the destination: Users → Users → Admin → Media → added Email with the recipient address; saved.
- Enabled alerting: Alerts → Actions → Trigger actions → enabled the action that sends notifications.

![image4.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%204.png?raw=true)

![image5.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%205.png?raw=true)

![image6.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%206.png?raw=true)

![image7.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%207.png?raw=true)

Alert successfully sent:

![image8.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%208.png?raw=true)

![image9.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%209.png?raw=true)

## 11. Creating Graphs

Data collection→ Hosts→ Graphs→ Create graph→ add item and create graph

![image10.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2010.png?raw=true)

## 12. Network Map

Monitoring→ Maps → Create map

![image11.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2011.png?raw=true)

## 13. Managing Users, Groups and Roles

Adding user permission:
Users→User groups→Select user(Guest)→ Host permissions→ configure permission

![image12.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2012.png?raw=true)

Add permission to see actual items and graphs:
Users→User groups→Select user(Guest)→ Template permissions→ configure permission

![image13.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2013.png?raw=true)

Creating new group

![image14.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2014.png?raw=true)

![image15.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2015.png?raw=true)

![image16.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2016.png?raw=true)

Create user:

![image17.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2017.png?raw=true)

![image18.png](https://github.com/AndrejGitH/Homelab-Monitoring/blob/master/images/image%2018.png?raw=true)

