# **Ansible Assignment 2** 

_Nginx • Apache • Reverse Proxy • Website Rotation • Logrotate_ 

## **1. Assignment Objective** 

The objective of this assignment is to configure Nginx and Apache on the managed nodes, use Nginx as a reverse proxy to Apache, host Tanya and Heena websites, rotate the active website every two hours, control Nginx log growth with logrotate, and execute Ansible operations one worker at a time. 

## **2. Assignment Requirements** 

- Install Nginx on more than two servers. 

- Ensure Nginx log files do not consume more than 1 GB of space on the nodes. 

- Create an equal number of websites according to the team size. For this implementation, Tanya and Heena websites were created. 

- The team website/domain should display Tanya content for the first two hours and Heena content for the next two hours, repeating every two hours. 

- Install Apache. 

- Configure Nginx as a reverse proxy to Apache. 

- Run Ansible commands so worker nodes are updated one by one, not altogether. 

- Use the required Ansible strategies. 

## **3. Final Architecture Implemented** 

Browser 

| | HTTP request :80 v Nginx (Reverse Proxy) | | proxy_pass v Apache :8080 | +--> /var/www/tanya/index.html 

- | 

- +--> /var/www/heena/index.html 

Response path: Apache -> Nginx -> Browser 

Nginx is the public-facing entry point. It receives the browser request on port 80 and forwards the request to Apache on port 8080. Apache serves the selected website. The response returns through Nginx to the browser. 

## **4. Website Configuration** 

The following website directories were created on the managed nodes: 

Ansible Assignment 2 – README 

/var/www/tanya/index.html /var/www/heena/index.html The website contents were verified using: 

ansible all -b -f 1 -m shell -a "find /var/www -maxdepth 2 -type f -name index.html -print" Expected files: 

- /var/www/tanya/index.html 

- /var/www/heena/index.html 

## **5. Apache Configuration** 

Apache was installed and configured to listen on port 8080 instead of port 80, so Nginx can receive public HTTP traffic on port 80. 

ansible all -b -f 1 -m shell -a "apache2 -v" ansible all -b -f 1 -m shell -a "apache2ctl configtest" ansible all -b -f 1 -m shell -a "ss -lntp | grep ':8080'" The Apache DocumentRoot is switched between: 

/var/www/tanya /var/www/heena 

## **6. Nginx Reverse Proxy** 

Nginx was configured as a reverse proxy. The browser sends the request to Nginx, Nginx forwards it to Apache on port 8080, Apache serves the website, and the response returns to the browser through Nginx. 

ansible all -b -f 1 -m shell -a "nginx -t" 

The reverse-proxy design means the browser does not directly communicate with Apache. Nginx acts as the reverseproxy entry point. 

## **7. Two-Hour Website Rotation** 

Cron jobs were configured on both managed nodes using the following schedule: 

|Schedule|Website|
|---|---|
|00,04,08,12,16,20|Tanya|
|02,06,10,14,18,22<br>|Heena|
|Same rotaton on control_node_3|Tanya/Heena|
|The cron jobs modify Apache's DocumentRoot a<br>|nd reload Apache. This is the correct switching mechanism for the|
|fnal reverse-proxy architecture.||



ansible all -b -f 1 -m shell -a "crontab -l" ansible all -b -f 1 -m shell -a "systemctl is-active cron" 

## **8. Nginx Logrotate** 

A dedicated logrotate configuration was created for Nginx logs. The current configuration is: 

/var/log/nginx/*.log { daily 

Ansible Assignment 2 – README 

rotate 7 compress missingok notifempty size 100M sharedscripts postrotate systemctl reload nginx endscript } The configuration was validated using logrotate's debug/dry-run mode. Current Nginx log usage was also checked and was far below the 1 GB requirement. 

ansible all -b -f 1 -m shell -a "logrotate -d /etc/logrotate.d/nginx-assignment2 2>&1 | tail -20" ansible all -b -f 1 -m shell -a "du -sh /var/log/nginx" ansible all -b -f 1 -m shell -a "cat /etc/logrotate.d/nginx-assignment2" 

## **9. Sequential Ansible Execution** 

Commands were executed using the fork value 1: 

ansible all -b -f 1 -m shell -a "<command>" 

The -f 1 option limits Ansible to one fork, so managed hosts are processed one at a time rather than being updated in parallel. 

## **10. Verification Commands** 

The following commands can be used as the final verification checklist: 

ansible all -b -f 1 -m shell -a "systemctl is-active nginx; systemctl is-active apache2" ansible all -b -f 1 -m shell -a "nginx -t; apache2ctl configtest" ansible all -b -f 1 -m shell -a "ss -lntp | grep -E ':80|:8080'" ansible all -b -f 1 -m shell -a "grep DocumentRoot /etc/apache2/sites-enabled/000-default.conf" ansible all -b -f 1 -m shell -a "crontab -l" 

ansible all -b -f 1 -m shell -a "systemctl is-active cron" ansible all -b -f 1 -m shell -a "cat /etc/logrotate.d/nginx-assignment2" ansible all -b -f 1 -m shell -a "du -sh /var/log/nginx" 

## **11. Requirement Status / Important Gap** 

|Requirement|Status|Evidence / Notes<br>|
|---|---|---|
|Nginx installed|Completed|Nginx is installed and confgured on<br>the current managed nodes.|
|More than 2 servers|Completed|Current setup shown in the work<br>contains control_node_1 and<br>control_node_2 and<br>control_node_3<br>|
|Nginx logs limited/controlled|Completed|Nginx logrotate confguraton<br>created and validated; current usage<br>is far below 1 GB.|
|Tanya website|Completed|Tanya website directory and<br>index.html created and verifed.|
|Heena website|Completed|Heena website directoryand|



Ansible Assignment 2 – README 



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all --list-hosts<br>hosts (3):<br>control_node_1<br>control_node_2<br>control_node_3<br>:~/opsTree-ansible/ansible-1$ |<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -m ping<br>h<br>:~/opsTree-ansible/ansible-1$|<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible ansible-1$ ansible all -b -f 1 -m shell -a "echo '--- TANYA ---'; cat /var/www/tanya/index.htm<br>; echo '--- HEENA ---'; cat /var/www/heena/index.html"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "apache2 -v | head -1; ss -lntp | grep ':8080'"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "nginx -t && grep -n 'proxy_pass' /etc/nginx/sites-<br>enabled/default"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "ss -lntp | grep nginx"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "crontab -l 2>/dev/null"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "systemctl is-active cron"<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "sed -i 's|DocumentRoot /var/www/heena|DocumentRoot<br>/var/www/tanya|' /etc/apache2/sites-enabled/000-default.conf && apache2ctl configtest && systemctl reload apache2 && grep 'DocumentR<br>oot' /etc/apache2/sites—enabled/000-default.conf"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b “yl -m shell -a "curl -s http://127.0.0.1 | grep -E '<h1>|<p>'"<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "curl -s http://127.0.0.1 | grep -E '<h1>|<p>'"<br><!-- End of picture text -->

h 



<!-- Start of picture text -->
8 Tree i ible-1$ ansible all -b -f 1 -m shell ~-a "cat /etc/logrotate.d/nginx—assignment2"<br>h<br><!-- End of picture text -->



<!-- Start of picture text -->
:~/opsTree-ansible/ansible-1$ ansible all -b -f 1 -m shell -a "du -sh /var/log/nginx"<br><!-- End of picture text -->





<!-- Start of picture text -->
v  @ 13.232.84.178 x ie > Ask Gemini = (a) x vy @ 3.109.202.252 x + 4 Ask Gemini = Oo x<br><€ Cc A\ Notsecure 13.232.84.178 i d > Cs) Relaunch to update ? <€ Cc A\ Not secure 3.109.202.252 kd a Cs ) Relaunch to update<br>Tanya WebsiteD Tanya WebsiteD<br>Welcome to Tanya website Welcome to Tanya website<br>vy @ 13.203.79.38 x + N $ Ask Gemini - Oo x<br><€ CG A\ Not secure 13.203.79.38 kd eos > Cs ) Relaunch to update<br><!-- End of picture text -->

### Tanya<sup>Website</sup> D 

Welcome to Tanya website 

|vy<br>@ 13.232.84.178|x<br>+|4 AskGemini<br>=<br>Oo<br>x|v<br>@ 3.109.202.252|x<br>+<br>4 AskGemini|=<br>oO<br>x|
|---|---|---|---|---|---|
|<€<br>@)<br>A\ Notsecure<br>1|3.232.84.178|kd<br>mi<br>(s<br>Relaunchtoupdate<br>?|<€<br>Cc<br>A\ Notsecure|3.109.202.252<br>kd<br>mi<br>Cs}|Relaunchtoupdate<br>?|
|.<br>Heena Website|||.<br>Heena Website|||
|Welcome to Heena website|||Welcome to Heena website|||
|v<br>@<br>13.203.79.38|x<br>+|||4 Ask Gemini|=<br>o<br>x|
|€<br>Cc<br>A\Notsecure|13.203.79.38|||Xx<br>sonomm<br>ts}|Relaunchtoupdate|



#### Heena Website5 

Welcome to Heena website 

