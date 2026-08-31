# Ansible Assignment 1 – User, Group and Permission Management

## Introduction

This project was completed using Ansible to configure and verify users, groups, permissions, directories and access controls on the target machine.

The work was carried out on `control_node_1` using Ansible commands from the Ansible control machine.

---

## Environment

- **Ansible Core:** 2.21.3
- **Python:** 3.12.3
- **Target Host:** `control_node_1`
- **Privilege Escalation:** `-b` / `become`

### Ansible Version

Command used:

```bash
ansible --version
```

**Screenshot:**

![alt text](<2026-08-20 21_29_51-Downloads - File Explorer.png>)

---

## 1. Inventory Verification

The inventory was checked to make sure `control_node_1` was available as the managed host.

Command used:

```bash
ansible-inventory --graph
```

**Screenshot:**
![alt text](<2026-08-20 21_30_18-Downloads - File Explorer.png>)
---

## 2. Ansible Connectivity Test

The connection to the target host was tested using the Ansible `ping` module.

Command used:

```bash
ansible control_node_1 -m ping
```

The expected response is `pong`.

**Screenshot:**

![alt text](<2026-08-20 21_30_18-Downloads - File Explorer-1.png>)

---

## 3. Package Installation

The required packages were installed on the target machine.

Packages included:

- `zsh`
- `acl`

Command used:

```bash
ansible control_node_1 -b -m apt -a "name=zsh,acl state=present update_cache=yes"
```

**Screenshot:**

![alt text](<2026-08-20 21_30_57-Downloads - File Explorer.png>)

A second run was used to verify that the packages were already present and no further changes were required.

Command used:

```bash
ansible control_node_1 -b -m apt -a "name=zsh,acl state=present"
```

**Screenshot:**

![alt text](<2026-08-20 21_31_52-.png>)

---

## 4. Groups Creation

Three groups were created for the different teams.

| Group | GID |
|---|---:|
| `dev-team` | 1001 |
| `devops-team` | 1002 |
| `admin-group` | 1003 |

Commands used:

```bash
ansible control_node_1 -b -m group -a "name=dev-team state=present"
```

```bash
ansible control_node_1 -b -m group -a "name=devops-team state=present"
```

```bash
ansible control_node_1 -b -m group -a "name=admin-group state=present"
```

**Screenshot:**

![alt text](<2026-08-20 21_34_15-Downloads - File Explorer.png>)

---

## 5. Users Creation

Nine users were created and assigned to their respective groups.

### Development Team

- `dev1`
- `dev2`
- `dev3`

### DevOps Team

- `devops1`
- `devops2`
- `devops3`

### Admin Team

- `admin1`
- `admin2`
- `admin3`

The users were also configured with the required login shells.

**Screenshot:**

![alt text](<2026-08-20 21_36_06-.png>)
![alt text](<2026-08-20 21_37_08-Downloads - File Explorer.png>)
---

## 6. User IDs and Group Membership Verification

The assignment requires verification of all 9 users and their UIDs.

Command used:

```bash
ansible control_node_1 -b -m shell -a "id dev1 && id dev2 && id dev3 && id devops1 && id devops2 && id devops3 && id admin1 && id admin2 && id admin3"
```

Expected UIDs:

| User | UID |
|---|---:|
| `dev1` | 2000 |
| `dev2` | 2001 |
| `dev3` | 2002 |
| `devops1` | 2003 |
| `devops2` | 2004 |
| `devops3` | 2005 |
| `admin1` | 2006 |
| `admin2` | 2007 |
| `admin3` | 2008 |

**Screenshot:**

![alt text](<2026-08-20 21_38_38-Downloads - File Explorer.png>)

---

## 7. Login Shell Verification

The login shells of the users were checked.

Command used:

```bash
ansible control_node_1 -b -m shell -a "getent passwd dev1 dev2 dev3 devops1 devops2 devops3 admin1 admin2 admin3"
```

The output verifies the users' account information, including their login shell.

**Screenshot:**

![alt text](<2026-08-21 18_19_39-screenshots to be saved - File Explorer.png>)

---

## 8. Password Policy Verification

The password expiry policy was checked for the required user.

Example command used:

```bash
ansible control_node_1 -b -m command -a "chage -l dev1"
```

The output was checked for password expiry and related account policy settings.

**Screenshot:**

![alt text](<2026-08-21 18_19_39-screenshots to be saved - File Explorer-1.png>)

---

## 9. Workspace Directories

Workspace directories were checked for the created users.

Command used:

```bash
ansible control_node_1 -b -m shell -a "ls -ld /home/*/workspace"
```

The output verifies that the workspace directories exist under the users' home directories.

**Screenshot:**

![alt text](<2026-08-20 21_38_38-Downloads - File Explorer-1.png>)

---

## 10. Project Management Directories

The project-management directory structure was checked.

Command used:

```bash
ansible control_node_1 -b -m shell -a "find /opt/project-management -maxdepth 3 -type d -printf "%M %u %g %p\n""
```

The directory structure includes the project-management area and its subdirectories.

**Screenshot:**

![alt text](<2026-08-21 18_21_31-screenshots to be saved - File Explorer.png>)

---

## 11. Access Control Lists (ACLs)

ACL permissions were checked on the relevant team and project directories.

Example command:

```bash
ansible control_node_1 -b -m shell -a "getfacl /opt/teams/dev-team /opt/teams/devops-team /opt/teams/admin-group"
```

The ACL output was used to verify the access granted to the different groups.

**Screenshot:**

![alt text](<2026-08-20 21_39_52-Downloads - File Explorer.png>)

---

## 12. Project Directory ACL Verification

The ACLs for the project directories were also checked.

Command used:

```bash
ansible control_node_1 -b -m shell -a "getfacl /opt/project-management/shared-resources /opt/project-management/archive /opt/project-management/admin-area"
```

This verifies the permissions and default ACL entries configured for the project-management directories.

**Screenshot:**

![alt text](<2026-08-21 18_23_21-.png>)

---



---

## Summary

This assignment demonstrates basic Ansible administration tasks such as:

- Checking the Ansible environment
- Verifying inventory and connectivity
- Installing packages
- Creating groups
- Creating users
- Assigning users to groups
- Configuring login shells
- Checking password policies
- Creating and verifying workspace directories
- Managing project directories
- Configuring and checking ACL permissions
- Verifying the final configuration

The screenshots attached in each section provide evidence of the commands executed and their output.
