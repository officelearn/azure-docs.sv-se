# Översikt
## [Virtuella nätverk](virtual-networks-overview.md)
## [Användardefinierade vägar och IP-vidarebefordran](virtual-networks-udr-overview.md)
## [Virtuell nätverkspeering](virtual-network-peering-overview.md)
## [Verksamhetskontinuitet](virtual-network-disaster-recovery-guidance.md)
## [Vanliga frågor och svar](virtual-networks-faq.md)
## IP-adress
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [Klassisk](virtual-network-ip-addresses-overview-classic.md)
## Virtuella datorer
### [Nätverksgränssnitt](virtual-network-network-interface-overview.md)
### [Namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md)

# Kom igång
## [Skapa ett virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md)
## [Distribuera en virtuell dator till ett virtuellt nätverk](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

# Gör så här för att
## Planera och designa
### [Virtuella nätverk](virtual-network-vnet-plan-design-arm.md)
### [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md)

## Distribuera
### Virtuella nätverk
#### [Portal](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Mall](virtual-networks-create-vnet-arm-template-click.md)
#### [Portal (klassisk)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (klassisk)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [CLI (klassisk)](virtual-networks-create-vnet-classic-cli.md)

### Nätverkssäkerhetsgrupper
#### [Portal](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Mall](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (klassisk)](virtual-networks-create-nsg-classic-ps.md)
#### [CLI (klassisk)](virtual-networks-create-nsg-classic-cli.md)

### Användardefinierade vägar
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [Mall](virtual-network-create-udr-arm-template.md)
#### [PowerShell (klassisk)](virtual-network-create-udr-classic-ps.md)
#### [CLI (klassisk)](virtual-network-create-udr-classic-cli.md)

### Virtuell nätverkspeering
#### [Portal](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Mall](virtual-networks-create-vnetpeering-arm-template-click.md)

### Virtuella datorer

#### Statiska offentliga IP-adresser
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Mall](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

#### Statiska privata IP-adresser
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [Portal (klassisk)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (klassisk)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI (klassisk)](virtual-networks-static-private-ip-classic-cli.md)

#### Flera nätverksgränssnitt
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [Mall](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (klassisk)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI (klassisk)](virtual-network-deploy-multinic-classic-cli.md)

#### Flera IP-adresser
##### [Azure-portal](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Mall](virtual-network-multiple-ip-addresses-template.md)

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
##### [Enkel DMZ med NSG:er](virtual-networks-dmz-nsg-asm.md)
##### [DMZ med brandvägg och NSG:er](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ med brandvägg, UDR och NSG:er](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Exempelprogram](virtual-networks-sample-app.md)

## Konfigurera
### Snabbare nätverk
#### [Azure-portal](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### Listor för åtkomstkontroll
#### [Klassisk portal](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)

## Hantera
### Nätverkssäkerhetsgrupper
#### [Portal](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [Loggar](virtual-network-nsg-manage-log.md)
#### Felsöka
##### [Portal](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Felsökningsvägar
#### [Portal](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### Virtuella datorer
#### [Visa och ändra värdnamn](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Flytta en virtuell dator till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md)

# Referens
## [PowerShell (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [PowerShell (klassisk)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [Azure CLI](/cli/azure/)
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
## [Nätverksblogg](http://azure.microsoft.com/blog/topics/networking)
## [Nätverksforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Prissättning](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)


<!--HONumber=Dec16_HO2-->


