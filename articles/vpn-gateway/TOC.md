# [Dokumentation om VPN-gateway](index.md)

# Översikt
## [Om VPN-gateway](vpn-gateway-about-vpngateways.md)
## [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md)
## [Prenumerations- och tjänstbegränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Kom igång
## Skapa en ruttbaserad VPN-gateway
### [Azure Portal](create-routebased-vpn-gateway-portal.md)
### [Azure PowerShell](create-routebased-vpn-gateway-powershell.md)
### [Azure CLI](create-routebased-vpn-gateway-cli.md)

# Begrepp
## [Planering och design för VPN Gateway](vpn-gateway-plan-design.md)
## [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md)
## [Om VPN-enheter](vpn-gateway-about-vpn-devices.md)
## [Om kryptografikrav](vpn-gateway-about-compliance-crypto.md)
## [Om BGP och VPN Gateway](vpn-gateway-bgp-overview.md)
## [Om anslutningar med hög tillgänglighet](vpn-gateway-highlyavailable.md)
## [Om punkt-till-plats-anslutningar](point-to-site-about.md)

# Gör så här för att
## Konfigurera plats-till-plats-anslutningar
### [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Ladda ned konfigurationsskript för VPN-enheten](vpn-gateway-download-vpndevicescript.md)

## Konfigurera punkt-till-plats-anslutningar – ursprunglig Azure-certifikatautentisering
### Konfigurera ett virtuellt privat P2S-nätverk
#### [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Skapa självsignerade certifikat
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Skapa och installera VPN-klientkonfigurationsfiler](point-to-site-vpn-client-configuration-azure-cert.md)
### [Installera klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Konfigurera punkt-till-plats-anslutningar – RADIUS-autentisering
### Konfigurera ett virtuellt privat P2S-nätverk
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Skapa och installera VPN-klientkonfigurationsfiler](point-to-site-vpn-client-configuration-radius.md)
### [Integrera P2S VPN RADIUS-autentisering med NPS-server](vpn-gateway-radiuis-mfa-nsp.md)

## Konfigurera anslutningar mellan virtuella nätverk
### [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Konfigurera en VNet-till-VNet-anslutning mellan distributionsmodeller
### [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Konfigurera anslutningar för samexistens mellan plats-till-plats och ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Konfigurera flera plats-till-plats-anslutningar
### [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (klassisk)](vpn-gateway-multi-site.md)
## Ansluta flera principbaserade VPN-enheter
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Konfigurera IPsec-/IKE-principer för anslutningar
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Konfigurera aktiv-aktiv-anslutningar med hög tillgänglighet
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Konfigurera BGP för en VPN-gateway
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Azure CLI](bgp-how-to-cli.md)
## Konfigurera tvingad tunneltrafik
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (klassisk)](vpn-gateway-about-forced-tunneling.md)
## Ändra inställningar för lokal nätverksgateway
### [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [Verifiera en anslutning till VPN-gateway](vpn-gateway-verify-connection-resource-manager.md)
## [Återställ en VPN-gateway](vpn-gateway-resetgw-classic.md)
## Ta bort en VPN-gateway
### [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Konfigurera en VPN-gateway (klassisk)](vpn-gateway-configure-vpn-gateway-mp.md)
## [Gateway-SKU:er (äldre)](vpn-gateway-about-skus-legacy.md)
## Konfigurera VPN-enheter från tredje part
### [Översikt och Azure-konfiguration](vpn-gateway-3rdparty-device-config-overview.md)
### [Exempel: Cisco ASA-enhet (IKEv2/no BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migrering från klassisk till Resource Manager](vpn-gateway-classic-resource-manager-migration.md)
## [Felsöka](vpn-gateway-troubleshoot.md)
### [Community-föreslaget VPN eller brandväggsinställningar för enhet](vpn-gateway-third-party-settings.md)
### [Konfigurera och verifiera VNet- eller VPN-anslutningar](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)
### [Validera VPN-dataflöde till VNet](vpn-gateway-validate-throughput-to-vnet.md)
### Punkt-till-plats-anslutningsproblem
#### [Problem med punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Problem med punkt-till-plats-anslutning – VPN-klient för Mac OS X-klient](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### Plats-till-plats-anslutningsproblem
#### [Problem med plats-till-plats-anslutning](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Plats-till-plats-anslutningen kopplas från periodvis](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)


# Referens
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (klassisk)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (klassisk)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# Relaterat
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Belastningsutjämnare](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=networking)
## [Blogg](https://azure.microsoft.com/blog/topics/networking)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
