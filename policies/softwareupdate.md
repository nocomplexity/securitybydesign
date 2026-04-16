---
title: Software Update Policy 
short_title: Software Update Policy
---

It is essential that the processes and mechanisms for updating software are robust, reliable, and secure. If devices are not regularly updated, their security will degrade over time as new vulnerabilities and attack methods emerge.

However, IoT devices are often constrained by limited resources, such as energy (e.g. button-cell batteries), processing capability (e.g. for cryptographic operations), or communication capacity (e.g. low bandwidth). These constraints may limit the size, frequency, or timeliness of software updates, and in some cases may prevent updates altogether. In such situations, careful consideration must be given to the security risks associated with deploying and operating these devices.

Both system developers and users must establish and maintain a clear policy for updating software on devices in the field. This policy should address the following:

1. **Lifecycle management of connected devices**, including:
   a. Maintaining an accurate and up-to-date inventory of devices within the organisation.  
   b. Tracking and managing software version information for all deployed devices.  
   c. Defining processes for scheduled updates and the rapid deployment of critical patches.  
   d. Identifying devices that cannot be updated but have known vulnerabilities, and implementing measures to prevent them from compromising system security (e.g. device revocation or isolation).  
   e. Securely managing changes in device ownership.  
   f. Securely managing devices at the end of their operational life.  

+++


2. **A clear and well-publicised process for managing software vulnerabilities and errata**, which:
   a. Enables developers, users, and security researchers to report vulnerabilities and issues.  
   b. Supports timely and transparent communication with affected users.  
   c. Defines how affected configurations are identified.  
   d. Establishes criteria for when a software update must be developed and released.  
   e. Determines the urgency of updates based on the potential impact on users, the vendor, and the wider network, as well as the impact of deploying the update itself.  
   f. Specifies procedures for securely updating device software.  
   g. Assigns clear ownership and escalation paths within the organisation.  

+++


3. **Clearly defined update mechanisms within the software architecture**, ensuring that updates can be delivered securely and reliably.

+++

4. Recognition that existing standards for software patching (e.g. NIST SP 800-40) may require adaptation to address the specific constraints and risks associated with IoT systems.
