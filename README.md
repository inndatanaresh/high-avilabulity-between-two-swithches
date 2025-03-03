# High Availability Configuration Between Two Switches Using VRRP

## **1. Requirements**
To achieve high availability between two switches, we need:
- Two **Layer 2 (L2) or Layer 3 (L3) switches** connected in parallel.
- VLAN configuration on both switches.
- **VRRP (Virtual Router Redundancy Protocol)** setup for redundancy.

## **2. VLAN Configuration**
Before configuring VRRP, ensure that both switches have VLAN configured properly.

### **Configure VLAN on Switches**
```plaintext
Switch1(config)# vlan 1
Switch1(config-vlan)# exit

Switch2(config)# vlan 1
Switch2(config-vlan)# exit
```

## **3. VRRP Configuration for High Availability**
VRRP enables two or more switches to share a virtual IP address. If the primary switch fails, the backup switch will take over seamlessly.

### **Steps to Configure VRRP**
1. Enable configuration mode on the switch.
2. Configure the VLAN interface.
3. Assign IP addresses to the switches.
4. Set the **virtual IP address** (different from physical IPs).
5. Assign **priority values** (higher priority switch acts as the master).

### **Switch 1 (Primary)**
```plaintext
Switch1(config)# interface vlan 1
Switch1(config-if)# ip address 192.168.1.1 255.255.255.0
Switch1(config-if)# vrrp 1 ip 192.168.1.3
Switch1(config-if)# vrrp 1 priority 110
Switch1(config-if)# no shutdown
```

### **Switch 2 (Backup)**
```plaintext
Switch2(config)# interface vlan 1
Switch2(config-if)# ip address 192.168.1.2 255.255.255.0
Switch2(config-if)# vrrp 1 ip 192.168.1.3
Switch2(config-if)# vrrp 1 priority 100
Switch2(config-if)# no shutdown
```

> **Note:** The virtual IP address `192.168.1.3` is shared by both switches. Clients should use this IP as the default gateway.

## **4. Verification Commands**
After configuration, verify the VRRP setup with the following commands:
```plaintext
Switch1# show vrrp
Switch2# show vrrp
```
This will display the VRRP status, indicating which switch is the **Master** and which is the **Backup**.

## **5. Expected Behavior During Failure**
- **Normal Operation:** Switch 1 is the **master**, handling all traffic.
- **Failure Scenario:** If Switch 1 fails, Switch 2 becomes the new master and continues handling traffic using `192.168.1.3`.
- **Recovery:** When Switch 1 comes back online, it resumes its role as master (due to higher priority).

## **6. Best Practices**
- **Use VRRP-only (avoid mixing with HSRP or GLBP)** to prevent conflicts.
- **Ensure spanning tree protocol (STP) is configured** if L2 switches are used to prevent loops.
- **If using L3 switches, configure routing protocols** like OSPF or static routes to ensure proper network communication.

## **7. Conclusion**
By following this configuration, we ensure that in case of a switch failure, the backup switch seamlessly takes over, maintaining uninterrupted network connectivity. This enhances redundancy and network reliability.

