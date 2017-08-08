# Översikt
## [Om Azure-nätverk](networking-overview.md)
## Arkitektur
### [Virtuella datacenter](networking-virtual-datacenter.md)
### [Asymmetrisk routning med flera nätverksvägar](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Skydda nätverkstrafik](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologi av typen hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Metodtips för nätverkssäkerhet](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Högtillgängliga virtuella nätverksinstallationer](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Kombinera metoder för belastningsutjämning](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Planera och designa
### [Virtuella nätverk](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Trafikfiltrering](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslutning på flera platser – VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslutning på flera platser – dedikerad privat](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Koncept
### [Virtuella nätverk](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Utjämning av nätverksbelastning](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Belastningsutjämning för program](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS-baserad trafikdistribution](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslut lokalt – VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslut lokalt – särskilda](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Kom igång
## [Skapa ditt första virtuella nätverk](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Gör så här för att
## Internetanslutning
### [Offentliga servrar för belastningsutjämning av nätverk](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Offentliga servrar för belastningsutjämning av program](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Skydda webbprogram](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuera trafik över platser](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Interna anslutningar
### [Privata servrar för belastningsutjämning av nätverk](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Privata servrar för belastningsutjämning av program](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslut virtuella nätverk (samma plats)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslut virtuella nätverk (olika platser)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Anslutning på flera platser
### [Skapa en S2S VPN-anslutning (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Skapa en P2S VPN-anslutning (SSTP med certifikat)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Skapa en dedikerad privat anslutning (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Hantering
### [Visa nätverkstopologi](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Hantera paketinsamling](../network-watcher/network-watcher-packet-capture-manage-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Fastställ nästa routningshopp](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Kontrollera ip-flöden för en virtuell dator](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Exempelskript
### [Azure CLI](cli-samples.md)
### [PowerShell](powershell-samples.md)

## Självstudier
### [Belastningsutjämna virtuella datorer](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Anslut en dator till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Referens
## [Azure CLI](https://docs.microsoft.com/cli/azure/network)
## [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Resurser
## [Skapa mallar](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=networking)
## [Community-mallar](https://azure.microsoft.com/resources/templates/)
## [Nätverksblogg](http://azure.microsoft.com/blog/topics/networking)
## [Prissättning](https://azure.microsoft.com/pricing)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Regional tillgänglighet](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Videoklipp](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

