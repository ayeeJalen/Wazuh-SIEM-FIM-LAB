# Wazuh-SIEM-FIM-LAB
Two-laptop Wazuh SIEM/XDR home lab demonstrating endpoint monitoring and File Integrity Monitoring.
# Wazuh SIEM & File Integrity Monitoring Lab

## Overview

This project is a cybersecurity home lab using **Wazuh SIEM/XDR**.

The goal of this lab was to set up Wazuh and use it to watch a Windows computer for file changes.

I used **two physical laptops**:

* A Linux laptop running the Wazuh Manager and Dashboard
* A Windows laptop running the Wazuh Agent

I also set up **File Integrity Monitoring (FIM)** to watch a folder on the Windows computer. I then created and changed a file to make sure Wazuh could detect the changes.

---

## Lab Setup

The lab used two computers.

### Linux Laptop

The Linux laptop was used to run:

* Wazuh Manager
* Wazuh Dashboard

The Wazuh Manager is like the **main brain** of the Wazuh system. It receives information from the Windows computer.

The Wazuh Dashboard is where I can look at security events and alerts.

### Windows Laptop

The Windows laptop was used as the computer being monitored.

It had the:

* Wazuh Agent
* File Integrity Monitoring configuration
* Test folder

The Wazuh Agent sends information from the Windows computer to the Wazuh Manager.

---

## Lab Architecture

```text
             Linux Laptop
        ┌─────────────────────┐
        │    Wazuh Manager    │
        │         +           │
        │   Wazuh Dashboard   │
        └──────────┬──────────┘
                   │
                   │ Network
                   │
        ┌──────────▼──────────┐
        │   Windows Laptop    │
        │                     │
        │    Wazuh Agent      │
        │         ↓           │
        │    FIM / Syscheck   │
        │         ↓           │
        │      WAZUHTEST      │
        └─────────────────────┘
```

---

## Technologies Used

* Wazuh
* Wazuh Manager
* Wazuh Dashboard
* Wazuh Agent
* File Integrity Monitoring (FIM)
* Syscheck
* Linux
* Windows
* PowerShell
* XML
* Windows Services

---

# Step 1: Wazuh Manager

The first part of the lab was setting up the Wazuh Manager on the Linux laptop.

The Wazuh Manager receives security information from the Windows Wazuh Agent.

The Wazuh Dashboard allows me to view this information.

### Screenshot 01 — Wazuh Dashboard Overview

![Wazuh Dashboard Overview](screenshots/01-wazuh-dashboard-overview.png)

This is the main Wazuh screen.

It shows that my Windows laptop is connected to Wazuh. It also shows the alerts Wazuh found and the different security tools that are available.

**What it proves:** Wazuh is working, my Windows computer is connected, and Wazuh is receiving security information.

---

### Screenshot 02 — Wazuh Manager Service Running

![Wazuh Manager Service Running](screenshots/02-wazuh-manager-service-running.png)

This screenshot shows the Wazuh Manager running on my Linux laptop.

The screen says the service is **"active (running)."**

**What it proves:** The Wazuh Manager is installed correctly and running like it should.

---

# Step 2: Wazuh Agent

The next step was setting up the Wazuh Agent on the Windows laptop.

The Wazuh Agent collects information from the Windows computer and sends it to the Wazuh Manager.

### Screenshot 03 — Wazuh Agent Status

![Wazuh Agent Status](screenshots/03-wazuh-agent-status.png)

This is the Wazuh Agent on my Windows laptop.

It shows that the agent is **"Running"** and is connected to the Wazuh Manager.

Private information, such as the Manager IP address and authentication key, was covered up.

**What it proves:** The Wazuh Agent is installed and working on my Windows computer.

---

### Screenshot 04 — Wazuh Agent Service PowerShell

![Wazuh Agent Service PowerShell](screenshots/04-wazuh-agent-service-powershell.png)

This screenshot shows PowerShell checking the Wazuh Agent service.

The result says the service is **"Running."**

**What it proves:** I checked the Wazuh Agent using a Windows command instead of only looking at the Wazuh application.

---

# Step 3: File Integrity Monitoring

The main goal of this part of the lab was to test **File Integrity Monitoring (FIM).**

FIM allows Wazuh to watch files and folders for changes.

For this lab, I created a test folder on my Windows computer:

```text
C:\Users\jalen\Downloads\WAZUHTEST
```

I told Wazuh to watch this folder in real time.

The configuration added to the Wazuh Agent was:

```xml
<directories realtime="yes">C:\Users\jalen\Downloads\WAZUHTEST</directories>
```

The `realtime="yes"` setting tells Wazuh to watch the folder for changes as they happen.

---

### Screenshot 05 — FIM Directory Configuration

![FIM Directory Configuration](screenshots/05-fim-directory-configuration.png)

This screenshot shows the Wazuh configuration file.

It shows the folder I added:

```text
C:\Users\jalen\Downloads\WAZUHTEST
```

This tells Wazuh to watch that folder for changes in real time.

**What it proves:** I personally set up File Integrity Monitoring to watch a folder on my Windows computer.

---

# Step 4: Testing File Integrity Monitoring

After setting up FIM, I tested it by creating and changing a file inside the folder.

For example, I created a test file:

```powershell
New-Item "C:\Users\jalen\Downloads\WAZUHTEST\fim-test.txt"
```

I then changed the file:

```powershell
"Modified for Wazuh FIM test" | Out-File "C:\Users\jalen\Downloads\WAZUHTEST\fim-test.txt"
```

These changes were used to see if Wazuh could detect what happened.

---

# Step 5: FIM Events

After the file was created and changed, Wazuh detected the activity.

### Screenshot 06 — FIM Events Added and Modified

![FIM Added and Modified Events](screenshots/06-fim-events-added-modified.png)

This screenshot shows Wazuh finding two changes.

One event shows that `fim-test.txt` was **added**.

The other event shows that the file was **modified**.

**What it proves:** My FIM test worked. I changed a file, and Wazuh noticed the changes.

---

# Step 6: Looking at the Event Details

Wazuh also allows me to open an event and see more information about what happened.

### Screenshot 07 — FIM Event Details

![FIM Event Detail](screenshots/07-fim-event-detail.png)

This screenshot shows more information about one of the file changes.

Wazuh shows that the file's size and modification time changed.

**What it proves:** Wazuh does more than just say that a file changed. It records details about **what changed**, which can help with security investigations.

---

# How the FIM Test Worked

The test followed this process:

```text
Create File
     ↓
Wazuh Agent Sees Change
     ↓
FIM / Syscheck
     ↓
Wazuh Manager
     ↓
Wazuh Dashboard
     ↓
FIM Event
```

I then changed the file:

```text
Modify File
     ↓
Wazuh Agent Sees Change
     ↓
FIM / Syscheck
     ↓
Wazuh Manager
     ↓
Wazuh Dashboard
     ↓
Modified Event
```

This showed that the entire system was working from the Windows endpoint all the way to the Wazuh Dashboard.

---

# Results

The lab was successful.

I was able to:

* Set up Wazuh Manager on Linux
* Set up Wazuh Agent on Windows
* Connect the Windows Agent to the Wazuh Manager
* Configure File Integrity Monitoring
* Create a custom folder for FIM
* Monitor the folder in real time
* Create a test file
* Modify the test file
* See the file creation event in Wazuh
* See the file modification event in Wazuh
* Open the event and view more details

The test showed that Wazuh could detect file changes on the Windows endpoint.

---

# What the Screenshots Show

The seven screenshots tell the story of the entire lab.

### 1. Wazuh Dashboard

Shows that Wazuh is working and receiving information from the Windows computer.

### 2. Wazuh Manager

Shows that the Wazuh Manager is running on Linux.

### 3. Wazuh Agent

Shows that the Wazuh Agent is running on Windows.

### 4. PowerShell

Shows that the Windows Wazuh service is running.

### 5. FIM Configuration

Shows that I told Wazuh which folder to watch.

### 6. FIM Events

Shows that Wazuh detected the file being added and modified.

### 7. FIM Event Details

Shows the specific information Wazuh recorded about the file change.

Together, the screenshots show:

```text
Wazuh is running
       ↓
Windows Agent is connected
       ↓
FIM was configured
       ↓
FIM was tested
       ↓
Wazuh detected the changes
       ↓
The event was investigated
```

---

# Skills Demonstrated

Through this project, I practiced:

### SIEM

* Setting up Wazuh
* Using the Wazuh Dashboard
* Viewing security events
* Investigating alerts

### File Integrity Monitoring

* Setting up FIM
* Using Syscheck
* Monitoring a custom folder
* Detecting new files
* Detecting modified files
* Viewing file change details

### Windows

* Installing a security agent
* Checking Windows services
* Using PowerShell
* Editing configuration files
* Working with Windows file paths

### Linux

* Installing and running Wazuh Manager
* Checking Linux services
* Using the Linux terminal
* Managing security software

### Troubleshooting

* Fixing configuration problems
* Handling Windows permissions
* Restarting the Wazuh Agent
* Checking whether services are running
* Testing whether FIM events appear

---

# Lessons Learned

This lab taught me how a SIEM can watch a computer and find changes that happen on it.

I also learned that setting up a security tool is not enough. You need to **test it** to make sure it actually works.

For this lab, I created and changed a file and then checked Wazuh to make sure the events appeared.

I also learned how the Wazuh Agent, Wazuh Manager, and Dashboard work together.

---

# Future Improvements

In the future, I could make this lab bigger by:

* Monitoring more Windows folders
* Testing file deletion
* Adding more Windows computers
* Adding a Linux endpoint
* Creating custom Wazuh rules
* Monitoring Windows Event Logs
* Setting up automatic alerts
* Investigating suspicious file changes
* Mapping events to MITRE ATT&CK techniques

---

# Project Outcome

This project successfully demonstrated how to build and test a Wazuh SIEM/XDR lab using two physical laptops.

The Linux laptop ran the Wazuh Manager and Dashboard.

The Windows laptop ran the Wazuh Agent.

I then configured File Integrity Monitoring to watch:

```text
C:\Users\jalen\Downloads\WAZUHTEST
```

After creating and modifying a test file, Wazuh detected both changes and displayed the events in the Wazuh Dashboard.

This project gave me hands-on experience with:

* SIEM
* XDR
* File Integrity Monitoring
* Endpoint Security
* Windows
* Linux
* PowerShell
* Security Event Analysis
* Wazuh
* Troubleshooting

It also gives me a practical project that I can use to demonstrate my cybersecurity skills in my portfolio.
