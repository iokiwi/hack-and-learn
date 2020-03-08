# Setup Nat Networking between two Virtual Machines

>NAT = Network Address Translation

Setting up a NAT network between two virtual machines allows us to create a
semi private network where our virtual machines can talk to one another - but
nobody else on the network can talk to them.

## 1. Creating a NAT Network

Open File > Preferences > Network

![2](img/setup-nat-network/2.png)

Click on the green `+` on the right to add a new nat network.

![3](img/setup-nat-network/3.png)

View the configuration of the new `NatNetwork` by selecting it and clicking the Settings cog on the right.

The default settings populated by Virtual Box automatically should look like this.

![1](img/setup-nat-network/1.png)

Click **Ok** to save the new network

![3](img/setup-nat-network/3.png)

## 2. Configuring Virtual Machines to use your NAT Network

Select a VM and open **Settings** > **Network**

![5](img/import-vuln-vm/5.png)

Set `Attached to:` to NAT Network and select your NAT network from the list.

![6](img/import-vuln-vm/6.png)

Click **OK** to save the configuration and close the settings.

>**Note** Repeat for both your Vulnerable VM and you Kali Workstation VM