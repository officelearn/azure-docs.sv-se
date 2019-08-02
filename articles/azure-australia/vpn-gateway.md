---
title: Azure VPN Gateway i Azure Australien
description: Implementera VPN Gateway i Azure Australien för att vara kompatibel med ISM och effektivt skydda de australiska myndigheter
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571853"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure VPN Gateway i Azure Australien

En kritisk tjänst med ett offentligt moln är en säker anslutning av moln resurser och tjänster till befintliga lokala system.  Tjänsten som tillhandahåller den här funktionen i Azure är Azure-VPN Gateway (VPN Gateway). Den här artikeln beskriver viktiga överväganden när du konfigurerar VPN Gateway att följa ASD- [information säkerhets manuella kontroller](https://acsc.gov.au/infosec/ism/) (ASD) för australisk signaler.

En VPN Gateway används för att skicka krypterad trafik mellan ett virtuellt nätverk i Azure och ett annat nätverk.  Det finns tre scenarier som adresseras av VPN-gatewayer:

- **Plats-till-plats** S2S
- **Punkt-till-plats** P2s
- **VNet-till-VNet**

Den här artikeln fokuserar på S2S VPN-gatewayer. Diagram 1 visar en exempel konfiguration för plats-till-plats-VPN-gateway.

![VPN Gateway med anslutningar för flera platser](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1 – Azure plats-till-plats-VPN Gateway*

## <a name="key-design-considerations"></a>Viktiga design överväganden

Det finns tre nätverks alternativ för att ansluta Azure till myndigheter i Australien:

- **ICON**
- **ExpressRoute**
- **Offentligt Internet**

Användar handboken för australisk cyberhot Security Centre [för Azure](https://servicetrust.microsoft.com/viewpage/Australia) rekommenderar att VPN gateway (eller motsvarande skyddade Certified-tjänsten från tredje part) används tillsammans med de tre nätverks alternativen för att säkerställa att anslutningarna överensstämmer med ISM-kontroller för kryptering och integritet.

### <a name="encryption-and-integrity"></a>Kryptering och integritet

Som standard förhandlar VPN krypterings-och integritets algoritmerna och parametrarna under anslutnings etableringen som en del av IKE-handskakningarna.  Under IKE-handskakningen beror konfigurationen och prioritetsordningen på om VPN Gateway är initieraren eller svarande (Obs: Detta styrs via VPN-enheten).  Den slutliga konfigurationen av anslutningen styrs av konfigurationen av VPN-enheten.  Mer information om verifierade VPN-enheter och deras konfiguration finns här: [Om VPN-enheter](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

VPN-gatewayer kan styra kryptering och integritet genom att konfigurera en anpassad IPsec/IKE-princip på anslutningen.

### <a name="resource-operations"></a>Resurs åtgärder

VPN-gatewayer skapar en anslutning mellan Azure-och icke-Azure-miljöer via det offentliga Internet.  ISM har kontroller som relaterar till explicit auktorisering av anslutningar.  Som standard är det möjligt att använda VPN-gatewayer för att skapa oauktoriserade tunnlar i säkra miljöer.  Därför är det viktigt att organisationer använder Azure Role based Access Control (RBAC) för att kontrol lera vem som kan skapa och ändra VPN-gatewayer och deras anslutningar.  Azure har ingen "inbyggd" roll för att hantera VPN-gatewayer, vilket kräver att en anpassad roll krävs.

Åtkomst till rollerna "ägare", "deltagare" och "nätverks deltagare" är tätt kontrollerade.  Vi rekommenderar också att Azure AD Privileged Identity Management används för mer detaljerad åtkomst kontroll.

### <a name="high-availability"></a>Hög tillgänglighet

Azure VPN-gatewayer kan ha flera anslutningar (se diagram 1) och har stöd för flera lokala VPN-enheter till samma lokala miljö.  

Virtuella nätverk i Azure kan ha flera VPN-gatewayer som kan distribueras i oberoende, aktiva eller aktiva-aktiva konfigurationer.

Vi rekommenderar att alla VPN-gatewayer distribueras i en [konfiguration med hög tillgänglighet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable): till exempel två lokala VPN-enheter som är anslutna till två VPN-gatewayer i antingen aktivt-passivt eller aktivt-aktivt läge (se diagram 2).

![VPN Gateway redundanta anslutningar](media/dual-redundancy.png)

*Diagram 2 – aktiva-aktiva VPN-gatewayer och två VPN-enheter*

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Tvingad tunnel trafik omdirigeras eller "tvingar" all Internet-baserad trafik tillbaka till den lokala miljön via VPN Gateway för granskning och granskning. Utan Tvingad tunnel trafik passerar Internet-baserad trafik från virtuella datorer i Azure Azures nätverks infrastruktur direkt till det offentliga Internet, utan alternativet att inspektera eller granska trafiken.  Detta är kritiskt om organisationen måste använda en säker Internet-Gateway (SIG) för en miljö.

## <a name="detailed-configuration"></a>Detaljerad konfiguration

### <a name="service-attributes"></a>Tjänstens attribut

VPN-gatewayer för S2S-anslutningar som kon figurer ATS för den australiska regeringen måste ha följande attribut:

|Attribut | MÅSTE|
|--- | --- |
|gatewayType | KONFIGURERA|
|

De inställningar för attribut som krävs för att följa ISM-kontrollerna för skyddade är:

|Attribut | MÅSTE|
|--- |---|
|vpnType |Routningsbaserad|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Azure VPN-gatewayer har stöd för en mängd kryptografiska algoritmer från standarderna för IPsec-och IKE-protokollet.  Standard princip uppsättningarna maximerar samverkan med en mängd olika VPN-enheter från tredje part.  Därför är det möjligt att en icke-kompatibel konfiguration förhandlas under IKE-handskakningen.  Vi rekommenderar därför starkt att [anpassade IPSec/IKE-principinställningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) tillämpas på vpnClientConfiguration i VPN-gatewayer för att säkerställa att anslutningarna uppfyller ISM-kontrollerna för lokala miljö anslutningar till Azure.  Nyckelattribut är:

|Attribut|MÅSTE|MÅSTE|
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

*För dhGroup och pfsGroup i tabellen ovan är ECP256 och ECP384 önskade även om andra inställningar kan användas*

### <a name="related-services"></a>Relaterade tjänster

När du utformar och konfigurerar en Azure-VPN Gateway det finns ett antal relaterade tjänster som även måste finnas och som ska konfigureras:

|Tjänsten | Åtgärd krävs|
|--- | ---|
|Virtuellt nätverk | VPN-gatewayer är anslutna till ett virtuellt nätverk.  Du måste skapa ett virtuellt nätverk innan du skapar en ny VPN Gateway.|
|Offentlig IP-adress | VPN-gatewayer för S2S behöver en offentlig IP-adress för att upprätta anslutningar mellan den lokala VPN-enheten och VPN Gateway.  En offentlig IP-adress måste skapas innan en S2S-VPN Gateway skapas.|
|Subnet | Du måste skapa ett undernät för det virtuella nätverket för VPN Gateway.|
|

## <a name="implementation-steps-using-powershell"></a>Implementerings steg med PowerShell

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

1. Skapa en anpassad roll (till exempel virtualNetworkGateway Contributor).  Skapa en roll som ska tilldelas till användare som ska kunna skapa och ändra VPN-gatewayer. Den anpassade rollen ska tillåta följande åtgärder:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft. Network/routeTables/*  

2. Lägg till anpassad roll till användare som har behörighet att skapa och hantera VPN-gatewayer och anslutningar till lokala miljöer.

### <a name="create-vpn-gateway"></a>Skapa VPN Gateway

*De här stegen förutsätter att ett virtuellt nätverk redan har skapats*

1. Skapa en ny offentlig IP-adress
2. Skapa ett VPN Gateway undernät
3. Skapa en VPN Gateway IP-konfiguration
4. Skapa en VPN Gateway
5. Skapa en lokal nätverksgateway för den lokala VPN-enheten
6. Skapa en IPsec-princip (förutsatt att använda anpassade IPsec/IKE-principer)
7. Skapa anslutning mellan VPN Gateway och lokal nätverksgateway med IPsec-princip

### <a name="enforce-tunneling"></a>Tvingad tunnel trafik

Om Tvingad tunnel trafik krävs, innan VPN Gateway skapas:

1. Skapa routningstabellen och flödes regel (er)
2. Associera routningstabellen med lämpliga undernät

När du har skapat VPN Gateway:

1. Ange GatewayDefaultSite till den lokala miljön på VPN Gateway

### <a name="example-powershell-script"></a>Exempel på PowerShell-skript

Ett exempel på ett PowerShell-skript för att skapa en anpassad IPSEC/IKE-princip som följer ISM-kontroller för den australiska skyddade säkerhets klassificeringen.

Det förutsätter att det virtuella nätverket, VPN Gateway och lokala gatewayer finns.

#### <a name="create-an-ipsecike-policy"></a>Skapa en IPsec/IKE-princip

Följande exempel skript skapar en IPsec/IKE-princip med följande algoritmer och parametrar:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, SA livstid 14 400 sekunder & 102400000 KB

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

I den här artikeln beskrivs den specifika konfigurationen av VPN Gateway för att uppfylla de krav som anges i säkerhets hand boken för information (ISM) för att skydda australisk myndighets skyddade data under överföring. Detaljerade anvisningar för hur du konfigurerar VPN Gateway:

- [Översikt över Azure Virtual Network Gateway](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Vad är VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Skapa ett VNet med en plats-till-plats-VPN-anslutning med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Skapa och hantera VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)