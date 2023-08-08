<h1>Attach a Firewall to a Virtual Network in Azure</h1>


<h2>Description</h2>
Your company needs to restrict outgoing traffic from their server using a firewall. They want you to block users from visiting anything besides https://microsoft.com, and they don't want to allow port 53 outbound from the server. You will create a firewall and connect it to your virtual network as a solution.
<br />

<h2>Environments Used </h2>

- <b>Azure</b>
- <b>Windows 11</b>
- <b>Power Shell</b>
  
<h2>Program walk-through:</h2>

<h3>Create a Firewall</h3>


1. In the search bar at the top, enter firewall.
2. Select Firewalls in the search results dropdown.
3. Click Add.
4. Set the following values:
- Resource group: Select the existing one in the dropdown.
- Name: Enter fw-1.
- Region: Select the same region as the lab-provided resource group (Confirm the region by duplicating your Azure portal tab, navigating to Resource Groups, and reviewing the resource group's location).
- Firewall SKU: Select Standard.
- Firewall management: Select Use Firewall rules (classic) to manage this firewall.
- Choose a virtual network: Select Use existing.
- Virtual network: Select the existing one in the dropdown.
- Public IP address: Click Add new, name it fw-ip, and click OK.
5. Click Review + create.
6. Click Create.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

<h3>Create a Route Table</h3>


1. Once the deployment is complete, click Go to resource.
2. Copy the Firewall private IP, and save it in a separate text file for later.
3. In the search bar, enter route table.
4. Select Route tables in the search results.
5. Click Add.
6. Set the following values:
- Resource group: Select the existing one in the dropdown.
- Region: Select the same region as the existing resources.
- Name: Enter routetable1.
- Propagate gateway routes: Select Yes.
7. Click Review + create.
8. Click Create.



<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

1. Click Go to resource and navigate to Routes.
2. Click Add.
3. Set the following values:
- Route name: Enter route1.
- Destination address prefix: Select IP Addresses.
- Destination IP addresses/CIDR ranges: Enter 0.0.0.0/0.
- Next hop type: Select Virtual appliance.
- Next hop address: Paste your Azure firewall's private IP.
4. Click Add.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

1. In the left navigation menu, click Subnets.
2. Click Associate.
3. For Virtual network, select lab-VM-VNET.
4. For Subnet, select default.
5. Click OK.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

<h3>Configure Rule Collections for Firewall Add NAT Rule Collection</h3>

1. In the search bar at the top, enter firewalls.
2. Select Firewalls in the search results dropdown.
3. In the left-hand menu under Settings, click on Rules (classic) .
4. Click Add NAT rule collection.
5. In the section at the top, set the following values:
- Name: Enter natcollection.
- Priority: Enter 200.
6. In the Rules section, set the following values:
- Name: Enter rdp.
- Protocol: Enter TCP.
- Source type: Select IP address.
- Source: Enter * (one asterisk).
- Destination type: Select IP address.
- Destination Address: The public IP of your Azure firewall (to retrieve it, open a new Azure portal browser tab and navigate to Resource groups > FW-1. In the left navigation menu, -select Public IP configuration, and copy the IP address).
- Destination Ports: Enter 3389.
- Translated address: The private IP of your virtual machine (to retrieve it, open a new Azure portal browser tab and navigate to Resource groups > service-VM).
- Translated port: Enter 3389.
7. Click Add.



<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

<h3>Add Network Rule Collection</h3>

1. Click Network rule collection.
2. Click Add network rule collection.
3. In the section at the top, set the following values:
- Name: Enter netcollection.
- Priority: Enter 200.
- Action: Select Allow.
4. In the Rules section, under IP Addresses, set the following values:
-Name: Enter dns.
- Protocol: Enter UDP.
- Source type: Select IP address.
- Source: The IPv4 of the default subnet (to retrieve it, navigate back to the services-VM browser tab. In the left navigation menu, select Networking. Click lab-VM-VNET/default. In the -left navigation menu, click Subnets; copy the default IP).
- Destination type: Select IP address.
- Destination Address: Enter 8.8.8.8, 8.8.4.4.
- Destination Ports: Enter 53.
5. Click Add.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

<h3>Add Application Rule Collection</h3>

1. Click Application rule collection.
2. Click Add application rule collection.
3. In the section at the top, set the following values:
- Name: Enter appcollection.
- Priority: Enter 200.
- Action: Select Allow.
4. In the Rules section, under Target FQDNs, set the following values:
- name: Enter microsoftcom.
- Source type: Select IP address.
- Source: The IPv4 of the default subnet.
- Protocol:Port: Enter http, https.
- Target FQDNs: Enter www.microsoft.com.
5. Click Add.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

<h3>Test Connectivity</h3>

1. Navigate to Resource groups, click on your resource group, and click on service-VM in the new browser tab.
2. In the left navigation menu, click Networking.
3. Next to Network Interface:, click the listed network interface.
4. In the left navigation menu, click DNS servers.
5. Under DNS servers, select Custom.
6. In the field that appears, enter 8.8.8.8.
7. In the second field that appears, enter 8.8.4.4.
8. Click Save.



<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

1. In another browser tab, navigate to Firewalls > FW-1 > Public IP configuration.
2. Copy the listed public IP address.
3. Open a remote desktop app, and login with the lab credentials provided.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

- Open Internet Explorer and click OK.
- Navigate to microsoft.com. You will see that you cannot browse to the site.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

- Navigate to www.microsoft.com. You will see that you can now successfully browse to that site.


<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

- In the lower left corner, click on the magnifying glass icon to search for and open PowerShell.
- Test the DNS:
```
nslookup -type=TXT test.dns.google.com. Dns.google.
```
- You should see a message stating, "Thanks for using Google Public DNS."



<br/>
 
<p align="center">
<img src="https://i.imgur.com/jjQxANw.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />
