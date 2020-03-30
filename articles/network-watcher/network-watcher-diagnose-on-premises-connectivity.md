---
title: Diagnostisera lokal anslutning via VPN-gateway
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du diagnostiserar lokal anslutning via VPN-gateway med felsökning av Azure Network Watcher-resurser.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 835b3a69e779b536961110b674ae67f4e8c13ce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845066"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostisera lokal anslutning via VPN-gateways

Med Azure VPN Gateway kan du skapa en hybridlösning som tillgodoser behovet av en säker anslutning mellan ditt lokala nätverk och ditt virtuella Azure-nätverk. Eftersom dina krav är unika, så är valet av lokal VPN-enhet. Azure stöder för närvarande [flera VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) som ständigt valideras i samarbete med enhetsleverantörerna. Granska de enhetsspecifika konfigurationsinställningarna innan du konfigurerar din lokala VPN-enhet. På samma sätt är Azure VPN Gateway konfigurerad med en uppsättning [IPsec-parametrar som stöds](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) som används för att upprätta anslutningar. För närvarande finns det inget sätt för dig att ange eller välja en specifik kombination av IPsec-parametrar från Azure VPN Gateway. För att upprätta en lyckad anslutning mellan lokala och Azure måste de lokala VPN-enhetsinställningarna vara i enlighet med IPsec-parametrarna som föreskrivs av Azure VPN Gateway. Om inställningarna är felaktiga går anslutningen förlorad och felsökningen hittills var inte trivial och det tog vanligtvis timmar att identifiera och åtgärda problemet.

Med felsökfunktionen för Azure Network Watcher kan du diagnostisera eventuella problem med din gateway och anslutningar och inom några minuter ha tillräckligt med information för att fatta ett välgrundat beslut för att åtgärda problemet.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenario

Du vill konfigurera en plats-till-plats-anslutning mellan Azure och lokalt med FortiGate som lokal VPN-gateway. För att uppnå det här scenariot kräver du följande inställningar:

1. Virtual Network Gateway - VPN-gatewayen på Azure
1. Lokal nätverksgateway – den [lokala (FortiGate) VPN Gateway-representationen i Azure-molnet](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)
1. Anslutning från plats till plats (ruttbaserad) – [Anslutning mellan VPN-gatewayen och den lokala routern](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [Konfigurera FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Detaljerad steg för steg-vägledning för att konfigurera en plats-till-plats-konfiguration kan hittas genom att besöka: [Skapa ett virtuella nätverk med en plats-till-plats-anslutning med Azure-portalen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Ett av de kritiska konfigurationsstegen är att konfigurera IPsec-kommunikationsparametrarna, eventuell felkonfiguration leder till förlust av anslutning mellan det lokala nätverket och Azure. Azure VPN Gateways är konfigurerade för att stödja följande IPsec-parametrar för fas 1. Som du kan se i tabellen nedan är krypteringsalgoritmerna som stöds av Azure VPN Gateway AES256, AES128 och 3DES.

### <a name="ike-phase-1-setup"></a>IKE fas 1-inställning

| **Egenskap** | **Principbaserad** | **RouteBased och Standard eller Högpresterande VPN-gateway** |
| --- | --- | --- |
| IKE-version |IKEv1 |IKEv2 |
| Diffie-Hellman Group |Grupp 2 (1 024 bitar) |Grupp 2 (1 024 bitar) |
| Autentiseringsmetod |I förväg delad nyckel |I förväg delad nyckel |
| Krypteringsalgoritmer |AES256 AES128 3DES |AES256 3DES |
| Hash-algoritm |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Fas 1, Security Association (SA), livslängd (tid) |28 800 sekunder |28 800 sekunder |

Som användare måste du konfigurera din FortiGate, en exempelkonfiguration finns på [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Omedvetet har du konfigurerat din FortiGate att använda SHA-512 som hash-algoritm. Eftersom den här algoritmen inte är en algoritm som stöds för principbaserade anslutningar fungerar din VPN-anslutning.

Dessa problem är svåra att felsöka och grundorsakerna är ofta inte intuitiva. I det här fallet kan du öppna en supportbiljett för att få hjälp med att lösa problemet. Men med Azure Network Watcher felsöka API, kan du identifiera dessa problem på egen hand.

## <a name="troubleshooting-using-azure-network-watcher"></a>Felsöka med Azure Network Watcher

Om du vill diagnostisera anslutningen ansluter du `Start-AzNetworkWatcherResourceTroubleshooting` till Azure PowerShell och initierar cmdleten. Du hittar information om hur du använder den här cmdleten vid [Felsöka Virtual Network Gateway och anslutningar - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Denna cmdlet kan ta upp till några minuter att slutföra.

När cmdleten är klar kan du navigera till den lagringsplats som anges i cmdleten för att få detaljerad information om problemet och loggarna. Azure Network Watcher skapar en zip-mapp som innehåller följande loggfiler:

![1][1]

Öppna filen som heter IKEErrors.txt och visar följande fel, vilket indikerar ett problem med lokala IKE-inställningsfelkonfiguration.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Du kan få detaljerad information från Scrubbed-wfpdiag.txt om felet, som i `ERROR_IPSEC_IKE_POLICY_MATCH` detta fall nämner att det var som leder till anslutning inte fungerar korrekt.

En annan vanlig felkonfiguration är att ange felaktiga delade nycklar. Om du i föregående exempel hade angett olika delade nycklar visas följande fel i IKEErrors.txt: `Error: Authentication failed. Check shared key`.

Med felsökfunktionen för Azure Network Watcher kan du diagnostisera och felsöka din VPN-gateway och anslutning med hjälp av en enkel PowerShell-cmdlet. För närvarande stöder vi att diagnostisera följande villkor och arbetar för att lägga till fler villkor.

### <a name="gateway"></a>Gateway

| Feltyp | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway är inte etablerat. |Inga|
| PlannedMaintenance |  Underhåll utförs på Gateway-instansen.  |Inga|
| UserDrivenUpdate | När en användaruppdatering pågår. Detta kan vara en storleksåtgärd. | Inga |
| VipUnResponsive | Det går inte att nå den primära instansen av gatewayen. Detta händer när hälsoavsökningen misslyckas. | Inga |
| PlatformInActive | Det är något problem med plattformen. | Inga|
| ServiceInterunning | Den underliggande tjänsten körs inte. | Inga|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Det här är bara en varning.| Inga|
| AnslutningarAnslutna | Ingen av anslutningarna är anslutna. Det här är bara en varning.| Ja|
| GatewayCPUUsageExceed | Den aktuella CPU-användningen för gateway-användning är > 95 %. | Ja |

### <a name="connection"></a>Anslutning

| Feltyp | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway är inte etablerat. |Inga|
| PlannedMaintenance | Underhåll utförs på Gateway-instansen.  |Inga|
| UserDrivenUpdate | När en användaruppdatering pågår. Detta kan vara en storleksåtgärd.  | Inga |
| VipUnResponsive | Det går inte att nå den primära instansen av gatewayen. Det händer när hälsosonden misslyckas. | Inga |
| ConnectionEntityNotFound | Anslutningskonfigurationen saknas. | Inga |
| ConnectionIsMarkedDisconnected | Anslutningen är märkt "frånkopplad". |Inga|
| ConnectionNotConfiguredOnGateway | Den underliggande tjänsten har inte anslutningen konfigurerad. | Ja |
| AnslutningMarkedStandby | Den underliggande tjänsten markeras som vänteläge.| Ja|
| Autentisering | Dela upp nyckeln i förväg. | Ja|
| PeerReachability | Peer-gatewayen kan inte nås. | Ja|
| IkePolicyMismatch | Peer gateway har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse-fel | Ett fel uppstod när WFP-loggen tolkas. |Ja|

## <a name="next-steps"></a>Nästa steg

Lär dig att kontrollera VPN Gateway-anslutning med PowerShell och Azure Automation genom att besöka [Övervaka VPN-gateways med felsökning av Azure Network Watcher](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
