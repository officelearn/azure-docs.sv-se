---
title: Övervaka Synkronisering av Azure-filer | Microsoft-dokument
description: Så här övervakar du Synkronisering av Azure-filer.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699293"
---
# <a name="monitor-azure-file-sync"></a>Övervaka Azure File Sync

Använd Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver bevaras. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cachar som du behöver över hela världen.

I den här artikeln beskrivs hur du övervakar distributionen av Azure File Sync med hjälp av Azure Monitor, Storage Sync Service och Windows Server.

Följande övervakningsalternativ är för närvarande tillgängliga.

## <a name="azure-monitor"></a>Azure Monitor

Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för att visa mått och konfigurera aviseringar för synkronisering, molnnivåhantering och serveranslutning.  

### <a name="metrics"></a>Mått

Mått för Azure File Sync är aktiverade som standard och skickas till Azure Monitor var 15:e minut.

Om du vill visa Azure File Sync-mått i Azure Monitor väljer du resurstypen **Storage Sync Services.**

Följande mått för Azure File Sync är tillgängliga i Azure Monitor:

| Måttnamn | Beskrivning |
|-|-|
| Synkroniserade byte | Storleken på överförda data (ladda upp och ladda ner).<br><br>Enhet: Byte<br>Aggregeringstyp: Summa<br>Tillämpliga dimensioner: Serverslutpunktsnamn, synkroniseringsriktning, synkroniseringsgruppnamn |
| Återkalla molnnivådelning | Storleken på data som återkallas.<br><br>**Det**här måttet tas bort i framtiden. Använd måttet för återkallad storlek för molnnivå för att övervaka storleken på data som återkallas.<br><br>Enhet: Byte<br>Aggregeringstyp: Summa<br>Tillämplig dimension: Servernamn |
| Återkallad storlek för molnnivå | Storleken på data som återkallas.<br><br>Enhet: Byte<br>Aggregeringstyp: Summa<br>Tillämplig dimension: Servernamn, namn på synkroniseringsgrupp |
| Återkallar storlek för molnnivå efter program | Storleken på data som återkallas av programmet.<br><br>Enhet: Byte<br>Aggregeringstyp: Summa<br>Tillämplig dimension: Programnamn, Servernamn, Synkroniseringsgruppnamn |
| Dataflöde för återkallar molnnivåer | Storlek på dataåterkallningsdataflöde.<br><br>Enhet: Byte<br>Aggregeringstyp: Summa<br>Tillämplig dimension: Servernamn, namn på synkroniseringsgrupp |
| Filer som inte synkroniseras | Antal filer som inte synkroniseras.<br><br>Enhet: Antal<br>Aggregeringstyp: Summa<br>Tillämpliga dimensioner: Serverslutpunktsnamn, synkroniseringsriktning, synkroniseringsgruppnamn |
| Synkroniserade filer | Antal överförda filer (ladda upp och ladda ned).<br><br>Enhet: Antal<br>Aggregeringstyp: Summa<br>Tillämpliga dimensioner: Serverslutpunktsnamn, synkroniseringsriktning, synkroniseringsgruppnamn |
| Onlinestatus för servern | Antal pulsslag som tagits emot från servern.<br><br>Enhet: Antal<br>Aggregeringstyp: Maximal<br>Tillämplig dimension: Servernamn |
| Resultatet av synkroniseringssessionen | Synkroniseringssessionsresultat (1=lyckad synkroniseringssession; 0=misslyckad synkroniseringssession)<br><br>Enhet: Antal<br>Aggregeringstyper: Maximalt<br>Tillämpliga dimensioner: Serverslutpunktsnamn, synkroniseringsriktning, synkroniseringsgruppnamn |

### <a name="alerts"></a>Aviseringar

Om du vill konfigurera aviseringar i Azure Monitor väljer du tjänsten Storage Sync och väljer sedan [azure file sync-måttet](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) som ska användas för aviseringen.  

I följande tabell visas några exempelscenarier som ska övervakas och rätt mått som ska användas för aviseringen:

| Scenario | Mått som ska användas för avisering |
|-|-|
| Serverslutpunktshälsa i portalen = Fel | Resultatet av synkroniseringssessionen |
| Filer synkroniseras inte med en server- eller molnslutpunkt | Filer som inte synkroniseras |
| Registrerad server kan inte kommunicera med storage sync-tjänsten | Onlinestatus för servern |
| Återkallar storlek på molnnivå har överskridit 500GiB på en dag  | Återkallad storlek för molnnivå |

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns [i Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Tjänst för synkronisering av lagring

Om du vill visa registrerad serverhälsa, serverslutpunktshälsa och mått går du till storage sync-tjänsten i Azure-portalen. Du kan visa registrerad serverhälsa i bladet **Registrerade servrar** och serverslutpunktshälsa i bladet **Synkroniseringsgrupper.**

### <a name="registered-server-health"></a>Serverns hälsa har registrerats

- Om tillståndet **registrerad server** är **Online**kommunicerar servern med tjänsten.
- Om tillståndet **Registrerad server** är **Visas offline**kontrollerar du att processen Storage Sync Monitor (AzureStorageSyncMonitor.exe) på servern körs. Om servern ligger bakom en brandvägg eller proxy läser du den [här artikeln](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) för att konfigurera brandväggen och proxyn.

### <a name="server-endpoint-health"></a>Hälsa för serverslutpunkt

- Serverslutpunktshälsan i portalen baseras på synkroniseringshändelser som loggas i telemetrihändelseloggen på servern (ID 9102 och 9302). Om en synkroniseringssession misslyckas på grund av ett tillfälligt fel, till exempel fel som har avbrutits, kan synkroniseringen fortfarande visas felfritt i portalen så länge den aktuella synkroniseringssessionen gör framsteg. Händelse-ID 9302 används för att avgöra om filer används. Mer information finns i [synkroniseringshälsa](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [synkroniseringsstatus](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Om portalen visar ett synkroniseringsfel eftersom synkronisering inte gör framsteg läser du [felsökningsdokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) för vägledning.

### <a name="metric-charts"></a>Måttdiagram

- Följande måttdiagram kan visas i Storage Sync Service-portalen:

  | Måttnamn | Beskrivning | Bladnamn |
  |-|-|-|
  | Synkroniserade byte | Storleken på överförda data (ladda upp och ladda ner) | Synkroniseringsgrupp, Serverslutpunkt |
  | Återkalla molnnivådelning | Storleken på återkallade data | Registrerade servrar |
  | Filer som inte synkroniseras | Antal filer som inte synkroniseras | Serverslutpunkt |
  | Synkroniserade filer | Antal överförda filer (ladda upp och ladda ned) | Synkroniseringsgrupp, Serverslutpunkt |
  | Onlinestatus för servern | Antal pulsslag som tagits emot från servern | Registrerade servrar |

- Mer information finns i [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Diagrammen i Storage Sync Service-portalen har ett tidsintervall på 24 timmar. Om du vill visa olika tidsintervall eller dimensioner använder du Azure Monitor.

## <a name="windows-server"></a>Windows Server

På Windows Server kan du visa molnnivåindelning, registrerad server och synkroniseringshälsa.

### <a name="event-logs"></a>Händelseloggar

Använd telemetrihändelseloggen på servern för att övervaka registrerad server-, synkroniserings- och molnnivådelningshälsa. Händelseloggen för telemetri finns i Loggboken under *Applications and Services\Microsoft\FileSync\Agent*.

Synkronisera hälsa:

- Händelse-ID 9102 loggas när en synkroniseringssession har slutförts. Använd den här händelsen för att avgöra om synkroniseringssessioner lyckas (**HResult = 0**) och om det finns synkroniseringsfel per objekt. Mer information finns i dokumentationen [för synkroniseringshälsa](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [fel per artikel.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Ibland misslyckas synkroniseringssessioner totalt sett eller har en peritemErrrcount som inte är noll. De gör dock fortfarande framsteg framåt och vissa filer synkroniseras. Du kan se detta i fälten Tillämpad, till exempel AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes. De här fälten visar hur mycket av sessionen som lyckades. Om flera synkroniseringssessioner misslyckas i en rad och de har ett ökat antal tillämpade, ger du synkroniseringstid för att försöka igen innan du öppnar en supportbiljett.

- Händelse-ID 9302 loggas var 5:e till 10:e minut om det finns en aktiv synkroniseringssession. Använd den här händelsen för att avgöra om den aktuella synkroniseringssessionen gör framsteg (**AppliedItemCount > 0**). Om synkronisering inte gör framsteg, bör synkroniseringssessionen så småningom misslyckas och ett händelse-ID 9102 loggas med felet. Mer information finns i [dokumentationen för synkroniseringsstatus](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Registrerad serverhälsa:

- Händelse-ID 9301 loggas var 30:e sekund när en server frågar tjänsten efter jobb. Om GetNextJob slutar med **status = 0**kan servern kommunicera med tjänsten. Om GetNextJob avslutar med ett fel kan du läsa [felsökningsdokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) för vägledning.

Hälsotillstånd för molnnivådelning:

- Om du vill övervaka nivåindelningsaktivitet på en server använder du Händelse-ID 9003, 9016 och 9029 i händelseloggen Telemetri, som finns i Loggboken under *Applications and Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9003 ger feldistribution för en serverslutpunkt. Till exempel: Totalt antal fel och ErrorCode. En händelse loggas per felkod.
  - Händelse-ID 9016 ger spökresultat för en volym. Till exempel: Ledigt utrymme procent är, Antal filer spökat i session, och Antal filer misslyckades att spöke.
  - Händelse-ID 9029 innehåller spöksessionsinformation för en serverslutpunkt. Till exempel: Antal filer som försöks i sessionen, Antal filer som nivåindelad i sessionen och Antal filer som redan har nivåindelats.
  
- Om du vill övervaka återkallningsaktivitet på en server använder du Händelse-ID 9005, 9006, 9009 och 9059 i telemetrihändelseloggen, som finns i Loggboken under *Applications and Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9005 ger återkallningstillförlitlighet för en serverslutpunkt. Till exempel: Totalt antal unika filer som används och Totalt unika filer med misslyckad åtkomst.
  - Händelse-ID 9006 ger distribution av återkallningsfel för en serverslutpunkt. Till exempel: Totalt antal misslyckade begäranden och ErrorCode. En händelse loggas per felkod.
  - Händelse-ID 9009 innehåller information om återkallningssession för en serverslutpunkt. Till exempel: DurationSeconds, CountFilesRecallSucceeded och CountFilesRecallFailed.
  - Händelse-ID 9059 ger programåterkallningsdistribution för en serverslutpunkt. Till exempel: ShareId, Programnamn och TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Prestandaräknare

Använd prestandaräknarna för Azure File Sync på servern för att övervaka synkroniseringsaktivitet.

Om du vill visa prestandaräknare för Azure File Sync på servern öppnar du Prestandaövervakaren (Perfmon.exe). Du hittar räknarna under **afs-byte som överförts** och **AFS Sync** Operations-objekt.

Följande prestandaräknare för Azure File Sync är tillgängliga i Prestandaövervakaren:

| Namn på prestandaobjekt\räknare | Beskrivning |
|-|-|
| AFS-överförda byte\Hämtade byte/sek | Antal nedladdade byte per sekund. |
| ÖVERFÖRDA AFS-byte\Överförda byte/sek | Antal uppladdade byte per sekund. |
| ÖVERFÖRDA AFS-byte\Totalt antal byte/sek | Totalt antal byte per sekund (ladda upp och ladda ned). |
| AFS-synkroniseringsåtgärder\Hämtade synkroniseringsfiler/sek | Antal filer som hämtats per sekund. |
| AFS-synkroniseringsåtgärder\Uppladdade synkroniseringsfiler/sek | Antal filer som laddats upp per sekund. |
| AFS-synkroniseringsoperationer\Totalt antal synkroniseringsfiloperationer/sek | Totalt antal synkroniserade filer (ladda upp och ladda ner). |

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Överväg brandväggs- och proxyinställningar](storage-sync-files-firewall-and-proxy.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)
- [Vanliga frågor och svar i Azure-filer](storage-files-faq.md)
