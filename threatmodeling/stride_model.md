---
title: STRIDE Threat Model
short_title: STRIDE Threat Model
---


**STRIDE Threat Model**


| **Threat**                 | **Threat Definition**                                                                              |
|----------------------------|----------------------------------------------------------------------------------------------------|
| **Spoofing**               | Pretending to be something or someone other than oneself                                           |
| **Tampering**              | Unauthorised modification of data on disk, in network transit, in memory, or in other locations    |
| **Repudiation**            | Denying responsibility for an action or event (whether the claim is true or false)                 |
| **Information Disclosure** | Unauthorised access to or exposure of sensitive or confidential information                        |
| **Denial of Service**      | Making a service unavailable by exhausting or depleting required resources                         |
| **Elevation of Privilege** | Gaining higher access rights or permissions than originally authorised                             |

:::{note} 
- This table is commonly used as the foundation for threat modelling for systems, processes, software libraries, SaaS services, etc.

- STRIDE is a mature threat-modeling method that is used at large worldwide. For more information check [Open Security Reference Architecture](https://nocomplexity.com/documents/securityarchitecture/introduction.html).

:::

## Recommended Threat Modeling Steps

### Step 1: Define security requirements

1. Define security requirements
2. Create an application diagram
3. Identify threats
4. Mitigate threats
5. Validate that threats have been mitigated

### Step 2: Creating a Simple Design Representation

When applying STRIDE , create a simple representation of your design. Typically, this is done by creating a simple diagram. 

1. Data processes are represented with circles
2. Data stores are represented with lines above and below their names (you may also see them as cylinders)
3. Data flows are represented with directed lines; these include data flows over a network
4. Interactors 
5. Trust boundaries are represented with a dashed line; these represent the border between trusted and untrusted portions.


Everything except the trust boundaries, processes, data stores, data flows, and interactors, are considered elements.

The idea is to have a simple model of the design that shows the essential features. Here are some quick rules of thumb for a good representation:

- Every data store should have at least one input and at least one output ("no data coming out of thin air").
- Only processes read or write data in data stores ("no psychokinesis").
- Similar elements in a single trust boundary can be collapsed into one element ("make the model simple").

### Step 3: Identify Threats Using STRIDE

When applying STRIDE examine each of the elements (processes, data stores, data flows, and interactors) to determine the threats to which it is susceptible. For each element, you look for the threats as shown in this table:


| Threat | Property Violated | Threat Definition |
|--------|-------------------|--------------------|
| S | Spoofing Identity | Authentication | Pretending to be something or someone other than yourself |
| T | Tampering with Data | Integrity | Modifying something on disk, network, memory, or elsewhere |
| R | Repudiation | Non-repudiation | Claiming that you didn't do something or were not responsible; can be honest or false |
| I | Information Disclosure | Confidentiality | Providing information to someone not authorized to access it |
| D | Denial of Service | Availability | Exhausting resources needed to provide service |
| E | Elevation of Privilege | Authorization | Allowing someone to do something they are not authorized to do |