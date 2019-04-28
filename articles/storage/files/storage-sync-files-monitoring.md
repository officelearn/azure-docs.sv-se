---
title: Övervaka Azure File Sync | Microsoft Docs
description: Så här övervakar du Azure File Sync.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c0f19e3ea4f5952ac96b589fa267a2136c85e4f3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759476"
---
# <a name="monitor-azure-file-sync"></a>Övervaka Azure File Sync

Använd Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln beskriver hur du övervakar distributionen av Azure File Sync med hjälp av Azure-portalen och Windows Server.

Följande övervakningsalternativ finns för närvarande.

## <a name="azure-portal"></a>Azure Portal

Du kan visa registrerad server hälsotillstånd, Servertillstånd slutpunkt (sync hälsa) och mått i Azure-portalen.

### <a name="storage-sync-service"></a>Tjänst för synkronisering av lagring

Om du vill visa registrerad server hälsotillstånd, Servertillstånd för slutpunkten och mått, går du till Storage Sync-tjänsten i Azure-portalen. Du kan visa registrerad server health i den **registrerade servrar** bladet och server slutpunktshälsa i den **synkronisera grupper** bladet.

Registrerad server health:

- Om den **registrerad server** tillstånd är **Online**, servern kan kommunicera med tjänsten.
- Om den **registrerad server** tillstånd är **visas Offline**, verifiera att processen övervakaren lagring för synkronisering (AzureStorageSyncMonitor.exe) på servern körs. Om servern finns bakom en brandvägg eller proxyserver kan du läsa [i den här artikeln](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) att konfigurera brandväggen och proxyservern.

Serverhälsa-slutpunkten:

- Serverhälsa för slutpunkten i portalen är baserad på Synkronisera händelser som loggas i händelseloggen telemetri på servern (ID 9102 och 9302). Om en synkroniseringssessionen misslyckas på grund av ett tillfälligt fel, t.ex. fel har avbrutits visas sync fortfarande felfri i portalen så länge som den aktuella synkroniseringssessionen är framsteg. Händelse-ID 9302 används för att avgöra om filer som används. Mer information finns i [synkronisera hälsotillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [synkronisera förloppet](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Om portalen visar ett synkroniseringsfel eftersom synkronisering inte gör framsteg, se den [Felsökningsdokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) anvisningar.

Mått:

- Följande mått kan visas i den Lagringssynkroniseringstjänst-portalen:

  | Måttnamn | Beskrivning | Namn på bladet |
  |-|-|-|
  | Byte som har synkroniserats | Storleken på data som överförs (uppladdning och nedladdning) | Synkroniseringsgruppen, Server-slutpunkt |
  | Lagringsnivåer återkallande i molnet | Storleken på data som har återkallats | Registrerade servrar |
  | Filer som inte synkroniseras | Antal filer som inte kan synkronisera | Server-slutpunkt |
  | Filer som har synkroniserats | Antal filer överförs (uppladdning och nedladdning) | Synkroniseringsgruppen, Server-slutpunkt |
  | Onlinestatus för Server | Antal pulsslag togs emot från servern | Registrerade servrar |

- Mer information finns i [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Diagrammen i den Lagringssynkroniseringstjänst portalen har ett tidsintervall på 24 timmar. Använd Azure Monitor för att visa olika tidsintervall eller dimensioner.

### <a name="azure-monitor"></a>Azure Monitor

Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) att övervaka sync, lagringsnivåer för moln och anslutning. Mätvärden för Azure File Sync är aktiverade som standard och skickas till Azure Monitor var 15: e minut.

Om du vill visa Azure File Sync-mått i Azure Monitor, Välj den **lagringstjänster för synkronisering** resurstyp.

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

Du kan visa molnlagringsnivåer, registrerad server och synkronisera hälsotillstånd på Windows Server.

### <a name="event-logs"></a>Händelseloggar

Använda händelseloggen telemetri på servern för att övervaka registrerad server, synkronisering och lagringsnivåer hälsotillstånd för moln. Telemetri händelseloggen finns i Loggboken under *program och Services\Microsoft\FileSync\Agent*.

Synkronisera health:

- Händelse-ID 9102 loggas när en synkroniseringssessionen är klar. Använd den här händelsen för att avgöra om synkroniseringssessioner har lyckats (**HResult = 0**) och om det finns per objekt synkroniseras fel. Mer information finns i den [synkronisera hälsotillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [fel per objekt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) dokumentation.

  > [!Note]  
  > Ibland synkroniseringssessioner misslyckas övergripande eller har en PerItemErrorCount inte är noll. Men de fortfarande göra vidare framsteg och vissa filer synkronisera på nytt. Du ser du i fälten tillämpade som AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes. De här fälten berättar hur mycket av sessionen är klar. Om du ser flera synkroniseringssessioner misslyckas i en rad, och de har ett ökande antal tillämpade, ge tid att försöka igen innan du öppnar ett supportärende.

- Händelse-ID 9302 loggas var 5-10 minuter om det finns en ActiveSync-session. Använd den här händelsen för att avgöra om den aktuella synkroniseringssessionen är framsteg (**AppliedItemCount > 0**). Om synkronisering inte framsteg, synkroniseringssessionen misslyckas till sist och kommer att loggas en händelse-ID 9102 med fel. Mer information finns i den [synkronisera förloppet dokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Registrerad server health:

- Händelse-ID 9301 loggas med 30 sekunders mellanrum när en server frågar tjänsten för jobb. Om GetNextJob är klar med **status = 0**, servern kan kommunicera med tjänsten. Om GetNextJob är klar med ett fel, kontrollera den [Felsökningsdokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) anvisningar.

Molnet lagringsnivåer hälsotillstånd:

- Om du vill övervaka lagringsnivåer på en server, använder du händelse-ID 9003 9016 och 9029 i händelseloggen telemetri som finns i Loggboken under *program och Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9003 innehåller felfördelningen för en serverslutpunkt. Exempel: Total Felräkning och felkod. En händelse loggas per felkod.
  - Händelse-ID 9016 innehåller eftersläpningar resultat för en volym. Exempel: Ledigt utrymme i procent är antalet filer som avbildad i sessionen, och antalet filer som misslyckades med att ghost.
  - Händelse-ID 9029 innehåller eftersläpningar sessionsinformation för en serverslutpunkt. Exempel: Antal filer i sessionen, antal filer nivåer i sessionen och antalet filer som redan har nivåindelade.
  
- Du övervakar återställningar på en server med händelse-ID 9005 9006, 9009 och 9059 i händelseloggen telemetri som finns i Loggboken under *program och Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9005 ger återkallande tillförlitligheten hos en serverslutpunkt. Exempel: Totalt antal unika filer som öppnas och Totalt antal unika filer med misslyckade åtkomst.
  - Händelse-ID 9006 ger återkallande felfördelningen för en serverslutpunkt. Exempel: Totalt antal misslyckade begäranden och felkod. En händelse loggas per felkod.
  - Händelse-ID 9009 ger återkallande sessionsinformation för en serverslutpunkt. Exempel: DurationSeconds, CountFilesRecallSucceeded, and CountFilesRecallFailed.
  - Händelse-ID 9059 ger återkallande av programdistribution för en serverslutpunkt. Exempel: ShareId, programnamn och TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Prestandaräknare

Använda Azure File Sync-prestandaräknare på servern för att övervaka synkroniseringsaktivitet.

Om du vill visa prestandaräknare för Azure File Sync på servern, öppnar du Prestandaövervakaren (Perfmon.exe). Du kan hitta räknare under den **AFS byte överförs** och **AFS synkroniseringsåtgärder** objekt.

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
