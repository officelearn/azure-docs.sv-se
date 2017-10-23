# Översikt
## [Virtuella nätverk](virtual-networks-overview.md)
## [Användardefinierade vägar och IP-vidarebefordran](virtual-networks-udr-overview.md)
## [Virtuell nätverkspeering](virtual-network-peering-overview.md)
## [Slutpunkter för virtuellt nätverk](virtual-network-service-endpoints-overview.md)
## [Virtuellt nätverk för Azure-tjänster](virtual-network-for-azure-services.md)
## [Säkerhet](security-overview.md)
## [Verksamhetskontinuitet](virtual-network-disaster-recovery-guidance.md)
## [Vanliga frågor och svar](virtual-networks-faq.md)
## [IP-adress](virtual-network-ip-addresses-overview-arm.md)
## [DDoS Protection](ddos-protection-overview.md)
## Klassisk
### [IP-adress](virtual-network-ip-addresses-overview-classic.md)
### [Listor för åtkomstkontroll](virtual-networks-acl.md)

# Kom igång
## [Skapa ditt första virtuella nätverk](virtual-network-get-started-vnet-subnet.md)

# Gör så här för att
## Planera och designa
### [Virtuella nätverk](virtual-network-vnet-plan-design-arm.md)
### [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md)

## Distribuera
### [Virtuella nätverk](virtual-networks-create-vnet-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md)
#### [Azure CLI 1.0](virtual-networks-create-vnet-cli-nodejs.md)
#### [Mall](virtual-networks-create-vnet-arm-template-click.md)

### Nätverkssäkerhetsgrupper
#### [Azure Portal](virtual-networks-create-nsg-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [Azure CLI 2.0](virtual-networks-create-nsg-arm-cli.md)
#### [Azure CLI 1.0](virtual-networks-create-nsg-cli-nodejs.md)
#### [Mall](virtual-networks-create-nsg-arm-template.md)
#### [Säkerhetsgrupper för program](create-network-security-group-preview.md)
#### Klassisk
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### Användardefinierade vägar
#### [Azure Portal](create-user-defined-route-portal.md)
#### [Azure PowerShell](virtual-network-create-udr-arm-ps.md)
#### [Azure CLI 2.0](virtual-network-create-udr-arm-cli.md)
#### [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md)
#### [Mall](virtual-network-create-udr-arm-template.md)
#### Klassisk
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Azure CLI](virtual-network-create-udr-classic-cli.md)

### Virtuell nätverkspeering
#### [Samma distributionsmodell – samma prenumeration](virtual-network-create-peering.md)
#### [Samma distributionsmodell – olika prenumerationer](create-peering-different-subscriptions.md)
#### [Olika distributionsmodeller – samma prenumeration](create-peering-different-deployment-models.md)
#### [Olika distributionsmodeller – olika prenumerationer](create-peering-different-deployment-models-subscriptions.md)

### [Slutpunkter för virtuellt nätverk](virtual-network-service-endpoints-configure.md)

### Offentlig IP-adress – tillgänglighetszon
#### [Azure Portal](create-public-ip-availability-zone-portal.md)
#### [Azure CLI](create-public-ip-availability-zone-cli.md)
#### [PowerShell](create-public-ip-availability-zone-powershell.md)

### Virtuella datorer
#### Skapa en virtuell dator med en statisk offentlig IP-adress
##### [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Azure CLI 2.0](virtual-network-deploy-static-pip-arm-cli.md)
##### [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
##### [Mall](virtual-network-deploy-static-pip-arm-template.md)
##### Klassisk
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Skapa en virtuell dator med en statisk privat IP-adress
##### [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
##### Klassisk
###### [Azure Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Azure CLI](virtual-networks-static-private-ip-classic-cli.md)

#### Skapa en virtuell dator med flera nätverksgränssnitt
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI 2.0](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI 1.0](../virtual-machines/linux/multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Mall](virtual-network-deploy-multinic-arm-template.md)

##### Klassisk
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Azure CLI](virtual-network-deploy-multinic-classic-cli.md)

#### Skapa en virtuell dator med flera IP-adresser
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md)
##### [Azure CLI 1.0](virtual-network-multiple-ip-addresses-cli-nodejs.md)
##### [Mall](virtual-network-multiple-ip-addresses-template.md)

#### [Skapa en virtuell dator med accelererat nätverk](virtual-network-create-vm-accelerated-networking.md)

### Scenarier för anslutning
#### [Virtuellt nätverk (VNet) till VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) till VNet (klassisk)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet till lokalt nätverk (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet till lokalt nätverk (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Hybridnätverksarkitektur med hög tillgänglighet](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Säkerhetsscenarier
#### [Säkra nätverk med virtuella installationer](virtual-network-scenario-udr-gw-nva.md)
#### [DMZ mellan Azure och Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Säkerhet i molntjänst och nätverk](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Skapa en DMZ med NSG:er](virtual-networks-dmz-nsg.md)
##### [Skapa en DMZ med NSG:er (klassisk)](virtual-networks-dmz-nsg-asm.md)
##### [Skapa en DMZ med brandvägg och NSG:er (klassisk)](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ med brandvägg, UDR och NSG:er (klassisk)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Exempelprogram](virtual-networks-sample-app.md)

### Klassisk
#### [Virtuellt nätverk](create-virtual-network-classic.md)
##### [Azure Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Azure CLI](virtual-networks-create-vnet-classic-cli.md)
#### [Ange DNS-inställningar i en virtuell nätverkskonfigurationsfil](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [Ange DNS-inställningar i en tjänstkonfigurationsfil](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Konfigurera
### Virtuella datorer
#### [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md)
#### [Namnmatchning för virtuella datorer och molntjänster](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Använd dynamisk DNS för att registrera värdnamn i DNS-servern](virtual-networks-name-resolution-ddns.md)
#### [Optimera nätverkets dataflöde](virtual-network-optimize-network-bandwidth.md)
#### [Visa och ändra värdnamn](virtual-networks-viewing-and-modifying-hostnames.md)
#### Klassisk
##### Statiska IP-adresser
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [CLI](virtual-networks-static-private-ip-cli-nodejs.md)
##### [Offentlig IP-adress på instansnivå](virtual-networks-instance-level-public-ip.md)

### Klassisk
#### Listor för åtkomstkontroll
##### [Azure Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## Hantera
### [Virtuella nätverk](virtual-network-manage-network.md)
#### [Undernät](virtual-network-manage-subnet.md)
#### [Peering-sessioner](virtual-network-manage-peering.md)
#### Klassisk
##### [Nätverkskonfigurationsfil](virtual-networks-using-network-configuration-file.md)
##### [Migrera från en tillhörighetsgrupp till en region](virtual-networks-migrate-to-regional-vnet.md)
### Nätverkssäkerhetsgrupper
#### [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Azure CLI 2.0](virtual-network-manage-nsg-arm-cli.md)
#### [Azure CLI 1.0](virtual-network-manage-nsg-cli-nodejs.md)

#### [Loggar](virtual-network-nsg-manage-log.md)
### Nätverksgränssnitt (nätverkskort)
#### [Skapa, ändra eller ta bort nätverkskort](virtual-network-network-interface.md)
#### [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md)
### Virtuella datorer
#### [Flytta en virtuell dator till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md)
### [Offentliga IP-adresser](virtual-network-public-ip-address.md)
### DDoS Protection
#### [Azure Portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Felsöka
### Nätverkssäkerhetsgrupper
#### [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Vägar
#### [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Testning av dataflöde](virtual-network-bandwidth-testing.md)
### [Det går inte att ta bort virtuella nätverk](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Problem med anslutning mellan virtuella datorer](virtual-network-troubleshoot-connectivity-problem-between-vms.md)

# Referens
## [Kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (klassisk)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (klassisk)](https://msdn.microsoft.com/library/jj157182.aspx)


# Relaterat
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Belastningsutjämnare](/azure/load-balancer/)
## [VPN-gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=networking)
## [Nätverksblogg](http://azure.microsoft.com/blog/topics/networking)
## [Nätverksforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Prissättning](https://azure.microsoft.com/pricing/details/virtual-network)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Provider för nätverksresurser](resource-groups-networking.md)
