# Ansible Assignment 3 -- Spring3Hibernate Application Deployment

## Objective

The objective of this assignment is to use a **simple Ansible playbook**
to automate the setup and deployment of the Spring3Hibernate web
application on an Ubuntu EC2 instance.

The playbook performs the complete deployment process, including
installation of required packages, Tomcat setup, source-code checkout,
WAR creation using Maven, and deployment to Tomcat.

------------------------------------------------------------------------

## Technologies Used

-   Ansible
-   Ubuntu
-   AWS EC2
-   MySQL
-   OpenJDK 11
-   Maven
-   Git
-   Apache Tomcat 7.0.108
-   Spring3Hibernate

------------------------------------------------------------------------

## Project Structure

``` text
ansible-3/
├── Playbook.yml
├── hosts
└── ansible.cfg
```

------------------------------------------------------------------------

## Inventory Configuration

The `hosts` file defines the managed EC2 instance and the SSH connection
details.

Example:

``` ini
[managed_nodes]
control_node_3 ansible_host=<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=/home/<user>/webserver.pem
```

------------------------------------------------------------------------

## Ansible Configuration

The `ansible.cfg` file specifies the inventory file and
privilege-escalation settings.

``` ini
[defaults]
inventory = /path/to/ansible-3/hosts
become = true
become_method = sudo
become_user = root
host_key_checking = false
```

------------------------------------------------------------------------

## Playbook

The `Playbook.yml` automates the following tasks:

1.  Update the APT package cache.
2.  Install MySQL Server, OpenJDK 11, Maven, and Git.
3.  Start and enable the MySQL service.
4.  Create the `/opt/tomcat` directory.
5.  Download and extract Apache Tomcat 7.0.108.
6.  Clone the Spring3Hibernate Git repository.
7.  Build the WAR file using Maven.
8.  Copy the WAR file to the Tomcat `webapps` directory.
9.  Stop the existing Tomcat instance.
10. Wait for Tomcat to stop.
11. Remove the previous Spring3Hibernate deployment and WAR file.
12. Copy the newly built WAR file as `spring3hibernate.war`.
13. Start Tomcat.
14. Wait for the application to be deployed.

------------------------------------------------------------------------

## Running the Playbook

First, verify that Ansible is using the project configuration:

``` bash
ansible --version
```

Verify connectivity to the managed node:

``` bash
ansible all -i ./hosts -m ping
```

Expected result:

``` text
control_node_3 | SUCCESS => {
    "ping": "pong"
}
```

Run the playbook:

``` bash
ansible-playbook Playbook.yml
```

A successful execution should end with a recap similar to:

``` text
PLAY RECAP
control_node_3 : ok=... changed=... unreachable=0 failed=0
```

------------------------------------------------------------------------

## Deployment Verification

After the playbook completes successfully, the Spring3Hibernate
application can be accessed through the Tomcat server using:

``` text
http://<EC2_PUBLIC_IP>:8080/spring3hibernate/
```

The deployed application displays:

-   List of Employees
-   Add Employee
-   Upload File
-   List Images

------------------------------------------------------------------------

## Screenshots

### 1. Ansible Playbook Execution

**Screenshot:** Successful Ansible playbook execution showing the
`PLAY RECAP` with `unreachable=0` and `failed=0`.

<img width="960" height="463" alt="2026-09-01 21_39_40-Ansible-3 and 2 more tabs - File Explorer" src="https://github.com/user-attachments/assets/30461387-8378-4e65-86c5-4528d34592a7" />


------------------------------------------------------------------------

### 2. Spring3Hibernate Application

**Screenshot:** Browser showing the successfully deployed
Spring3Hibernate application.

<img width="960" height="328" alt="2026-09-01 21_33_02-" src="https://github.com/user-attachments/assets/65678cf1-39d0-4dcb-9dd5-668060a48ee8" />


------------------------------------------------------------------------

## Result

The Spring3Hibernate application was successfully deployed on an Ubuntu
EC2 instance using a **simple Ansible playbook**.

The playbook automated the installation, configuration, build, and
deployment process, and the application was successfully accessed
through Apache Tomcat on port `8080`.
