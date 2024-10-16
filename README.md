# Project-Azure-Firewall-and-Route-Table

**Description :**  
Normally In azure 2 virtual machines are communicate to internet through therir public ip's. There may be a chance to hackers comes through. So we configure firewall on vnet. Then we block the internet of both virtual machines through their public ip and allow the internet through firewall only through route table.

**Procedure :**
Step 1 :**Create vnet**
Go to azure portal->search virtual networks->click create->give subscription,resource group,region->give name of vnet->give cidr range(for ex:10.1.0.0/22)-click save

Step 2 : C**reate subnetA and azure firewall subnet inside above vnet**
Go to azure portal->search virtual networks->click the above created vnet->go to subnets option->click create subnet->give cidr range(for ex:10.1.0.0./24) 
Go to azure portal->search virtual networks->click the above created vnet->go to subnets option->click create gateway subnet->give cidr range(for ex:10.1.0.0./26) 

Step 3: **Create firewall for vnet**
go to azure portal->search virtual networks->click the above created vnet->go to firewall option->click create firewall->select standard firewall->select firewall rules as firewall mangaement->select particular vnet->create new public ip for firewall->click review and create.

Step 4: **create 2virtual machines,1NAT Gtaeway and attach them into subnetA**
Go to azure portal->click create virtual machine1->give subscription,resource group,region,vnet,subnet->click review and create.
Go to azure portal->click create virtual machine2->give subscription,resource group,region,vnet,subnet->click review and create.
Go to azure portal-> search NG->click create NSG->enable rdp3389->click save
Go to azure portal->search virtual networks->Click the vnet which is created in task1->click subnets->select subnetA->attach above NSG into this subnet

Step5: **Create Route table to route the traffic to firewall**
Go to azure portal->search route table->click create->click create route table->click routes->click add ->add thease deatails(destination-internet-0.0.0.0/0, nexthop-virtual appliance(firewall)-firewall private ip)

Step 6 : **attach route table to subnetA**
go to azure portal->search virtual networks->select vnet->go to subnets->slect subnetA->add route table

Now traffic is routed through routetable, not directly to internet(i.e)you can't access internet inside rdp now.

Step 7 : **create rules in firewall**

1.DNAT Rule for rdp purpose 2.Network rule for accessing internet 3.application rule for accessing only specific domain(for example: www.google.com,wwww.youtupe.com)

Go to azure portal->search firewall->click your firewall->click rules under settings option->add below rules
DNAT rule-> protocol(tcp),source type(ip address),source(internet-0.0.0.0/0 or *),destination(firewall-firewall public ip),destination port(3389),translated address(private ip of virtual machine1)
Network rule->protocol(tcp),source(*),destination(*),destination port(*)

After adding network rule, you can access internet inside rdp.

Note: * measns everything
      If you want to give internet for only specifi domain such as google,youtupe you can add application rule. for that you need to delete netwokrk rule and apply.

      Application rule->source type(ip address),source(*),protocolport(http,https:80)

