---
title: Övervaka Azure File Sync | Microsoft Docs
description: Så här övervakar du Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 5a0d02768b0fbd23e33d13c5e5c3fe84a41cdc52
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243662"
---
# <a name="monitor-azure-file-sync"></a>Övervaka Azure File Sync

Använd Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln beskriver hur du övervakar dina Azure File Sync-distribution med Azure-portalen och Windows Server.

Följande övervakningsalternativ finns för närvarande:

## <a name="azure-portal"></a>Azure Portal

Du kan visa registrerad server hälsotillstånd, Servertillstånd slutpunkt (sync hälsa) och mått i Azure-portalen.

### <a name="storage-sync-service"></a>Tjänst för synkronisering av lagring

Om du vill visa registrerad server hälsotillstånd, Servertillstånd för slutpunkten och mått, går du till Storage Sync-tjänsten i Azure-portalen. Registrerad server health kan visas på bladet registrerade servrar. Serverhälsa slutpunkt kan visas på bladet synkronisera grupper.

Registered Server Health
- Om tillståndet registrerad server är Online, kan servern kommunicera med tjänsten.
- Om registrerad server tillståndet är Offline visas, kontrollerar du processen övervakaren lagring för synkronisering (AzureStorageSyncMonitor.exe) på servern körs. Om servern finns bakom en brandvägg eller proxyserver kan du konfigurera brandväggen och proxyservern per [dokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Serverhälsa-slutpunkt
- Serverhälsa för slutpunkten i portalen är baserad på Synkronisera händelser som loggas i händelseloggen telemetri på servern (ID 9102 och 9302). Om synkroniseringssession misslyckas på grund av ett tillfälligt fel (till exempel fel som har avbrutits), kan fortfarande synkronisera visar felfria i portalen så länge som den aktuella synkroniseringssessionen är framsteg (händelse-ID 9302 används för att avgöra om filer som används). Se följande dokumentation för mer information: [Synkronisera hälsotillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [synkronisera förloppet](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Om portalen visar ett synkroniseringsfel på grund av att synkronisera inte önskade förlopp, kontrollerar du den [Felsökningsdokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) anvisningar.

Mått
- Följande mått kan visas i den Lagringssynkroniseringstjänst-portalen:

  | Måttnamn | Beskrivning | Portalen blade(s) | 
  |-|-|-|
  | Byte som har synkroniserats | Storleken på data som överförs (uppladdning och nedladdning) | Synkroniseringsgruppen, Server-slutpunkt |
  | Lagringsnivåer återkallande i molnet | Storleken på data som har återkallats | Registrerade servrar |
  | Filer som inte synkroniseras | Antal filer som inte kan synkronisera | Server-slutpunkt |
  | Filer som har synkroniserats | Antal filer överförs (uppladdning och nedladdning) | Synkroniseringsgruppen, Server-slutpunkt |
  | Onlinestatus för Server | Antal pulsslag togs emot från servern | Registrerade servrar |

- Mer information finns i [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor) avsnittet. 

  > [!Note]  
  > Diagrammen i den Lagringssynkroniseringstjänst portalen har ett tidsintervall på 24 timmar. Använd Azure Monitor för att visa olika tidsintervall eller dimensioner.


### <a name="azure-monitor"></a>Azure Monitor

Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) att övervaka sync, lagringsnivåer för moln och anslutning. Mätvärden för Azure File Sync är aktiverade som standard och skickas till Azure Monitor var 15: e minut.

Om du vill visa Azure File Sync-mått i Azure Monitor, Välj resurstypen lagringstjänster för synkronisering.

Följande mått för Azure File Sync är tillgängliga i Azure Monitor:

| Måttnamn | Beskrivning |
|-|-|
| Byte som har synkroniserats | Storleken på data som överförs (uppladdning och nedladdning).<br><br>Enhet: Byte<br>Mängdtyp: Summa<br>Tillämpliga mått: Server-slutpunkt namn, riktning, synkronisera Synkroniseringsgruppsnamn |
| Lagringsnivåer återkallande i molnet | Storlek på data som har återkallats.<br><br>Enhet: Byte<br>Mängdtyp: Summa<br>Tillämpliga dimension: Servernamn |
| Filer som inte synkroniseras | Antal filer som inte kan synkroniseras.<br><br>Enhet: Antal<br>Mängdtyp: Summa<br>Tillämpliga mått: Server-slutpunkt namn, riktning, synkronisera Synkroniseringsgruppsnamn |
| Filer som har synkroniserats | Antal filer överförs (uppladdning och nedladdning).<br><br>Enhet: Antal<br>Mängdtyp: Summa<br>Tillämpliga mått: Server-slutpunkt namn, riktning, synkronisera Synkroniseringsgruppsnamn |
| Onlinestatus för Server | Antal pulsslag togs emot från servern.<br><br>Enhet: Antal<br>Mängdtyp: Maximal<br>Tillämpliga dimension: Servernamn |
| Synkronisera session resultat | Synkronisera session resultatet (1 = synkronisering session; 0 = misslyckade synkroniseringssessionen)<br><br>Enhet: Antal<br>Aggregeringstyper: Maximal<br>Tillämpliga mått: Server-slutpunkt namn, riktning, synkronisera Synkroniseringsgruppsnamn |

## <a name="windows-server"></a>Windows Server

Du kan visa molnlagringsnivåer, registrerad server och synkronisera hälsotillstånd på Windows-Server.

### <a name="event-logs"></a>Händelseloggar

Använda händelseloggen telemetri på servern för att övervaka registrerad server, synkronisering och lagringsnivåer hälsotillstånd för moln. Telemetri händelseloggen finns under program och Services\Microsoft\FileSync\Agent i Loggboken.

Sync Health
- Händelse-ID 9102 loggas när en synkroniseringssessionen har slutförts. Den här händelsen ska användas för att avgöra om synkroniseringssessioner slutförs (HResult = 0) och om det finns per objekt synkroniseras fel. Se följande dokumentation för mer information: [Synkronisera hälsotillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [fel Per objekt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Ibland synkroniseringssessioner misslyckas övergripande eller har en noll PerItemErrorCount men fortfarande göra vidare framsteg med vissa filer synkroniseras har. Detta kan ses i tillämpade fält (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes), som visar hur mycket av sessionen lyckas. Om du ser flera synkroniseringssessioner i en rad som misslyckas men har ett ökande antal tillämpade, bör du ge tid att försöka igen innan du öppnar ett supportärende.

- Händelse-ID 9302 loggas var 5-10 minuter om det finns en ActiveSync-session. Den här händelsen ska användas för att avgöra om den aktuella synkroniseringssessionen är framsteg (AppliedItemCount > 0). Om synkronisering inte framsteg, synkroniseringssessionen misslyckas till sist och kommer att loggas en händelse-ID 9102 med fel. Se följande dokumentation för mer information: [Synkronisering pågår](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Registered Server Health
- Händelse-ID 9301 loggas med 30 sekunders mellanrum när en server frågar tjänsten för jobb. Om GetNextJob är klar med status = 0, servern kan kommunicera med tjänsten. Om GetNextJob är klar med ett fel, kontrollera den [Felsökningsdokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) anvisningar.

Cloud Tiering Health
- Om du vill övervaka lagringsnivåer på en server, använder du händelse-ID 9003 9016 och 9029 i händelseloggen telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken).

  - Händelse-ID 9003 innehåller felfördelningen för en serverslutpunkt. Till exempel Total Felräkning ErrorCode, osv. Observera att en händelse loggas per felkod.
  - Händelse-ID 9016 innehåller eftersläpningar resultat för en volym. Till exempel ledigt utrymme i procent är avbildad antalet filer i sessionen, antal filer kunde inte ghost osv.
  - Händelse-ID 9029 innehåller eftersläpningar sessionsinformation för en serverslutpunkt. Till exempel antalet filer i sessionen, antal filer nivåer i sessionen, antalet filer som redan har nivåindelade, osv.
  
- Du övervakar återställningar på en server med händelse-ID 9005 9006, 9009 och 9059 i händelseloggen telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken).

  - Händelse-ID 9005 ger återkallande tillförlitligheten hos en serverslutpunkt. Till exempel Total unika filer som används, totalt antal unika filer med misslyckade åtkomst osv.
  - Händelse-ID 9006 ger återkallande felfördelningen för en serverslutpunkt. Exempel: Totalt antal misslyckade begäranden, ErrorCode, osv. Observera att en händelse loggas per felkod.
  - Händelse-ID 9009 ger återkallande sessionsinformation för en serverslutpunkt. Till exempel DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, osv.
  - Händelse-ID 9059 ger återkallande av programdistribution för en serverslutpunkt. Till exempel ShareId, programnamn och TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Prestandaräknare

Använda Azure File Sync-prestandaräknare på servern för att övervaka synkroniseringsaktivitet.

Visa prestanda för Azure File Sync räknare på servern, Starta Prestandaövervakaren (Perfmon.exe) och räknarna kan hittas under AFS byte överförs och AFS synkroniseringsåtgärder objekt.

Följande prestandaräknare för Azure File Sync är tillgängliga i Prestandaövervakaren:

| Prestanda objekt räknare namn | Beskrivning |
|-|-|
| AFS byte Transferred\Downloaded byte/sek | Antal hämtade byte per sekund. |
| AFS byte Transferred\Uploaded byte/sek | Antal byte som överförs per sekund. |
| AFS byte Transferred\Total byte/sek | Totalt antal byte per sekund (uppladdning och nedladdning). |
| AFS synkronisering Operations\Downloaded synkronisera filer/sek | Antal filer som hämtas per sekund. |
| AFS synkronisering Operations\Uploaded synkronisera filer/sek | Antal filer som överförts per sekund. |
| AFS synkronisering Operations\Total synkronisering fil/sek | Totalt antal filer som har synkroniserats (uppladdning och nedladdning). |

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Överväg att brandvägg och proxyinställningar](storage-sync-files-firewall-and-proxy.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Felsök Azure File Sync](storage-sync-files-troubleshoot.md)
- [Vanliga och frågor svar om Azure Files](storage-files-faq.md)
