# nvidia-kvm-patcher

Quick Instructions:

    1. Start NVIDIA Driver Setup, Exit Before Installing (Unpacks to C:/NVIDIA)
    2. Install Windows 10 WDK
    3. Install Python 3
    4. Enable Testsigning and Reboot
    5. Open Administrator Command Prompt
    6. Run python /path/to/patcher.py C:/NVIDIA/DisplayDriver/Version/Win10_64/International/Display.Driver
    7. Install Driver Through Installer
    
### TLDR

So, so story so far:
You have a VM that uses a passed-through NVIDIA graphics card

However, the driver errored out with code 43, or outright blue-screened your VM

This is because NVIDIA "Introduced a Bug" making their driver "Fail" on "Unsupported configurations", such as having a geforce, by "accidentally" detecting the prescence of a hypervisor

Now, naturally, after some googling, you did something like this (libvirt config):
```xml
    <kvm>
      <hidden state='on'/>
    </kvm>
```
    
And this solves the problem, by masking off virtualization related CPUIDs/MSRs. However, this comes with the following issue:
Naturally, you can't use those virtualization extensions anymore, as the guest has no way of detecting them

Now, supposing we want these extensions enabled, we could be smart and do some magic down at the KVM level to detect when nvidia is probing and only feed it the BS. However, I am not smart, and therefore I am going to do something very dumb...

This script patches the NVIDIA driver to "fix the bug", and then attempts to test sign it (Only setup for Windows 10 x64 currently).

It is fugly, but has been tested to work on NVIDIA drivers 361.91 and 368.39...
For test signing to work without additional modification, one must be on Windows 10 x64, have the WDK installed, and run the script as an administrator.

Use At Your Own Risk...

### Troubleshooting

1. If testsigning fails, make sure you are running Windows 10 x64, and have the WDK Installed
2. If you ran the script without administrator priviledges, or the script otherwise generated the certificate but failed to complete the process, you will manually need to add the generated testsign.cer to trusted root certificates, using certmgr. Alternatively, you can delete the generated TestSign.cer, and remove any instances of SKSoftware using certmgr and run the script again.
3. Windows will attempt to overwrite the driver using versions from windows update, you will want to blacklists these updates using Microsoft's tool: https://support.microsoft.com/en-us/kb/3073930
4. Having another problem? File an Issue. I would love some feedback on my crappy script.

### OS Support

At some time, I plan to update this to support OS such as windows 7 and 8, however, at this time, only Windows 10 x64 is supported


