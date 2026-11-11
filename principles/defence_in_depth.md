---
title: Defence in depth
short_title: Defence in depth
---


## Principle


Apply multiple layers of security so that the failure or bypass of a single control does not result in full compromise.

## Objective

Reduce the impact of security failures by layering independent controls that slow attackers, increase detection and prevent single points of compromise.

## Checklist

### Layer controls around critical assets

- Identify the most sensitive assets (e.g. credentials, control functions, customer data).
- Ensure that more than one independent control protects each asset.
- Where critical data or commands cross trust boundaries, consider integrity and freshness controls that remain effective if another protection layer, such as transport security, is bypassed.

### Assume control failure and design for containment

- Design systems so that bypass of one control does not grant unrestricted access.

### Enable detection at multiple layers

- Generate security-relevant logs at key layers (identity, network, application, data).
- Ensure that failed and suspicious actions raise alerts and are not silently blocked.

### Use diverse and independent controls

- Avoid relying on multiple controls that share the same technology, identity source or trust assumption.

### Slow attackers and enable response

- Apply controls that increase attacker effort and time (rate limits, step-up authentication, staged access).
- Ensure that response mechanisms exist to contain or isolate compromised components when alerts are triggered.

## Minimum evidence

- Layered protection mapped: a simple table or diagram showing critical assets and the multiple security controls protecting each (stored in repo/wiki).
- Independent controls confirmed: configuration or design notes showing that layered controls do not rely on the same identity source or enforcement point.
- Multilayer logging enabled: log configuration or sample logs showing security events recorded across controls.
- Alerts and response defined: alert rules or playbook reference showing how suspicious activity is detected and the associated containment or response action.

## Release gate

- [ ] Critical assets have more than one security control applied and the layering is documented.
- [ ] Layered controls are independent; no single identity source or enforcement point is a single point of failure.
- [ ] Security-relevant events are logged at multiple layers
- [ ] Alerts are in place for suspicious activity and a response action exists
