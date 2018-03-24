---
title: Introduktion till resursen felsökning i Azure-Nätverksbevakaren | Microsoft Docs
description: Den här sidan innehåller en översikt över funktioner för felsökning av Nätverksbevakaren resurs
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
ms.openlocfilehash: 646caa5e4aacd58377c0a2b5985a69277d00cec3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introduktion till felsökning i Azure Nätverksbevakaren resurs

Virtuella Nätverksgatewayer anslutningsbarhet mellan lokala resurser och andra virtuella nätverk i Azure. Övervakning gateways och deras anslutningar är viktiga för att säkerställa kommunikationen är inte skadad. Nätverksbevakaren ger möjlighet att felsöka gateways och anslutningar. Kapacitet kan anropas via portalen, PowerShell, Azure CLI eller REST API. När den anropas, Nätverksbevakaren diagnostiserar hälsotillståndet för gateway eller anslutningen och returnerar en rapport. Begäran är en tidskrävande transaktion. Resultaten returneras när diagnosen är klar.

![portal][2]

## <a name="results"></a>Resultat

Preliminär resultaten ger en övergripande bild av hälsotillståndet för resursen. Mer detaljerad information kan anges för resurser som visas i följande avsnitt:

Följande lista innehåller de värden som returneras med felsökning API:

* **startTime** -värdet är felsöka API-anropet startades.
* **endTime** -värdet är den tid då felsökning avslutades.
* **koden** -värdet är ohälsosamma, om det finns ett enda diagnos-fel.
* **resultaten** -resultat är en samling av resultaten på anslutningen eller den virtuella nätverksgatewayen.
    * **ID** -värdet är feltypen.
    * **Översikt över** -värdet är en sammanfattning av felet.
    * **detaljerad** -värdet ger en detaljerad beskrivning av felet.
    * **recommendedActions** -den här egenskapen är en uppsättning rekommenderade åtgärder som ska vidtas.
      * **actionText** -värdet innehåller den text som beskriver åtgärd att vidta.
      * **actionUri** -värdet innehåller URI-dokumentationen om hur fungerar.
      * **actionUriText** -det här värdet är en kort beskrivning av åtgärden text.

Följande tabeller visar vilka typer av olika fel (id under resultat från föregående lista) som är tillgängliga och om felet skapar loggar.

### <a name="gateway"></a>Gateway

| Feltypen | Orsak | Logga|
|---|---|---|
| NoFault | Om inga fel har identifierats |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway inte har etablerats |Nej|
| PlannedMaintenance |  Gateway-instans är under Underhåll  |Nej|
| UserDrivenUpdate | Det här felet uppstår när en användare uppdatering pågår. Uppdateringen kan vara en åtgärd för storleksändring. | Nej |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gateway inte kan nås på grund av ett hälsotillstånd, uteblivna avsökning. | Nej |
| PlatformInActive | Det finns ett problem med plattformen. | Nej|
| ServiceNotRunning | Den underliggande tjänsten körs inte. | Nej|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Det här felet är bara en varning.| Nej|
| ConnectionsNotConnected | Anslutningar är inte ansluten. Det här felet är bara en varning.| Ja|
| GatewayCPUUsageExceeded | Aktuell gateway CPU-användning är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Feltypen | Orsak | Logga|
|---|---|---|
| NoFault | Om inga fel har identifierats |Ja|
| GatewayNotFound | Det går inte att hitta gateway eller gateway inte har etablerats |Nej|
| PlannedMaintenance | Gateway-instans är under Underhåll  |Nej|
| UserDrivenUpdate | Det här felet uppstår när en användare uppdatering pågår. Uppdateringen kan vara en åtgärd för storleksändring.  | Nej |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gateway inte kan nås på grund av ett hälsotillstånd, uteblivna avsökning. | Nej |
| ConnectionEntityNotFound | Konfigurationen för anslutningen saknas | Nej |
| ConnectionIsMarkedDisconnected | Anslutningen har markerats ”frånkopplad” |Nej|
| ConnectionNotConfiguredOnGateway | Den underliggande tjänsten har inte konfigurerats anslutningen. | Ja |
| ConnectionMarkedStandy | Den underliggande tjänsten har markerats som vänteläge.| Ja|
| Autentisering | I förväg delad nyckel stämmer inte | Ja|
| PeerReachability | Peer-gateway kan inte nås. | Ja|
| IkePolicyMismatch | Peer-gateway har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse fel | Ett fel uppstod vid parsning WFP-loggen. |Ja|

## <a name="supported-gateway-types"></a>Gateway-typer som stöds

I följande tabell visas vilka gateways och anslutningar stöds med Nätverksbevakaren Felsökning:

|  |  |
|---------|---------|
|**Gateway-typer**   |         |
|VPN      | Stöds        |
|ExpressRoute | Stöds inte |
|**VPN-typer** | |
|Vägen baserat | Stöds|
|Principbaserad | Stöds inte|
|**Anslutningstyper**||
|IPSec| Stöds|
|VNet2Vnet| Stöds|
|ExpressRoute| Stöds inte|
|VPNClient| Stöds inte|

## <a name="log-files"></a>Loggfiler

Resursen felsökning loggfilerna lagras i ett lagringskonto efter resurs felsökning har slutförts. Följande bild visar exempel innehållet i ett anrop som resulterade i ett fel.

![ZIP-filen][1]

> [!NOTE]
> I vissa fall skrivs endast en delmängd av loggfiler till lagring.

Anvisningar för att hämta filer från azure storage-konton, referera till [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Lagringsutforskaren. Mer information om Lagringsutforskaren hittar du här på följande länk: [Lagringsutforskaren](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Den **ConnectionStats.txt** filen innehåller övergripande statistik för anslutningen, inklusive ingående och utgående byte, anslutningsstatus och den tid som anslutningen har upprättats.

> [!NOTE]
> Om anropet till felsökning API returnerar felfri, det enda som returneras i zip-filen är en **ConnectionStats.txt** fil.

Innehållet i den här filen liknar följande exempel:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Den **CPUStats.txt** filen innehåller CPU-användning och minne vid tidpunkten för testning.  Innehållet i den här filen liknar följande exempel:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Den **IKEErrors.txt** filen innehåller några IKE-fel som identifierades under övervakning.

I följande exempel visar innehållet i en IKEErrors.txt-fil. Felen kan vara olika beroende på problemet.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Den **Scrubbed wfpdiag.txt** loggfilen innehåller wfp-loggen. Den här loggfilen innehåller loggning av paketet släpp och IKE/AuthIP fel.

I följande exempel visar innehållet i filen Scrubbed wfpdiag.txt. I det här exemplet var den delade nyckeln för en anslutning inte på rätt sätt kan ses från den tredje raden längst ned. I följande exempel är bara ett fragment av hela loggen eftersom loggen kan vara tidskrävande beroende på problemet.

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

Den **wfpdiag.txt.sum** filen är en logg som innehåller buffertar och händelser som har bearbetats.

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

Lär dig att diagnostisera VPN-gatewayer och anslutningar via portalen genom att besöka [Gateway felsökning – Azure-portalen](network-watcher-troubleshoot-manage-portal.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
