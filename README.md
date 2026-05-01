# ENUMERATION-PART1_VA

# IKB21403 Vulnerability Analysis — Chapter 5: Enumeration

## 10-Challenge Checklist:
- Challenge 2: Fast Nmap Scan 
- Challenge 16: Version Detection 
- Challenge 17: OS Detection 
- Challenge 11: SMB NSE Enumeration 
- Challenge 9: FTP Banner 
- Challenge 10: Anonymous FTP Login 
- Challenge 7: SMTP VRFY 
- Challenge 12: Enum4linux 
- Challenge 5: TTL OS Fingerprinting 
- Challenge 1: NetBIOS Enumeration 

---

## Lab Environment Setup
- **Attacker Machine:** Kali Linux 
- **Victim Machine:** Metasploitable2 
- **Target IP:** `192.168.56.110`

---

## Technical Challenges Report

### Challenge 1 — NetBIOS Enumeration
- **Objective:** Identify the NetBIOS name, workgroup, and services running via NetBIOS.
- **Command:** `nbtscan 192.168.56.110`
- **Findings:**
  - NetBIOS Name: `METASPLOITABLE`
- **Screenshot:** *<img width="623" height="227" alt="nbtscan" src="https://github.com/user-attachments/assets/11c0b9bb-3c35-4965-9a30-48227d4dde17" />*

### Challenge 2 — Fast Nmap Scan
- **Objective:** Quickly discover active services on the target.
- **Command:** `nmap -F 192.168.56.110`
- **Findings:**
  - Scanned the top 100 common ports.
  - Identified multiple open ports including 21 (FTP), 22 (SSH), 23 (Telnet), 25 (SMTP), 80 (HTTP), and 445 (SMB).
- **Screenshot:** *<img width="516" height="442" alt="nmap -F" src="https://github.com/user-attachments/assets/3088d942-eed9-432c-a9c7-cc536311a0a3" />*

### Challenge 5 — TTL OS Fingerprinting
- **Objective:** Guess the operating system based on the TTL (Time to Live) value of an ICMP response.
- **Command:** `ping -c 4 192.168.56.110`
- **Findings:**
  - Average TTL returned: `64`
  - **Interpretation:** A TTL value of `64` indicates that the host system is running a Linux/Unix operating system.
- **Screenshot:** *<img width="507" height="188" alt="ping" src="https://github.com/user-attachments/assets/b45966b9-cd47-46c9-aa06-f6ae7336907c" />*

### Challenge 7 — SMTP VRFY
- **Objective:** Enumerate valid usernames via the Simple Mail Transfer Protocol (SMTP).
- **Command:** `nc 192.168.56.110 25` followed by `VRFY root`
- **Findings:**
  - Response: `250 2.1.5 root`
  - **Interpretation:** The server confirmed that the user `root` is a valid account on the target machine.
- **Screenshot:** *<img width="437" height="122" alt="SMTP VRFY" src="https://github.com/user-attachments/assets/a65d87aa-8717-4591-9d09-90a37a4eea5d" />*

### Challenge 9 — FTP Banner
- **Objective:** Extract the exact version of the FTP daemon running.
- **Command:** `nc 192.168.56.110 21`
- **Findings:**
  - Banner Output: `220 (vsFTPd 2.3.4)`
  - **Interpretation:** The server is running vsFTPd version 2.3.4, which is a key indicator for checking specific version-related vulnerabilities.
- **Screenshot:** *<img width="223" height="70" alt="FTP Banner" src="https://github.com/user-attachments/assets/1d2fc4e7-a395-4670-b4b1-b69da0852b14" />*

### Challenge 10 — Anonymous FTP Login
- **Objective:** Test if the FTP server allows unauthorized access via the anonymous user account.
- **Command:** `ftp 192.168.56.110` (Log in as `anonymous` with a blank password).
- **Findings:**
  - Response: `230 Login successful.`
  - **Interpretation:** Vulnerability confirmed. Anyone can connect and list directories/files without credentials.
- **Screenshot:** *<img width="388" height="256" alt="ftp login" src="https://github.com/user-attachments/assets/b02c97c0-553e-4588-8afd-f0d1558f80c9" />*

### Challenge 11 — SMB NSE Enumeration
- **Objective:** Use Nmap's Scripting Engine (NSE) to gather detailed SMB operating system profiles and user information.
- **Command:** `nmap --script smb-os-discovery & nmap --script smb-enum-users -p 445 192.168.56.110`
- **Findings:**
  - Identified target OS running Samba on Linux.
  - Confirmed local domain/workgroup structure.
- **Screenshots:** *<img width="518" height="313" alt="SMB NSE" src="https://github.com/user-attachments/assets/181268fc-9867-494f-8c6b-398a2be42246" />*
  
  *<img width="518" height="455" alt="SMB NSE01" src="https://github.com/user-attachments/assets/4df3f19a-60e0-4ff1-a59a-8b0cd1ec0518" />*

  *<img width="480" height="132" alt="SMB NSE02" src="https://github.com/user-attachments/assets/26e36b01-074b-41eb-812b-523a158b9858" />*

### Challenge 12 — Enum4linux
- **Objective:** Conduct in-depth enumeration of local users, groups, and SMB shares.
- **Command:** `enum4linux -a 192.168.56.110`
- **Findings:**
  - Discovered a publicly accessible and browseable SMB shared directory (`/tmp` with `Mapping: OK Listing: OK`).
  - Access to `/opt` was correctly denied (`DENIED`).
- **Screenshot:** *<img width="624" height="409" alt="Enum4linux" src="https://github.com/user-attachments/assets/ee5a0391-7a8e-4d6a-a2a3-f8952f3fea68" />*

  *<img width="631" height="392" alt="Enum4linux01" src="https://github.com/user-attachments/assets/c1b12d0f-0279-46f7-81df-d31c82acbc04" />*

### Challenge 16 — Version Detection
- **Objective:** Perform banner grabbing and service probe detection to find exact software versions.
- **Command:** `nmap -sV 192.168.56.110`
- **Findings:**
  - Discovered running versions: `vsFTPd 2.3.4` (FTP), `OpenSSH 4.7p1` (SSH), `Apache httpd 2.2.8` (HTTP).
  - This provides exact targets for software vulnerability research.
- **Screenshot:** *<img width="587" height="452" alt="nmap -sV" src="https://github.com/user-attachments/assets/f494a92a-1952-4baf-8fc6-75770e924260" />*

  *<img width="623" height="131" alt="nmap -sV01" src="https://github.com/user-attachments/assets/c576b1de-c4bc-4a79-95a0-5daef0607b48" />*

### Challenge 17 — OS Detection
- **Objective:** Fingerprint the operating system by analyzing network packet responses.
- **Command:** `sudo nmap -O 192.168.56.110`
- **Findings:**
  - Matches the previously identified TTL value from Challenge 5.
- **Screenshot:** *<img width="530" height="422" alt="nmap -O" src="https://github.com/user-attachments/assets/bc0c21bc-e2c9-4e13-9f91-9dcf95ecc118" />*

  *<img width="627" height="292" alt="nmap -O01" src="https://github.com/user-attachments/assets/9be46c2a-be31-4963-a56c-c0a90470b89f" />*

---

## Conclusion
This 10 challenges performed reveal a highly insecure system layout with legacy services and active misconfigurations, including unrestricted anonymous FTP access, unsecured SMB shares, and verbose service information banners.
