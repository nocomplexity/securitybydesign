% TAGS [PREVENTION] [SLDC]

# How to trust software?

## Problem

One of the most important and difficult issues with cyber security is trust. Trust is good and often essential, since it is not possible for one single human to oversee and truly understand all aspects. Technology is simply too complex and dealing with business risks and business processes is another discipline. 

But whenever possible: **Never trust and assume but verify\!** 

This accounts also for using software.

Anyone can inspect the source code of FOSS solutions for malicious flaws. But most software is distributed pre-compiled with no method to confirm whether they correspond. 

Attacks on developers who create or release software including their tools are vulnerable for creating malicious flaws. Mostly not on purpose but due to political influence, blackmail or severe threats or violence to relatives.

## Solution

Trusting software code is not the same as trusting its executable that is created for a target platform (Linux, BSD, Windows, etc). For every target platform software must be compiled and packaged. 

A minimum way to verify your download is to check a hash. Checking a hash for a software download serves a crucial purpose in ensuring the integrity and authenticity for the software package: 

- Integrity Verification: A hash is a unique "digital fingerprint" of a file. Any alteration to the file, even a tiny change, will result in a completely different hash value. By comparing the hash of the downloaded file with the hash provided by the software's publisher, you can verify that the file hasn't been corrupted during download or tampered with by a third party.   This is especially important for large files, where the risk of data corruption during transfer is higher.  
- Authenticity Verification: Checking a hash also helps to ensure that the downloaded file is indeed the genuine software from the intended source. If a malicious actor tries to distribute a modified version of the software, the hash will not match the official one, alerting you to a potential threat.

:::{warning}  
Checking a hash is not  sufficient\!   
:::

If you really want to be sure that software downloads you want to use are not tampered with you need a [reproducible build](https://nocomplexity.com/documents/securityarchitecture/prevention/reproduciblebuilds.html#reproducible-builds).

Reproducible Builds provide certainty that software is genuine and has not been tampered with. Reproducible build offer the following advantages from a security point of view:

- Trust: If software is altered this will be detected.  
- Transparency: Using a reproducible build ensures that software code always works the same.   
- Protection of build infrastructure: Using reproducible build detect unauthorized changes to the build process and other types of supply chain attacks.

Creating software that matches the reproducible build standard is not simple. However as end-user or business you should have an easy task: Demand that the software you want to use has a reproducible build. The supplier of software that claims to use the reproducible-build standard and process  should you provide a way to recreate a close enough build environment, perform the build process, and validate that the output matches the original build. Even if you do not recreate the build continuously, it can and should be done periodically or for an audit.

More information:

- [https://nocomplexity.com/documents/securityarchitecture/prevention/reproduciblebuilds.html\#reproducible-builds](https://nocomplexity.com/documents/securityarchitecture/prevention/reproduciblebuilds.html#reproducible-builds) 

- [https://reproducible-builds.org/](https://reproducible-builds.org/) 