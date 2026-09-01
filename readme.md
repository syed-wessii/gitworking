# Ansible Assignment 2

## Nginx • Apache • Reverse Proxy • Website Rotation • Logrotate

------------------------------------------------------------------------

## 1. Assignment Objective

The objective of this assignment is to configure Nginx and Apache on the
managed nodes, use Nginx as a reverse proxy to Apache, host Tanya and
Heena websites, rotate the active website every two hours, control Nginx
log growth with logrotate, and execute Ansible operations one worker at
a time.

## 2. Assignment Requirements

-   Install Nginx on more than two servers.
-   Ensure Nginx log files do not consume more than 1 GB of space on the
    nodes.
-   Create an equal number of websites according to the team size. For
    this implementation, Tanya and Heena websites were created.
-   The team website/domain should display Tanya content for the first
    two hours and Heena content for the next two hours, repeating every
    two hours.
-   Install Apache.
-   Configure Nginx as a reverse proxy to Apache.
-   Run Ansible commands so worker nodes are updated one by one, not
    altogether.
-   Use the required Ansible strategies.

## 3. Final Architecture Implemented

``` text
Browser
 |
 | HTTP request :80
 v
Nginx (Reverse Proxy)
 |
 | proxy_pass
 v
Apache :8080
 |
 +--> /var/www/tanya/index.html
 |
 +--> /var/www/heena/index.html

Response path:
Apache -> Nginx -> Browser
```

Nginx is the public-facing entry point. It receives the browser request
on port 80 and forwards the request to Apache on port 8080. Apache
serves the selected website.

# 4. Screenshots

## 4 Ansible Inventory Showing All Managed Nodes


Command:

``` bash
ansible all --list-hosts
```
<img width="536" height="89" alt="2026-08-22 02_34_08-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/5031a384-2130-4cdd-a048-75623bf620ec" />
<img width="944" height="455" alt="2026-08-22 02_36_21-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/be46b9f4-849f-494b-8bd9-7bb0550124a9" />


## 4.1 Website Configuration

The following website directories were created on the managed nodes:

``` text
/var/www/tanya/index.html
/var/www/heena/index.html
```

``` bash
ansible all -b -f 1 -m shell -a "find /var/www -maxdepth 2 -type f -name index.html -print"
```

### Screenshot 2 -- Tanya and Heena index.html Files

<img width="944" height="323" alt="2026-08-22 02_38_16-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/89dc132a-2ac9-4ec1-8a5d-de06a366eeb4" />


## 5. Apache Configuration

Apache was installed and configured to listen on port 8080 instead of
port 80, so Nginx can receive public HTTP traffic on port 80.

``` bash
ansible all -b -f 1 -m shell -a "apache2 -v"
ansible all -b -f 1 -m shell -a "apache2ctl configtest"
ansible all -b -f 1 -m shell -a "ss -lntp | grep ':8080'"
```

The Apache DocumentRoot is switched between `/var/www/tanya` and
`/var/www/heena`.

<img width="944" height="307" alt="2026-08-22 02_41_13-screenshots to be saved - File Explorer" src="https://github.com/user-attachments/assets/f8ce9540-45ef-4346-ae95-8bf670cce340" />


> **\[INSERT SCREENSHOT HERE\]**

## 6. Nginx Reverse Proxy

Nginx was configured as a reverse proxy. The browser sends the request
to Nginx on port 80 and Nginx forwards it to Apache on port 8080 using:

``` text
proxy_pass http://127.0.0.1:8080;
```

``` bash
ansible all -b -f 1 -m shell -a "nginx -t"
```

<img width="943" height="281" alt="2026-08-22 02_42_37-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/0ea847dc-9711-4cea-a765-20dda124e9a7" />

<img width="946" height="351" alt="2026-08-22 02_45_37-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/f0e4e70f-3fb5-4b3c-9080-ac051cc2afe4" />


## 7. Two-Hour Website Rotation

Cron jobs were configured on all three managed nodes.

  Schedule                 Website
  ------------------------ ---------
  00, 04, 08, 12, 16, 20   Tanya
  02, 06, 10, 14, 18, 22   Heena

The same Tanya/Heena rotation is configured on `control_node_1`,
`control_node_2`, and `control_node_3`. The Cron jobs modify Apache's
DocumentRoot and reload Apache.

``` bash
ansible all -b -f 1 -m shell -a "crontab -l"
ansible all -b -f 1 -m shell -a "systemctl is-active cron"
```

### Screenshot 5 -- Cron Entries Showing Tanya and Heena Schedules

<img width="947" height="412" alt="2026-08-22 02_50_22-screenshots to be saved - File Explorer" src="https://github.com/user-attachments/assets/e2c34db9-e8dc-47d6-a149-4095b0f57ec2" />


## 8. Nginx Logrotate

A dedicated logrotate configuration was created for Nginx logs:

``` text
/var/log/nginx/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    size 100M
    sharedscripts
    postrotate
        systemctl reload nginx
    endscript
}
```

``` bash
ansible all -b -f 1 -m shell -a "logrotate -d /etc/logrotate.d/nginx-assignment2 2>&1 | tail -20"
ansible all -b -f 1 -m shell -a "cat /etc/logrotate.d/nginx-assignment2"
```

### Screenshot 6 -- Nginx Logrotate Configuration

<img width="951" height="491" alt="2026-08-22 02_58_43-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/7e68cdf4-292a-4f02-91b3-2f2f9e2a23b6" />


## 9. Sequential Ansible Execution

Commands were executed using the fork value `1`:

``` bash
ansible all -b -f 1 -m shell -a "<command>"
```

The `-f 1` option limits Ansible to one fork, so managed hosts are
processed one at a time rather than being updated in parallel.

<img width="947" height="257" alt="2026-08-22 02_53_48-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/46fb394c-2798-415d-9c76-73b4307fa4e1" />


> **\[INSERT SCREENSHOT HERE\]**

## 10. Verification Commands

``` bash
ansible all -b -f 1 -m shell -a "systemctl is-active nginx; systemctl is-active apache2"
ansible all -b -f 1 -m shell -a "nginx -t; apache2ctl configtest"
ansible all -b -f 1 -m shell -a "ss -lntp | grep -E ':80|:8080'"
ansible all -b -f 1 -m shell -a "grep DocumentRoot /etc/apache2/sites-enabled/000-default.conf"
ansible all -b -f 1 -m shell -a "crontab -l"
ansible all -b -f 1 -m shell -a "systemctl is-active cron"
ansible all -b -f 1 -m shell -a "cat /etc/logrotate.d/nginx-assignment2"
ansible all -b -f 1 -m shell -a "du -sh /var/log/nginx"
```

### Screenshot 8 -- Final All-in-One Verification

<img width="959" height="526" alt="2026-08-22 03_04_12-_Ansible - Notepad" src="https://github.com/user-attachments/assets/091cf60e-3527-4cea-b8ea-6fdbcc3428ad" />


## 11. Requirement Status

  -----------------------------------------------------------------------
  Requirement             Status                  Evidence / Notes
  ----------------------- ----------------------- -----------------------
  Nginx installed         Completed               Nginx is installed and
                                                  configured on the
                                                  managed nodes.

  More than 2 servers     Completed               `control_node_1`,
                                                  `control_node_2`,
                                                  `control_node_3`

  Nginx logs              Completed               Logrotate configuration
  limited/controlled                              created and validated;
                                                  usage is below 1 GB.

  Tanya website           Completed               Directory and
                                                  `index.html` created
                                                  and verified.

  Heena website           Completed               Directory and
                                                  `index.html` created
                                                  and verified.

  2-hour website rotation Completed / configured  Cron switches Apache
                                                  DocumentRoot between
                                                  Tanya and Heena.

  Apache installed        Completed               Apache installed and
                                                  verified.

  Apache on 8080          Completed               Apache listens on port
                                                  8080.

  Nginx reverse proxy to  Completed               Nginx is the
  Apache                                          public-facing reverse
                                                  proxy and Apache is the
                                                  backend.

  One-by-one worker       Completed               Commands were run with
  execution                                       `-f 1`.
  -----------------------------------------------------------------------





## 12.1 Website Switching Result for Tanya

<img width="941" height="228" alt="2026-08-22 02_57_24-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/10fb6f58-de2d-45a8-a8f6-a77c4169a57d" />


## 12.2 Nginx Log Directory Size

<img width="938" height="229" alt="2026-08-22 03_01_50-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/5f30a29a-0274-4420-84c1-8b06d57ff68f" />


``` bash
ansible all -b -f 1 -m shell -a "du -sh /var/log/nginx"
```


## 12.3 Final Output Through Web Page

### Tanya Website

<img width="958" height="419" alt="2026-08-22 03_10_30-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/622c64b4-6325-4e98-8677-41d60a7a838f" />


### Heena Website

<img width="960" height="363" alt="2026-08-22 03_11_21-Ansible_Assignment_2_README  -  Compatibility Mode - Word" src="https://github.com/user-attachments/assets/d7d07a59-83d0-4fd0-b991-51f443fee5dc" />


# 13. Final Result

The assignment was implemented using three managed nodes with Nginx as
the public-facing reverse proxy and Apache running on port 8080 as the
backend.

The Tanya and Heena websites were created and configured for two-hour
rotation through Cron. Nginx log growth was controlled using logrotate,
and Ansible commands were executed sequentially using `-f 1`.

The final website output was verified through the web browser.
