---
title: Övervaka Azure File Sync | Microsoft Docs
description: Så här övervakar du Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699293"
---
# <a name="monitor-azure-file-sync"></a>Övervaka Azure File Sync

Använd Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Azure File Sync transformerar Windows Server till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln beskriver hur du övervakar Azure File Sync-distributionen med hjälp av Azure Monitor, tjänsten Storage Sync och Windows Server.

Följande övervaknings alternativ är tillgängliga för närvarande.

## <a name="azure-monitor"></a>Azure Monitor

Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) om du vill visa mått och konfigurera aviseringar för synkronisering, moln nivåer och server anslutning.  

### <a name="metrics"></a>Mått

Mått för Azure File Sync är aktiverade som standard och skickas till Azure Monitor var 15: e minut.

Om du vill visa Azure File Sync mått i Azure Monitor väljer du resurs typen **Storage Sync Services** .

Följande mått för Azure File Sync är tillgängliga i Azure Monitor:

| Måttnamn | Beskrivning |
|-|-|
| Byte som har synkroniserats | Storlek på överförda data (uppladdning och nedladdning).<br><br>Processor Byte<br>Sammansättnings typ: Summa<br>Tillämpliga dimensioner: Server slut punkts namn, synkroniseringsanslutning, namn på Sync-grupp |
| Återkalla moln nivå | Storlek på data som har återkallats.<br><br>**Obs!** Måttet tas bort i framtiden. Använd storleks måttet återkalla storlek på moln nivå för att övervaka storleken på data som återkallas.<br><br>Processor Byte<br>Sammansättnings typ: Summa<br>Tillämplig dimension: Servernamn |
| Återställnings storlek för moln nivå | Storlek på data som har återkallats.<br><br>Processor Byte<br>Sammansättnings typ: Summa<br>Tillämplig dimension: Server namn, namn på Sync-grupp |
| Återställnings storlek för moln skikt per program | Storlek på data som återkallas av program.<br><br>Processor Byte<br>Sammansättnings typ: Summa<br>Tillämplig dimension: Program namn, Server namn, namn på Sync-grupp |
| Återkalla data flöde för moln nivå | Storlek på data återställnings data flöde.<br><br>Processor Byte<br>Sammansättnings typ: Summa<br>Tillämplig dimension: Server namn, namn på Sync-grupp |
| Filer som inte synkroniseras | Antal filer som inte kan synkroniseras.<br><br>Processor Count<br>Sammansättnings typ: Summa<br>Tillämpliga dimensioner: Server slut punkts namn, synkroniseringsanslutning, namn på Sync-grupp |
| Synkroniserade filer | Antal överförda filer (uppladdning och nedladdning).<br><br>Processor Count<br>Sammansättnings typ: Summa<br>Tillämpliga dimensioner: Server slut punkts namn, synkroniseringsanslutning, namn på Sync-grupp |
| Status för server online | Antal pulsslag som tagits emot från servern.<br><br>Processor Count<br>Sammansättnings typ: Maximal<br>Tillämplig dimension: Servernamn |
| Resultat av Sync-session | Resultat av synkroniseringsbegäran (1 = lyckad synkroniseringsanslutning, 0 = det gick inte att synkronisera sessionen)<br><br>Processor Count<br>Sammansättnings typer: Maximal<br>Tillämpliga dimensioner: Server slut punkts namn, synkroniseringsanslutning, namn på Sync-grupp |

### <a name="alerts"></a>Aviseringar

Om du vill konfigurera aviseringar i Azure Monitor väljer du tjänsten Storage Sync och väljer sedan det [Azure File Sync mått](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) som ska användas för aviseringen.  

I följande tabell visas några exempel scenarier som du kan använda för att övervaka och rätt mått för aviseringen:

| Scenario | Mått som ska användas för avisering |
|-|-|
| Server slut punktens hälso tillstånd i portalen = fel | Resultat av Sync-session |
| Det går inte att synkronisera filer till en server eller moln slut punkt | Filer som inte synkroniseras |
| Den registrerade servern kan inte kommunicera med tjänsten för synkronisering av lagring | Status för server online |
| Återställnings storleken för moln skiktet har överskridit 500GiB per dag  | Återställnings storlek för moln nivå |

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Tjänst för synkronisering av lagring

Om du vill visa registrerad Server hälsa, Server slut punkts hälsa och mått går du till tjänsten Storage Sync i Azure Portal. Du kan visa registrerad Server hälsa på bladet **registrerade servrar** och Server slut punktens hälsa på bladet **Synkronisera grupper** .

### <a name="registered-server-health"></a>Registrerad Server hälsa

- Om det **registrerade Server** läget är **online**, kommunicerar servern med tjänsten.
- Om det **registrerade Server** läget **visas som offline**kontrollerar du att processen för övervakning av lagrings plats (AzureStorageSyncMonitor. exe) på servern körs. Om servern finns bakom en brand vägg eller proxy kan du läsa [den här artikeln](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) för att konfigurera brand väggen och proxyn.

### <a name="server-endpoint-health"></a>Server slut punkts hälsa

- Server slut punktens hälso tillstånd i portalen baseras på de synkroniserade händelser som loggas i händelse loggen för telemetri på servern (ID 9102 och 9302). Om en Sync-session Miss lyckas på grund av ett tillfälligt fel, till exempel fel som avbrutits, kan synkronisering fortfarande visas felfritt i portalen så länge den aktuella synkroniseringen pågår. Händelse-ID 9302 används för att avgöra om filer tillämpas. Mer information finns i [synkronisering av hälso tillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [synkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Om portalen visar ett synkroniseringsfel eftersom synkroniseringen inte gör något, se [fel söknings dokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) för vägledning.

### <a name="metric-charts"></a>Mått diagram

- Följande mått diagram visas i Storage Sync service portal:

  | Måttnamn | Beskrivning | Blad namn |
  |-|-|-|
  | Byte som har synkroniserats | Storlek på överförda data (uppladdning och nedladdning) | Synkronisera grupp, Server slut punkt |
  | Återkalla moln nivå | Storlek på data som återkallas | Registrerade servrar |
  | Filer som inte synkroniseras | Antal filer som inte kan synkroniseras | Server slut punkt |
  | Synkroniserade filer | Antal överförda filer (uppladdning och nedladdning) | Synkronisera grupp, Server slut punkt |
  | Status för server online | Antal pulsslag som tagits emot från servern | Registrerade servrar |

- Läs mer i [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Diagrammen i Storage Sync Service Portal har ett tidsintervall på 24 timmar. Använd Azure Monitor om du vill visa olika tidsintervall eller dimensioner.

## <a name="windows-server"></a>Windows Server

På Windows Server kan du Visa moln nivåer, registrerad Server och synkroniseringsstatus.

### <a name="event-logs"></a>Händelseloggar

Använd händelse loggen för telemetri på-servern för att övervaka registrerad Server, synkronisering och moln nivå hälsa. Händelse loggen för telemetri finns i Loggboken under *program-och Services\Microsoft\FileSync\Agent*.

Sync-hälsa:

- Händelse-ID 9102 loggas när en Sync-session har slutförts. Använd den här händelsen för att avgöra om Sync-sessioner lyckas (**HResult = 0**) och om det finns synkroniseringsfel per objekt. Mer information finns i dokumentationen om [synkronisering av hälso tillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [meddelanden per objekt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > Synkronisering av sessioner fungerar ibland inte generellt eller har en PerItemErrorCount som inte är noll. De kommer dock fortfarande att fortsätta och vissa filer synkroniseras. Du kan se detta i de använda fälten, till exempel AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes. Dessa fält visar hur mycket av sessionen som har lyckats. Om flera Sync-sessioner visas på en rad och de har ett ökande antal tillämpade, kan du ange att synkroniseringen ska göras innan du öppnar ett support ärende.

- Händelse-ID 9302 loggas var 5 till 10 minuter om det finns en aktiv Sync-session. Använd den här händelsen för att ta reda på om den pågående synkroniseringen görs (**AppliedItemCount > 0**). Om synkronisering inte sker, bör Sync-sessionen sluta att fungera och händelse-ID 9102 kommer att loggas med felet. Mer information finns i dokumentationen för [Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)-förloppet.

Registrerad Server hälsa:

- Händelse-ID 9301 loggas var 30: e sekund när en server frågar tjänsten efter jobb. Om GetNextJob slutförs med **status = 0**, kan servern kommunicera med tjänsten. Om GetNextJob har slutförts med ett fel kan du läsa mer i [fel söknings dokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .

Hälso tillstånd för moln nivåer:

- Om du vill övervaka nivå aktivitet på en server använder du händelse-ID 9003, 9016 och 9029 i händelse loggen för telemetri, som finns i Loggboken under *program-och Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9003 ger fel distribution för en server slut punkt. Exempel: Totalt antal fel och felkod. En händelse loggas per felkod.
  - Händelse-ID 9016 ger ghosting-resultat för en volym. Exempel: Procent ledigt utrymme är, antal filer som är avbildade i sessionen och antalet filer som misslyckades med Ghost.
  - Händelse-ID 9029 tillhandahåller ghosting-sessionsinformation för en server slut punkt. Exempel: Antal filer som försökts i sessionen, antalet filer som skiktas i sessionen och antalet filer som redan har delats på nivå.
  
- Om du vill övervaka återställnings aktivitet på en server använder du händelse-ID 9005, 9006, 9009 och 9059 i händelse loggen för telemetri, som finns i Loggboken under *program-och Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9005 ger åter kallelse tillförlitlighet för en server slut punkt. Exempel: Totalt antal unika filer som används och totalt antal unika filer med misslyckad åtkomst.
  - Händelse-ID 9006 ger återställnings fel distribution för en server slut punkt. Exempel: Totalt antal misslyckade förfrågningar och felkod. En händelse loggas per felkod.
  - Händelse-ID 9009 ger information om återkallande-sessionsinformation för en server slut punkt. Exempel: DurationSeconds, CountFilesRecallSucceeded och CountFilesRecallFailed.
  - Händelse-ID 9059 tillhandahåller distribution av program åter användning för en server slut punkt. Exempel: ShareId, program namn och TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Prestandaräknare

Använd Azure File Sync prestanda räknare på servern för att övervaka synkronisering av aktiviteter.

Öppna prestanda övervakaren (Perfmon. exe) om du vill visa Azure File Sync prestanda räknare på servern. Du hittar räknarna under objekten **AFS-överförda byte** och AFS- **synkronisering** .

Följande prestanda räknare för Azure File Sync är tillgängliga i prestanda övervakaren:

| Object\Counter-namn för prestanda | Beskrivning |
|-|-|
| AFS-byte Transferred\Downloaded byte/s | Antal hämtade byte per sekund. |
| AFS-byte Transferred\Uploaded byte/s | Antal överförda byte per sekund. |
| AFS-byte Transferred\Total byte/s | Totalt antal byte per sekund (uppladdning och nedladdning). |
| AFS Sync-Operations\Downloaded Sync-filer/SEK | Antal hämtade filer per sekund. |
| AFS Sync-Operations\Uploaded Sync-filer/SEK | Antal överförda filer per sekund. |
| AFS Sync-Operations\Total Sync File-åtgärder/SEK | Totalt antal filer som har synkroniserats (uppladdning och nedladdning). |

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Överväg inställningar för brand vägg och proxy](storage-sync-files-firewall-and-proxy.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)
- [Vanliga frågor och svar om Azure Files](storage-files-faq.md)
