---
title: Introduktion till felsökning i Azure Network Watcher-resurs | Microsoft Docs
description: Den här sidan innehåller en översikt över funktioner för felsökning i Network Watcher-resurs
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 8048dde6158d9eaa9bf38a8c3020420b81bdd55b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099789"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introduktion till felsökning i Azure Network Watcher-resurs

Virtuella Nätverksgatewayer möjliggöra anslutning mellan lokala resurser och andra virtuella nätverk i Azure. Gatewayer för övervakning och deras anslutningar är viktiga för att säkerställa att kommunikationen inte bryts. Network Watcher kan du felsöka gateways och anslutningar. Funktionen kan anropas via portalen, PowerShell, Azure CLI eller REST API. När den anropas, Network Watcher diagnostiserar hälsotillståndet för gateway eller anslutning och returnerar en rapport. Begäran är en tidskrävande transaktion. Resultaten returneras när diagnosen har slutförts.

![portal][2]

## <a name="results"></a>Resultat

Preliminär resultaten ger en övergripande bild av hälsotillståndet för resursen. Mer detaljerad information kan anges för resurser som du ser i följande avsnitt:

I följande lista är de värden som returneras med Felsök API:

* **startTime** – det här värdet är Felsök API-anropet startades.
* **endTime** – det här värdet är den tid då felsökning avslutades.
* **kod** – det här värdet är inte felfri, om det finns ett enda diagnos-fel.
* **resultaten** -resultatet är en uppsättning resultat som returneras på anslutningen eller den virtuella nätverksgatewayen.
    * **ID** – det här värdet är typ av fel.
    * **Sammanfattning av** – det här värdet är en sammanfattning av felet.
    * **detaljerad** – det här värdet innehåller en detaljerad beskrivning av felet.
    * **recommendedActions** -den här egenskapen är en uppsättning rekommenderade åtgärder som ska vidtas.
      * **actionText** – det här värdet innehåller den text som beskriver åtgärd att vidta.
      * **actionUri** – det här värdet innehåller URI: N till dokumentationen om hur så att den fungerar.
      * **actionUriText** – det här värdet är en kort beskrivning av åtgärden texten.

Följande tabeller visar de olika fel-typer (id under resultat i listan ovan) som är tillgängliga och om felet skapar loggar.

### <a name="gateway"></a>Gateway

| Zadaný typ | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel har identifierats |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway inte har etablerats |Nej|
| PlannedMaintenance |  Gateway-instans är under Underhåll  |Nej|
| UserDrivenUpdate | Det här felet uppstår när en uppdatering pågår. Uppdateringen kan vara en storleksändringen. | Nej |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gatewayen inte kan nås på grund av en avsökning uteblivna. | Nej |
| PlatformInActive | Det finns ett problem med plattformen. | Nej|
| ServiceNotRunning | Den underliggande tjänsten körs inte. | Nej|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Det här felet är bara en varning.| Nej|
| ConnectionsNotConnected | Anslutning är inte ansluten. Det här felet är bara en varning.| Ja|
| GatewayCPUUsageExceeded | Den aktuella gatewayen CPU-användning är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Zadaný typ | Orsak | Logga|
|---|---|---|
| NoFault | När inget fel har identifierats |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway inte har etablerats |Nej|
| PlannedMaintenance | Gateway-instans är under Underhåll  |Nej|
| UserDrivenUpdate | Det här felet uppstår när en uppdatering pågår. Uppdateringen kan vara en storleksändringen.  | Nej |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gatewayen inte kan nås på grund av en avsökning uteblivna. | Nej |
| ConnectionEntityNotFound | Anslutningskonfiguration saknas | Nej |
| ConnectionIsMarkedDisconnected | Anslutningen har markerats ”frånkopplad” |Nej|
| ConnectionNotConfiguredOnGateway | Den underliggande tjänsten har inte den anslutningen har konfigurerats. | Ja |
| ConnectionMarkedStandby | Den underliggande tjänsten har markerats som vänteläge.| Ja|
| Autentisering | I förväg delad nyckel matchar inte | Ja|
| PeerReachability | Peer-gatewayen kan inte nås. | Ja|
| IkePolicyMismatch | Peer-gateway har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse fel | Ett fel uppstod parsning WFP-loggen. |Ja|

## <a name="supported-gateway-types"></a>Gateway-typer som stöds

I följande tabell visas vilka gateways och anslutningar som stöds med Network Watcher troubleshooting:

|  |  |
|---------|---------|
|**Gateway-typer**   |         |
|VPN      | Stöds        |
|ExpressRoute | Stöds inte |
|**VPN-typer** | |
|Routningsbaserade | Stöds|
|Principbaserad | Stöds inte|
|**Anslutningstyper**||
|IPSec| Stöds|
|VNet2Vnet| Stöds|
|ExpressRoute| Stöds inte|
|VPNClient| Stöds inte|

## <a name="log-files"></a>Loggfiler

Resursen felsökning loggfilerna lagras i ett lagringskonto efter resurs felsökning har slutförts. I följande bild visas exempel innehållet i ett anrop som resulterade i ett fel.

![ZIP-filen][1]

> [!NOTE]
> I vissa fall kan skrivs endast en delmängd av loggfilerna till lagring.

Instruktioner om att ladda ned filer från azure storage-konton finns i [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Den **ConnectionStats.txt** filen innehåller övergripande statistik för anslutningen, inklusive ingående och utgående byte, anslutningsstatus och den tid som anslutningen har upprättats.

> [!NOTE]
> Om anropet till felsökning API: et returnerar felfritt, det enda som returneras i zip-filen är en **ConnectionStats.txt** fil.

Innehållet i den här filen liknar följande exempel:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Den **CPUStats.txt** filen innehåller CPU-användning och minne som är tillgänglig vid tidpunkten för testning.  Innehållet i den här filen liknar följande exempel:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Den **IKEErrors.txt** filen innehåller några IKE-fel som har identifierats under övervakningen.

I följande exempel visar innehållet i en IKEErrors.txt-fil. Felen kan vara olika beroende på problemet.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Gömd wfpdiag.txt

Den **Scrubbed wfpdiag.txt** loggfilen innehåller wfp-loggen. Den här loggfilen innehåller loggning av paket släpp och IKE/AuthIP fel.

I följande exempel visar innehållet i filen Scrubbed wfpdiag.txt. I det här exemplet var den delade nyckeln för en anslutning inte korrekt eftersom kan ses från den tredje raden längst ned. I följande exempel är bara ett kort utdrag av hela loggen, eftersom loggen kan vara långa beroende på problemet.

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

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.SUM

Den **wfpdiag.txt.sum** filen är en logg som innehåller buffertar och händelser som bearbetas.

I följande exempel är innehållet i en wfpdiag.txt.sum-fil.
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

Läs hur du diagnostiserar problem med en gateway eller gateway-anslutning i [diagnostisera kommunikationsproblem mellan nätverk](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
