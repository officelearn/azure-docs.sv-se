---
title: Azure VPN Gateway i Azure Australien
description: Implementera VPN Gateway i Azure Australien för att vara kompatibel med ISM och effektivt skydda de australiska myndigheter
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575432"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure VPN Gateway i Azure Australien

En kritisk tjänst med ett offentligt moln är en säker anslutning av moln resurser och tjänster till befintliga lokala system. Tjänsten som tillhandahåller den här funktionen i Azure är Azure VPN Gateway. Den här artikeln beskriver de viktiga punkter som du bör tänka på när du konfigurerar en VPN-gateway så att den följer ASD- [kontrollerna (information Security manual](https://acsc.gov.au/infosec/ism/)) för Australien (ASD).

En VPN-gateway används för att skicka krypterad trafik mellan ett virtuellt nätverk i Azure och ett annat nätverk. Tre scenarier åtgärdas av VPN-gatewayer:

- Plats-till-plats (S2S)
- Punkt-till-plats (P2S)
- Nätverks-till-nätverk

Den här artikeln fokuserar på S2S VPN-gatewayer. Diagram 1 visar en exempel konfiguration av S2S VPN gateway.

![VPN-gateway med anslutningar för flera platser](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1 – S2S-VPN Gateway i Azure*

## <a name="key-design-considerations"></a>Viktiga design överväganden

Det finns tre nätverks alternativ för att ansluta Azure till myndigheter i Australien:

- ICON
- Azure ExpressRoute
- Offentligt Internet

Användar handboken för australisk cyberhot Security Centre [för Azure](https://servicetrust.microsoft.com/viewpage/Australia) rekommenderar att VPN gateway (eller en likvärdig skyddad certifierad tredjepartstjänst) används tillsammans med de tre nätverks alternativen. Den här rekommendationen är att se till att anslutningarna överensstämmer med ISM-kontrollerna för kryptering och integritet.

### <a name="encryption-and-integrity"></a>Kryptering och integritet

Som standard förhandlar VPN krypterings-och integritets algoritmerna och parametrarna under anslutnings etableringen som en del av IKE-handskakningarna. Under IKE-handskakningen beror konfigurationen och prioritetsordningen på om VPN-gatewayen är initieraren eller svarande. Den här beteckningen styrs via VPN-enheten. Den slutliga konfigurationen av anslutningen styrs av konfigurationen av VPN-enheten. Mer information om verifierade VPN-enheter och deras konfiguration finns i [om VPN-tjänster](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

VPN-gatewayer kan styra kryptering och integritet genom att konfigurera en anpassad IPsec/IKE-princip på anslutningen.

### <a name="resource-operations"></a>Resurs åtgärder

VPN-gatewayer skapar en anslutning mellan Azure-och icke-Azure-miljöer via det offentliga Internet. ISM har kontroller som relaterar till explicit auktorisering av anslutningar. Som standard är det möjligt att använda VPN-gatewayer för att skapa oauktoriserade tunnlar i säkra miljöer. Det är viktigt att organisationer använder Azure rollbaserad åtkomst kontroll (RBAC) för att kontrol lera vem som kan skapa och ändra VPN-gatewayer och deras anslutningar. Azure har ingen inbyggd roll för att hantera VPN-gatewayer, så en anpassad roll krävs.

Åtkomst till roller för ägare, deltagare och nätverks deltagare är nära styrd. Vi rekommenderar också att du använder Azure Active Directory Privileged Identity Management för mer detaljerad åtkomst kontroll.

### <a name="high-availability"></a>Hög tillgänglighet

Azure VPN-gatewayer kan ha flera anslutningar och har stöd för flera lokala VPN-enheter till samma lokala miljö. Se diagram 1.

Virtuella nätverk i Azure kan ha flera VPN-gatewayer som kan distribueras i oberoende, aktiva eller aktiva-aktiva konfigurationer.

Vi rekommenderar att du distribuerar alla VPN-gateways i en [konfiguration med hög](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)tillgänglighet. Ett exempel är två lokala VPN-enheter som är anslutna till två VPN-gatewayer antingen i aktivt läge eller aktivt-aktivt läge. Se diagram 2.

![Redundanta VPN gateway-anslutningar](media/dual-redundancy.png)

*Diagram 2 – aktiva-aktiva VPN-gatewayer och två VPN-enheter*

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Tvingad tunnel trafik dirigerar om, eller tvingar, all Internet-baserad trafik tillbaka till den lokala miljön via VPN-gatewayen för granskning och granskning. Utan Tvingad tunnel trafik passerar Internet-baserad trafik från virtuella datorer i Azure Azures nätverks infrastruktur direkt till det offentliga Internet, utan alternativet att inspektera eller granska trafiken. Tvingad tunnel trafik är kritisk när en organisation krävs för att använda en säker Internet-Gateway (SIG) för en miljö.

## <a name="detailed-configuration"></a>Detaljerad konfiguration

### <a name="service-attributes"></a>Tjänstens attribut

VPN-gatewayer för S2S-anslutningar som kon figurer ATS för den australiska regeringen måste ha följande attribut:

|Attribut | Måste|
|--- | --- |
|gatewayType | KONFIGURERA|
|

De inställningar för attribut som krävs för att följa ISM-kontrollerna för skyddade är:

|Attribut | Måste|
|--- |---|
|vpnType |Routningsbaserad|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Azure VPN-gatewayer har stöd för en mängd kryptografiska algoritmer från standarderna för IPsec-och IKE-protokollet. Standard principen anger maximal interoperabilitet med en mängd olika VPN-enheter från tredje part. Därför är det möjligt att under IKE-hand skakningen en inkompatibel konfiguration kan förhandlas. Vi rekommenderar starkt att du tillämpar [anpassade IPSec/IKE-principinställningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) på vpnClientConfiguration i VPN-gatewayer för att se till att anslutningarna uppfyller ISM-kontrollerna för lokala miljö anslutningar till Azure. Nyckelattribut visas i följande tabell.

|Attribut|Måste|Måste|
|---|---|---|
|saLifeTimeSeconds|< 14 400 sekunder|> 300 sekunder|
|saDataSizeKilobytes| |> 1 024 KB|
|ipsecEncryption| |AES256 – GCMAES256|
|ipsecIntegrity| |SHA256 – GCMAES256|
|ikeEncryption| |AES256 – GCMAES256|
|ikeIntegrity| |SHA256 – GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

För dhGroup och pfsGroup i föregående tabell är ECP256 och ECP384 önskade även om andra inställningar kan användas.

### <a name="related-services"></a>Relaterade tjänster

När du utformar och konfigurerar en Azure VPN-gateway måste även ett antal relaterade tjänster finnas och konfigureras.

|Tjänsten | Åtgärd krävs|
|--- | ---|
|Virtuellt nätverk | VPN-gatewayer är anslutna till ett virtuellt nätverk. Skapa ett virtuellt nätverk innan du skapar en ny VPN-gateway.|
|Offentlig IP-adress | VPN-gatewayer för S2S behöver en offentlig IP-adress för att upprätta anslutningar mellan den lokala VPN-enheten och VPN-gatewayen. Skapa en offentlig IP-adress innan du skapar en S2S VPN gateway.|
|Subnet | Skapa ett undernät för det virtuella nätverket för VPN-gatewayen.|
|

## <a name="implementation-steps-using-powershell"></a>Implementerings steg med PowerShell

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

1. Skapa en anpassad roll. Ett exempel är virtualNetworkGateway Contributor. Skapa en roll som ska tilldelas till användare som ska kunna skapa och ändra VPN-gatewayer. Den anpassade rollen ska tillåta följande åtgärder:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft. Network/routeTables/*  

2. Lägg till den anpassade rollen till användare som tillåts att skapa och hantera VPN-gatewayer och anslutningar till lokala miljöer.

### <a name="create-a-vpn-gateway"></a>Skapa en VPN-gateway

De här stegen förutsätter att du redan har skapat ett virtuellt nätverk.

1. Skapa en ny offentlig IP-adress.
2. Skapa ett undernät för VPN gateway.
3. Skapa en VPN gateway-IP-konfigurationsfil.
4. Skapa en VPN-gateway.
5. Skapa en lokal nätverksgateway för den lokala VPN-enheten.
6. Skapa en IPsec-princip. I det här steget förutsätter vi att du använder anpassade IPsec/IKE-principer.
7. Skapa en anslutning mellan VPN-gatewayen och en lokal nätverksgateway med hjälp av IPsec-principen.

### <a name="enforce-tunneling"></a>Tvingad tunnel trafik

Om Tvingad tunnel trafik krävs måste du innan du skapar VPN-gatewayen:

1. Skapa en routningstabell och flödes regler.
2. Koppla en routningstabell till lämpliga undernät.

När du har skapat VPN-gatewayen:

- Ange GatewayDefaultSite till den lokala miljön på VPN-gatewayen.

### <a name="example-powershell-script"></a>Exempel på PowerShell-skript

Ett exempel på PowerShell-skript som används för att skapa en anpassad IPsec/IKE-princip följer ISM-kontroller för den australiska skyddade säkerhets klassificeringen.

Det förutsätter att det virtuella nätverket, VPN-gatewayen och lokala gatewayer finns.

#### <a name="create-an-ipsecike-policy"></a>Skapa en IPsec/IKE-princip

Följande exempel skript skapar en IPsec/IKE-princip med följande algoritmer och parametrar:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, SA-livstid 14 400 sekunder och 102 400 000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Skapa en S2S VPN-anslutning med den anpassade IPsec/IKE-principen

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs den specifika konfigurationen av VPN Gateway för att uppfylla de krav som anges i säkerhets hand boken för informations säkerhet för att skydda australiska myndigheters skyddade data under överföring. Anvisningar för hur du konfigurerar din VPN-gateway finns i:

- [Översikt över Azure Virtual Network Gateway](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Vad är VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Skapa ett virtuellt nätverk med en plats-till-plats-VPN-anslutning med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Skapa och hantera VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)