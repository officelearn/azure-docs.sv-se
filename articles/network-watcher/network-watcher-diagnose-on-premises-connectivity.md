---
title: Diagnostisera lokal anslutning via VPN-gateway med Azure Network Watcher | Microsoft Docs
description: Den här artikeln beskriver hur du diagnostisera lokal anslutning via VPN-gateway med felsökning av Azure Network Watcher-resurs.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 323e5d63b5f8566d570dfd47323fcf12f7c6b28b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051588"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostisera lokal anslutning via VPN-gatewayer

Azure VPN-Gateway kan du skapa hybridlösning som behovet av en säker anslutning mellan ditt lokala nätverk och Azure-nätverk. När dina behov är unika, så är valet av den lokala VPN-enhet. Azure stöder för närvarande [flera VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) som verifieras ständigt i samarbete med leverantörer för enheter. Granska de specifika inställningarna innan du konfigurerar den lokala VPN-enheten. På samma sätt kan Azure VPN Gateway har konfigurerats med en uppsättning [stöd för IPsec-parametrar](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) som används för att upprätta anslutningar. Det finns för närvarande inget sätt att ange eller välja en specifik kombination av IPsec-parametrar från Azure VPN Gateway. Inställningar för den lokala VPN-enheter måste vara i enlighet med IPsec-parametrar som föreskrivs av Azure VPN Gateway för att upprätta en anslutning mellan lokala och Azure. Om inställningarna är korrekta, där är en förlust av anslutning och fram till nu felsöka problemen var inte trivial och vanligtvis tog timmar att identifiera och åtgärda problemet.

Felsöka funktionen med Azure Network Watcher, kan du diagnostisera problem med din Gateway och dina anslutningar och inom några minuter har tillräckligt med information för att fatta ett välgrundat beslut att rätta till problemet.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenario

Du vill konfigurera en plats-till-plats-anslutning mellan Azure och lokalt med hjälp av FortiGate som den lokala VPN-Gateway. För att uppnå det här scenariot kan kräver du följande inställningar:

1. Vnet-Gateway - VPN-Gateway på Azure
1. Lokal nätverksgateway - den [lokala (FortiGate) VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) representation i Azure-molnet
1. Plats-till-plats-anslutning (routningsbaserade) - [anslutning mellan VPN-Gateway och den lokala routern](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Konfigurera FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Detaljerade steg-för-steg-guiden för att konfigurera en plats-till-plats-konfiguration kan hittas genom att besöka: [Skapa ett VNet med en plats-till-plats-anslutning med Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

En av de viktiga konfigurationsstegen konfigurera parametrar för IPsec-kommunikation, alla felkonfiguration leder till förlust av anslutning mellan det lokala nätverket och Azure. Azure VPN gateway är konfigurerad för att stödja följande IPsec-parametrar för fas 1. Observera att som vi nämnde tidigare de här inställningarna inte kan ändras.  Som du ser i tabellen nedan är krypteringsalgoritmer som stöds av Azure VPN Gateway AES256, AES128 och 3DES.

### <a name="ike-phase-1-setup"></a>IKE fas 1-konfiguration

| **Egenskap** | **Principbaserad** | **Routningsbaserad och Standard eller högpresterande VPN gateway** |
| --- | --- | --- |
| IKE-version |IKEv1 |IKEv2 |
| Diffie-Hellman Group |Grupp 2 (1 024 bitar) |Grupp 2 (1 024 bitar) |
| Autentiseringsmetod |I förväg delad nyckel |I förväg delad nyckel |
| Krypteringsalgoritmer |AES256 AES128 3DES |AES256 3DES |
| Hash-algoritm |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Fas 1, Security Association (SA), livslängd (tid) |28 800 sekunder |10 800 sekunder |

Du skulle bli ombedd att konfigurera din FortiGate som en användare, en exempelkonfiguration finns på [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Utan att veta att du konfigurerat din FortiGate för att använda SHA-512 som hash-algoritm. Eftersom den här algoritmen inte är en algoritm som stöds för principbaserad anslutningar fungerar VPN-anslutningen.

Dessa problem är svåra att felsöka och rotorsaker är ofta tveksamma. I det här fallet kan du öppna ett supportärende för att få hjälp om hur du löser problemet. Men med Azure Network Watcher felsöka API, kan du identifiera problemen på egen hand.

## <a name="troubleshooting-using-azure-network-watcher"></a>Felsöka med Azure Network Watcher

Ansluta till Azure PowerShell för att diagnostisera din anslutning och initiera den `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet. Du hittar information om hur du använder denna cmdlet på [felsöka virtuella nätverks-Gateway och anslutningar – PowerShell](network-watcher-troubleshoot-manage-powershell.md). Denna cmdlet kan ta upp till några minuter att slutföra.

När cmdleten har slutförts kan du navigera till lagringsplatsen som angetts i cmdlet för att få detaljerad information om om problemet och loggar. Azure Network Watcher skapar en komprimerad mapp som innehåller följande loggfiler:

![1][1]

Öppna den fil som heter IKEErrors.txt och visar följande fel som indikerar ett problem med den lokala IKE inställningen felkonfiguration.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Du kan hämta detaljerad information från Scrubbed-wfpdiag.txt om felet, som i det här fallet nämner det att det fanns `ERROR_IPSEC_IKE_POLICY_MATCH` som leda till att anslutningen inte fungerar korrekt.

En annan gemensam felkonfigurering är att ange felaktig delade nycklar. Om du hade angett olika delade nycklar i föregående exempel, IKEErrors.txt visas följande fel: `Error: Authentication failed. Check shared key`.

Azure Network Watcher Felsöka funktionen gör det möjligt för dig att diagnostisera och felsöka din VPN-Gateway och anslutning med lättanvända en enkel PowerShell-cmdlet. För närvarande vi stöder diagnostisering av följande villkor och arbetar för att lägga till fler villkor.

### <a name="gateway"></a>Gateway

| Zadaný typ | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta Gateway eller Gateway inte har etablerats. |Nej|
| PlannedMaintenance |  Gateway-instans är under underhåll.  |Nej|
| UserDrivenUpdate | När en uppdatering pågår. Detta kan vara en storleksändringen. | Nej |
| VipUnResponsive | Det går inte att nå den primära instansen av gatewayen. Detta händer när hälsoavsökningen misslyckas. | Nej |
| PlatformInActive | Det finns ett problem med plattformen. | Nej|
| ServiceNotRunning | Den underliggande tjänsten körs inte. | Nej|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Detta är endast en varning.| Nej|
| ConnectionsNotConnected | Ingen av anslutningarna är anslutna. Detta är endast en varning.| Ja|
| GatewayCPUUsageExceeded | Aktuell gatewayanvändning CPU-användning är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Zadaný typ | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta Gateway eller Gateway inte har etablerats. |Nej|
| PlannedMaintenance | Gateway-instans är under underhåll.  |Nej|
| UserDrivenUpdate | När en uppdatering pågår. Detta kan vara en storleksändringen.  | Nej |
| VipUnResponsive | Det går inte att nå den primära instansen av gatewayen. Det händer när hälsoavsökningen misslyckas. | Nej |
| ConnectionEntityNotFound | Anslutningskonfiguration saknas. | Nej |
| ConnectionIsMarkedDisconnected | Anslutningen har markerats ”frånkopplad”. |Nej|
| ConnectionNotConfiguredOnGateway | Den underliggande tjänsten har inte den anslutningen har konfigurerats. | Ja |
| ConnectionMarkedStandby | Den underliggande tjänsten har markerats som vänteläge.| Ja|
| Authentication | I förväg delad nyckel matchar inte. | Ja|
| PeerReachability | Peer-gatewayen kan inte nås. | Ja|
| IkePolicyMismatch | Peer-gateway har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse Error | Ett fel uppstod parsning WFP-loggen. |Ja|

## <a name="next-steps"></a>Nästa steg

Lär dig att kontrollera VPN Gateway-anslutningar med PowerShell och Azure Automation genom att besöka [övervaka VPN-gatewayer med Azure Network Watcher troubleshooting](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
