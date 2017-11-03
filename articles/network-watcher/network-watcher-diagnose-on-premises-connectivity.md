---
title: "Diagnostisera lokal anslutning via VPN-gateway med Azure Nätverksbevakaren | Microsoft Docs"
description: "Den här artikeln beskriver hur du diagnostisera lokal anslutning via VPN-gateway med Azure Nätverksbevakaren resurs felsökning."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 94658dfcf93e821e24cabb1f010f8dce0c014700
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostisera lokal anslutning via VPN-gatewayer

Azure VPN-Gateway kan du skapa en hybridlösning som behovet av en säker anslutning mellan ditt lokala nätverk och dina virtuella Azure-nätverket. Dina behov är unika, så är valet av lokala VPN-enhet. Azure stöder för närvarande [flera VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) som ständigt verifieras tillsammans med enhetsleverantörer. Granska de specifika inställningarna innan du konfigurerar din lokala VPN-enhet. På liknande sätt Azure VPN-Gateway har konfigurerats med en uppsättning [IPSec-parametrar som stöds](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) som används för att upprätta anslutningar. Det finns för närvarande inget sätt för att ange eller välj en specifik kombination av IPSec-parametrar från Azure VPN-Gateway. Inställningar för lokala VPN-enheter måste vara i enlighet med IPsec-parametrar som föreskrivs av Azure VPN-Gateway för att upprätta en anslutning mellan Azure och lokalt. Om inställningarna är korrekta finns där det förlust av anslutningen och fram till nu felsökning av problem med dessa var inte trivial och vanligtvis tog timmar att identifiera och åtgärda problemet.

Felsöka med Azure Nätverksbevakaren funktionen, kan du diagnostisera problem med din Gateway och anslutningar och inom minuter har tillräckligt med information för att fatta ett välgrundat beslut för att åtgärda problemet.

## <a name="scenario"></a>Scenario

Du vill konfigurera en plats-till-plats-anslutning mellan Azure och lokala med FortiGate som lokala VPN-Gateway. För att uppnå det här scenariot, skulle du behöver följande inställningar:

1. Virtuella nätverkets Gateway - VPN-Gateway på Azure
1. Lokal nätverksgateway - den [lokalt (FortiGate) VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) representation i Azure-molnet
1. Plats-till-plats-anslutning (route baserat) - [anslutningen mellan VPN-Gateway och den lokala routern](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Konfigurera FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Detaljerade steg-för-steg-guiden för att konfigurera en plats-till-plats-konfiguration kan hittas genom att besöka: [skapa ett VNet med en plats-till-plats-anslutning med hjälp av Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

En av de viktiga konfigurationsstegen konfigurerar IPSec-kommunikation parametrar, alla felkonfiguration leder till anslutningsproblem mellan lokala nätverk och Azure. Azure VPN-gatewayer är konfigurerad för att stödja följande IPsec-parametrar för fas 1. Observera att som tidigare nämnts inte kan ändra de här inställningarna.  Som du ser i tabellen nedan är krypteringsalgoritmer som stöds av Azure VPN-Gateway AES256, AES128 och 3DES.

### <a name="ike-phase-1-setup"></a>Installationsprogrammet för IKE fas 1

| **Egenskap** | **Principbaserad** | **RouteBased och Standard eller högpresterande VPN-gateway** |
| --- | --- | --- |
| IKE-version |IKEv1 |IKEv2 |
| Diffie-Hellman Group |Grupp 2 (1 024 bitar) |Grupp 2 (1 024 bitar) |
| Autentiseringsmetod |I förväg delad nyckel |I förväg delad nyckel |
| Krypteringsalgoritmer |AES256 AES128 3DES |AES256 3DES |
| Hash-algoritm |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Fas 1, Security Association (SA), livslängd (tid) |28 800 sekunder |10 800 sekunder |

Du skulle behöva konfigurera din FortiGate som en användare, en exempelkonfiguration finns på [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Utan vetskap du har konfigurerat din FortiGate för att använda SHA-512 som hash-algoritm. Den här algoritmen inte är en algoritm som stöds för principbaserad anslutningar fungerar din VPN-anslutning.

Dessa problem är svåra att felsöka och rot-orsakerna är ofta tveksamma. I det här fallet kan du öppna ett supportärende för att få hjälp om hur du löser problemet. Men med Azure Nätverksbevakaren felsöka API, kan du identifiera problemen på egen hand.

## <a name="troubleshooting-using-azure-network-watcher"></a>Felsökning med hjälp av Azure Nätverksbevakaren

Ansluta till Azure PowerShell om du vill felsöka din anslutning och starta den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet. Du kan hitta information om hur du använder denna cmdlet på [felsöka virtuella nätverksgateway och anslutningar - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Denna cmdlet kan ta några minuter att slutföra.

När cmdleten har slutförts kan du navigera till lagringsplatsen anges i för att hämta detaljerad information om problemet och loggar. Azure Nätverksbevakaren skapar en komprimerad mapp som innehåller följande loggfiler:

![1][1]

Öppna den fil som heter IKEErrors.txt och visar följande fel som indikerar ett problem med lokala IKE inställningen felaktig konfiguration.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Du kan få detaljerad information från Scrubbed-wfpdiag.txt om felet, som i det här fallet nämns att `ERROR_IPSEC_IKE_POLICY_MATCH` som leda till att anslutningen inte fungerar korrekt.

En annan gemensam felkonfigurering är att ange felaktig delade nycklar. Om du har angett olika delade nycklar i föregående exempel, IKEErrors.txt visas följande fel: `Error: Authentication failed. Check shared key`.

Felsöka Azure Nätverksbevakaren funktionen kan du diagnostisera och felsöka din VPN-Gateway och en anslutning med enkelt en enkel PowerShell-cmdlet. För närvarande vi stöder diagnostisering av följande villkor och arbetar för att lägga till flera villkor.

### <a name="gateway"></a>Gateway

| Feltypen | Orsak | Logga|
|---|---|---|
| NoFault | Om inga fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta Gateway eller Gateway inte har etablerats. |Nej|
| PlannedMaintenance |  Gateway-instans är under underhåll.  |Nej|
| UserDrivenUpdate | När en användare uppdatering pågår. Detta kan vara en åtgärd för storleksändring. | Nej |
| VipUnResponsive | Det går inte att nå den primära instansen av Gateway. Detta händer när hälsoavsökningen misslyckas. | Nej |
| PlatformInActive | Det finns ett problem med plattformen. | Nej|
| ServiceNotRunning | Den underliggande tjänsten körs inte. | Nej|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gateway. Detta är endast en varning.| Nej|
| ConnectionsNotConnected | Ingen av anslutningarna är anslutna. Detta är endast en varning.| Ja|
| GatewayCPUUsageExceeded | Aktuell Gateway användning CPU-användning är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Feltypen | Orsak | Logga|
|---|---|---|
| NoFault | Om inga fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta Gateway eller Gateway inte har etablerats. |Nej|
| PlannedMaintenance | Gateway-instans är under underhåll.  |Nej|
| UserDrivenUpdate | När en användare uppdatering pågår. Detta kan vara en åtgärd för storleksändring.  | Nej |
| VipUnResponsive | Det går inte att nå den primära instansen av Gateway. Det händer när hälsoavsökningen misslyckas. | Nej |
| ConnectionEntityNotFound | Konfigurationen för anslutningen saknas. | Nej |
| ConnectionIsMarkedDisconnected | Anslutningen har markerats ”frånkopplad”. |Nej|
| ConnectionNotConfiguredOnGateway | Den underliggande tjänsten har inte konfigurerats anslutningen. | Ja |
| ConnectionMarkedStandy | Den underliggande tjänsten har markerats som vänteläge.| Ja|
| Autentisering | I förväg delad nyckel matchar inte. | Ja|
| PeerReachability | Peer-gateway kan inte nås. | Ja|
| IkePolicyMismatch | Peer-gateway har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse fel | Ett fel uppstod vid parsning WFP-loggen. |Ja|

## <a name="next-steps"></a>Nästa steg

Lär dig att kontrollera VPN-Gateway-anslutningen med PowerShell och Azure Automation genom att besöka [övervakaren VPN-gatewayer Azure Nätverksbevakaren felsökning](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
