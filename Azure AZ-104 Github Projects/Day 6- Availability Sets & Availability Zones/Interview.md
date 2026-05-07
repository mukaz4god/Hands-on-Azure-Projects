# Day 06 – Interview Questions and Answers: Availability Sets and Availability Zones

## 1. What is an Availability Set?

An Availability Set is a logical grouping of Azure virtual machines that helps keep workloads available during planned maintenance and hardware failures.

It spreads VMs across:

- Fault domains
- Update domains

---

## 2. What is a fault domain?

A fault domain is a group of hardware that shares common power and network infrastructure. Azure places VMs across different fault domains so that a single hardware failure is less likely to affect all VMs.

---

## 3. What is an update domain?

An update domain is a group of VMs that may be rebooted together during planned Azure maintenance. Azure updates one update domain at a time.

---

## 4. What is an Availability Zone?

An Availability Zone is a physically separate datacenter location inside an Azure region. Zones are designed to protect applications from datacenter-level failures.

---

## 5. What is the difference between Availability Sets and Availability Zones?

| Availability Set | Availability Zone |
|---|---|
| Protects against rack/host failures | Protects against datacenter failures |
| Uses fault and update domains | Uses physically separate zones |
| Logical grouping | Physical separation |
| Useful where zones are unavailable | Better for higher resilience |

---

## 6. Can you move an existing VM into an Availability Set?

Not as a simple setting change. The VM must be associated with the Availability Set during creation. In real environments, this usually requires redeployment or rebuilding the VM configuration.

---

## 7. Can you move an existing VM into an Availability Zone?

Not as a simple property change. Zone placement is normally selected at VM creation. Migration may require redeployment or migration tooling.

---

## 8. Which provides stronger resilience?

Availability Zones usually provide stronger resilience because they protect against datacenter-level failure.

---

## 9. When would you use Availability Sets?

Use Availability Sets when:

- Availability Zones are not available in the region.
- You need protection from hardware failure and planned maintenance.
- You need lower latency between VMs in the same regional environment.

---

## 10. When would you use Availability Zones?

Use Availability Zones when:

- The workload is production critical.
- You need datacenter-level resilience.
- The selected Azure region and VM SKU support zones.

---

## 11. How does this lab relate to the Azure Well-Architected Framework?

This lab supports the Reliability pillar by reducing single points of failure and improving workload availability.

---

## 12. How would you explain this project in an interview?

I designed and deployed two Azure VM resilience patterns. First, I created an Availability Set and deployed two VMs into it to understand fault domains and update domains. Then I deployed two VMs across separate Availability Zones to provide datacenter-level resilience. I validated both deployments using Azure CLI and the Azure Portal, documented the differences, and captured evidence for GitHub.
