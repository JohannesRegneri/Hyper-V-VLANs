# Hyper-V-VLANs

From: https://taeffner.net/2022/04/multiple-vlans-windows-10-11-onboard-tools-hyper-v/


This will return a list of network adapters, find your physical NIC and note its "Name" - In most cases "Ethernet"
```powershell
Get-NetAdapter
```

This creates a new vSwitch named VLAN-vSwitch and bridging our physical NIC called "Ethernet". Also we allow to add virtual Host-NICs to this switch.
```powershell
New-VMSwitch -name VLAN-vSwitch -NetAdapterName Ethernet -AllowManagementOS $true
```
Hyper-V automatically creates a virtual NIC without a VLAN tag to keep the host online - Remove it, except you are using a Untagged/Tagged combination.
```powershell
Remove-VMNetworkAdapter -ManagementOS -Name VLAN-vSwitch
```

Now we create a new virtual Host-NIC and assign a VLAN tag 123 to it. Please note, that the interface name can be chosen freely. One might want to name them by purpose.
```powershell
Add-VMNetworkAdapter -ManagementOS -Name "VLAN123" -SwitchName "VLAN-vSwitch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 123
```
You can now add as many virtual NICs as you need

Finally, verify that all adapter are in place
```powershell
Get-NetAdapter
```

List of Virtual Adapters
```powershell
Get-VMNetworkAdapter -All
```


Delete specific Adapter
```powershell
Remove-VMNetworkAdapter -ManagementOS -SwitchName "VLAN-vSwitch" -Name "VLAN123"
```


