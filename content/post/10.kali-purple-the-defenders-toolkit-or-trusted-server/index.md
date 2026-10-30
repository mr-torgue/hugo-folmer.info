---
title: 'Kali Purple: the Defenders Toolkit or Trusted Server'
date: '2021-11-15'
---

Offensive Security, the creators of Kali Linux, have come out with a new flavor of Kali Linux, called Kali Purple.
Traditionally, Kali Linux focused on offense and contained all kinds of hacking tools.
Kali Purple is different. It focuses more on the defender side.
For this blog post we will take a deep dive into Kali Purple:
1. What tools does it offer?
2. How secure is it?

# 

# Security
For security, we consider two aspects: hardening and monitoring. 
This corresponds broadly to prevention and detection.
For hardening we evaluate Kali Purple by using the generic linux CIS benchmark and lynis.
We compare it to two well-known security distributions: Rocky and Ubuntu.

## Methodology
We have three operating systems we want to test: Rocky(CentOS), Ubuntu Server, and Kali Purple.
We download all machines as ISOs and create a VM in VirtualBox with the same specs.
The installation is done as follows: at every point, we choose the most secure option.
So, if the installer asks if we want to encrypt our harddrive, we select yes.
This way we get the safest default version.
We install the same version of lynis by downloading it from github.
We do the same for the OpenSCAP tool and CIS-CAT-Lite.
We are particularly interested in the lynis hardening score, and the CIS compliance score.
We use two CIS benchmarks to test reliability of the results.
Operating systems remain unchanged.

For clarity we test the following operating systems(x86-64):
1. Rocky 9.2
2. Ubuntu Server 22.04.2
3. Kali Purple ?

We use the following tools:
1. Lynis 3.0.8
2. CIS-CAT-Lite v?
3. OpenScap v?

## Results
The following table shows the results:

| OS | Lynis Hardening Score | CIS Compliance Score |
| -- | ----- --------- ----- | --- ---------- ----- |
| Rocky 9.2 | ? | ? |
| Ubuntu Server 22.04 | ? | ? |
| Kali Purple ? | ? | ? |

Interestingly enough, Rocky 9.2 asks for a security profile.
Since we are interested in CIS, we just selected this.
## Monitoring
For this we just considered the monitoring capabilities of Kali Purple.
There is no comparison to other solutions, because to my knowledge none of the operating systems provide this feature.

# Discussion
We have to take into account that th

# Conclusion
There is still a lot of work to do for system hardening.
A good way to start is by looking at how Rocky tackles the problem.
By building in security policies, the system is basically hardened from the start.

For mon