# Carving Memory to extract secrets

In addition to the princeton PoC tools to find things like 

## Volatility Framework
Installation: Volatility is a Python script, so you'll need Python installed on your system. You can then install Volatility using pip:

`pip install volatility`

Get a list of commands: After installation, you can see a list of available commands using:

`vol.py --info`

Identify the profile: The first step is identifying the correct profile for the memory dump. The profile is essentially the version of the OS from which the dump was taken. You can do this using the imageinfo command:

`vol.py -f [memory file] imageinfo `

Perform analysis: After that, you can use the relevant command for your analysis. For example, you can use the pstree command to see a tree of all the processes in the memory dump:

`vol.py -f [memory file] --profile=[Profile] pstree`

There are many other plugins you can use to extract information such as command history, network connections, open files, loaded DLLs, registry hives, etc.

**BitLocker Keys**: There's no specific plugin in Volatility for BitLocker keys. However, BitLocker keys are typically stored in the LSASS process, and you can dump that process's memory using the procdump command. Then you can manually search the dump file for potential keys.

`vol.py -f [memory file] --profile=[Profile] procdump -p [LSASS PID] -D dump/`

**Windows Credentials**: The mimikatz plugin can be used to extract Windows credentials. It uses various techniques to attempt to reveal plaintext passwords, hashes, Pin codes, and Kerberos tickets.

`vol.py -f [memory file] --profile=[Profile] mimikatz`

**Other Possible Credential Materials/Secrets**: Various plugins can reveal different types of potentially sensitive information. For example, clipboard can reveal data in the clipboard, cmdscan can reveal command-line inputs, and browserhistory can reveal browsing history.

```
vol.py -f [memory file] --profile=[Profile] clipboard
vol.py -f [memory file] --profile=[Profile] cmdscan
vol.py -f [memory file] --profile=[Profile] browserhistory
```

-----

## Rekall Forensic
Installation: You can install Rekall in a Python virtual environment:

`virtualenv /tmp/MyEnv source /tmp/MyEnv/bin/activate pip install --upgrade setuptools pip wheel pip install rekall-agent rekall`

Session creation: To start working with Rekall, you need to create a new session and specify the file you want to work with:

`rekall -f [memory file]`

Profile selection: Rekall will attempt to guess the correct profile automatically. If it doesn't, you can specify it manually in the session creation:

`rekall -f [memory file] --profile=[Profile]`

Data Analysis: After that, you can start running plugins to analyze the data. For example, to view running processes, you can use the pslist plugin:

`rekall pslist`

**BitLocker Keys and Windows Credentials**: You can use the procdump plugin to dump the memory of a process to an output file. Typically, the LSASS process might contain sensitive data like BitLocker keys or Windows credentials.

`rekall procdump --pid [pid] --dump-dir=/path/to/dump`

**Other Possible Credential Materials/Secrets**: The session plugin can be used to reveal active logon sessions, the netscan plugin can be used to reveal network activity, and the cmdscan plugin can be used to reveal command-line inputs.

```
rekall session
rekall netscan
rekall cmdscan
```


-------

## Commercial Tooling

* Tool: Passware - https://www.passware.com/
* Guide: https://www.raedts.biz/security/extract-bitlocker-key-ram-dump-passware/

* Tool: Elcomsoft Forensic Disk Decryptor - https://www.elcomsoft.com/efdd.html
* Guide: https://blog.elcomsoft.com/2020/05/unlocking-bitlocker-can-you-break-that-password/

------ 

## Other memory dumps

* Windbg, VirtualBox snapshot, VMware snapshot, hiberfil.sys - https://diverto.github.io/2019/11/05/Extracting-Passwords-from-hiberfil-and-memdumps 
