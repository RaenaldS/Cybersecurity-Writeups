# 🕵️‍♂️ BTLO: Poisoned Aid - DFIR Investigation

**Platform**: Blue Team Labs Online (BTLO)  
**Category**: Digital Forensics & Incident Response (DFIR)  
**Status**: ✅ Completed

---

## 📖 Scenario

> *Khaled, an employee at EZ-CERT, received what appeared to be a legitimate email containing information about an ongoing humanitarian aid initiative. Believing the attachment was a harmless aid coordination document, he downloaded and opened the file on his corporate workstation. Shortly afterward, the Security Operations Center identified suspicious activity originating from Khaled's endpoint, including unusual network connections and signs of potential unauthorized execution. Concerned that the incident may involve more than a simple phishing attempt, the incident response team acquired a triage image of Khaled's machine to preserve forensic artifacts.*

**Objective**: Analyze the triage image, identify the malicious payload and persistence mechanisms, and establish a full timeline of the compromise.

---

## 🛠️ Tools Used

- **Wireshark** – Network traffic analysis
- **PECmd (Eric Zimmerman)** – Prefetch file analysis
- **Registry Explorer** – Registry hive analysis
- **DB Browser (SQLite)** – Database analysis
- **Notepad++** – Log and file inspection

---

## 📊 Investigation Findings

| # | Question | Answer |
|---|----------|--------|
| 1 | Full URL of the decoy PDF | `http://159.198.41.140/static/builder/lnk_uploads/invo.pdf` |
| 2 | Time Khaled opened the malicious file | `2026-05-31 18:34:34` |
| 3 | Attacker's web server version | `nginx/1.24.0` |
| 4 | Full path of the staging directory | `C:\Users\Khaled.Allam\AppData\Roaming\WindowsHelper` |
| 5 | Name of the malicious module | `module.pyw` |
| 6 | Obfuscation timestamp of the module | `2026-05-28 13:00:26.488` |
| 7 | Time persistence mechanism was implanted | `2026-05-31 18:36:07` |
| 8 | Total number of files scanned | `1138` |
| 9 | Total size of exfiltrated data | `13 MB` |
| 10 | Exfiltration endpoint | `/upload_file` |
| 11 | Stolen credentials (email:password) | `00darksideofme00@gmail.com:AmT1redAlfr3d` |

---

## 🔍 Key Investigation Steps

### 1. Network Analysis (Wireshark)
- Filtered HTTP traffic using `http.request.uri contains "pdf"` to locate the decoy document download.
- Analyzed HTTP stream to identify the web server banner: `nginx/1.24.0`.

### 2. Execution Timeline (PECmd)
- Dumped Prefetch files (`.pf`) and converted them to CSV.
- Correlated the timeline to identify `CMD.EXE` and `RUNDLL32.EXE` executing simultaneously at the exact moment of compromise.

### 3. Staging Directory & Persistence
- Discovered the folder `WindowsHelper` under `AppData\Roaming`.
- Located a malicious scheduled task named `windowshelper` inside `System32\Tasks` to maintain persistence.

### 4. Database Analysis (SQLite)
- Opened `inventory_state.db` (found in the staging directory) using DB Browser to count the total scanned files.

### 5. Credential Theft
- Extracted email from Chrome's local extension storage (`Local Extension Settings\nngcec~\000013.ldb`).
- Retrieved the password from keystroke/clipboard logs stored in the `WindowsHelper` directory.

---

## 📸 Screenshots

*Place your investigation screenshots here:*

![Wireshark - Decoy PDF](images/wireshark-decoy.png)
*Figure 1: HTTP filter showing the decoy PDF request.*

![PECmd - Timeline](images/pecmd-timeline.png)
*Figure 2: Prefetch timeline showing the malicious execution.*

![SQLite - Scanned Files](images/sqlite-count.png)
*Figure 3: DB Browser showing the total file count from inventory_state.db.*

---

## 📝 Key Takeaways

- **Phishing remains a primary initial access vector** – user awareness training is critical.
- **Prefetch files are golden** for establishing precise execution timelines during an investigation.
- **Malware often hides in plain sight** – staging directories mimic legitimate Windows paths (`WindowsHelper`).
- **SQLite databases left by infostealers** can reveal the exact scope of data harvested.
- **Persistence mechanisms (Scheduled Tasks)** ensure malware survives reboots, making them a top priority for remediation.

---

## 🔗 External Links

- 📖 **Full Walkthrough (Medium)**: [Read Here](https://medium.com/@raenaldsyaputra57/poisoned-aid-btlo-walkthrough-496d90100cdf)
- 🏆 **BTLO Achievement**: [View Certificate](https://blueteamlabs.online/achievement/share/161475/304)
- 📂 **Back to Main Repository**: [../](../README.md)

---

*Written with 🖥️ by Renaldy Syaputra*
