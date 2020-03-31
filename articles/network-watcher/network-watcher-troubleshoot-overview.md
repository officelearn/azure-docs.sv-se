---
title: Introduktion till felsökning av resurser
titleSuffix: Azure Network Watcher
description: Den här sidan innehåller en översikt över felsökningsfunktionerna för nätverksbevakningsresurser
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 199b4fc762919c2e3988f477c14d09fc23b0136b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840697"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introduktion till felsökning av resurser i Azure Network Watcher

Virtuella nätverksgatewayer ger anslutning mellan lokala resurser och andra virtuella nätverk i Azure. Gatewayer för övervakning och deras anslutningar är viktiga för att säkerställa att kommunikationen inte bryts. Network Watcher ger möjlighet att felsöka gateways och anslutningar. Funktionen kan anropas via portalen, PowerShell, Azure CLI eller REST API. När network watcher anropas diagnostiserar den hälsotillståndet för gatewayen eller anslutningen och returnerar lämpliga resultat. Begäran är en tidskrävande transaktion. Resultaten returneras när diagnosen är klar.

![portal][2]

## <a name="results"></a>Resultat

De preliminära resultat som returneras ger en helhetsbild av resursens hälsa. Djupare information kan tillhandahållas för resurser som visas i följande avsnitt:

Följande lista är de värden som returneras med felsöknings-API:et:

* **startTime** - Det här värdet är den tid då felsöknings-API-anropet startade.
* **endTime** - Det här värdet är den tidpunkt då felsökningen avslutades.
* **kod** - Det här värdet är ohälsosamt, om det finns ett enda diagnosfel.
* **resultat** - Resultat är en samling resultat som returneras på anslutningen eller den virtuella nätverksgatewayen.
    * **id** - Det här värdet är feltypen.
    * **sammanfattning** - Det här värdet är en sammanfattning av felet.
    * **detaljerad** - Det här värdet ger en detaljerad beskrivning av felet.
    * **recommendedActions** - Den här egenskapen är en samling rekommenderade åtgärder att vidta.
      * **actionText** - Det här värdet innehåller texten som beskriver vilka åtgärder som ska vidtas.
      * **actionUri** - Det här värdet ger URI till dokumentation om hur du ska agera.
      * **actionUriText** - Det här värdet är en kort beskrivning av åtgärdstexten.

Följande tabeller visar de olika feltyper (id under resultat från föregående lista) som är tillgängliga och om felet skapar loggar.

### <a name="gateway"></a>Gateway

| Feltyp | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel upptäcks |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway är inte etablerat |Inga|
| PlannedMaintenance |  Gateway-instansen är under underhåll  |Inga|
| UserDrivenUpdate | Det här felet inträffar när en användaruppdatering pågår. Uppdateringen kan vara en storleksändring. | Inga |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gatewayen inte kan nås på grund av ett fel i tillståndsavsökningen. | Inga |
| PlatformInActive | Det är något problem med plattformen. | Inga|
| ServiceInterunning | Den underliggande tjänsten körs inte. | Inga|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Detta fel är bara en varning.| Inga|
| AnslutningarAnslutna | Anslutningarna är inte anslutna. Detta fel är bara en varning.| Ja|
| GatewayCPUUsageExceed | Den aktuella gateway-CPU-användningen är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Feltyp | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel upptäcks |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway är inte etablerat |Inga|
| PlannedMaintenance | Gateway-instansen är under underhåll  |Inga|
| UserDrivenUpdate | Det här felet inträffar när en användaruppdatering pågår. Uppdateringen kan vara en storleksändring.  | Inga |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gatewayen inte kan nås på grund av ett fel i tillståndsavsökningen. | Inga |
| ConnectionEntityNotFound | Anslutningskonfiguration saknas | Inga |
| ConnectionIsMarkedDisconnected | Anslutningen är märkt "frånkopplad" |Inga|
| ConnectionNotConfiguredOnGateway | Den underliggande tjänsten har inte anslutningen konfigurerad. | Ja |
| AnslutningMarkedStandby | Den underliggande tjänsten markeras som vänteläge.| Ja|
| Autentisering | Fördelade nyckelmatchning | Ja|
| PeerReachability | Peer-gatewayen kan inte nås. | Ja|
| IkePolicyMismatch | Peer gateway har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse-fel | Ett fel uppstod när WFP-loggen tolkas. |Ja|

## <a name="supported-gateway-types"></a>Gateway-typer som stöds

I följande tabell visas vilka gateways och anslutningar som stöds med felsökning av Network Watcher:

|  |  |
|---------|---------|
|**Gateway-typer**   |         |
|VPN      | Stöds        |
|ExpressRoute | Stöds inte |
|**VPN-typer** | |
|Rutt baserad | Stöds|
|Policybaserad | Stöds inte|
|**Anslutningstyper**||
|IPsec| Stöds|
|VNet2Vnet| Stöds|
|ExpressRoute| Stöds inte|
|VPNClient| Stöds inte|

## <a name="log-files"></a>Loggfiler

Resursfelsökningen av loggfiler lagras i ett lagringskonto när felsökningen av resurser är klar. Följande bild visar exempelinnehållet i ett samtal som resulterade i ett fel.

![zip-fil][1]

> [!NOTE]
> I vissa fall skrivs bara en delmängd av loggfilerna till lagring.

Instruktioner om hur du hämtar filer från azure storage-konton finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Filen **ConnectionStats.txt** innehåller övergripande statistik för anslutningen, inklusive ingående och utgående byte, anslutningsstatus och den tidpunkt då anslutningen upprättades.

> [!NOTE]
> Om anropet till felsöknings-API:et återgår till felfritt är det enda som returneras i zip-filen en **ConnectionStats.txt-fil.**

Innehållet i den här filen liknar följande exempel:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**FILEN CPUStats.txt** innehåller CPU-användning och minne som är tillgängligt vid tidpunkten för testningen.  Innehållet i den här filen liknar följande exempel:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt-filen** innehåller alla IKE-fel som hittades under övervakningen.

I följande exempel visas innehållet i en IKEErrors.txt-fil. Dina fel kan vara olika beroende på problemet.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Skrubbade-wfpdiag.txt

**Den Scrubbed-wfpdiag.txt** loggfilen innehåller wfp loggen. Den här loggen innehåller loggning av paketsläpp och IKE/AuthIP-fel.

I följande exempel visas innehållet i filen Scrubbed-wfpdiag.txt. I det här exemplet var den delade nyckeln för en anslutning inte korrekt som kan ses från den tredje raden från nederkanten. Följande exempel är bara ett utdrag av hela loggen, eftersom loggen kan vara lång beroende på problemet.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

Filen **wfpdiag.txt.sum** är en logg som visar de buffertar och händelser som bearbetas.

Följande exempel är innehållet i en wfpdiag.txt.sum-fil.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du diagnostiserar ett problem med en gateway- eller gatewayanslutning finns i [Diagnostisera kommunikationsproblem mellan nätverk](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
