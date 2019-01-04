---
title: Använd Azure Database Migration Service för att övervaka migreringsaktiviteten | Microsoft Docs
description: Lär dig att använda Azure Database Migration Service för att övervaka migreringsaktiviteter.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/14/2018
ms.openlocfilehash: a7fc48c149d488ab7860513b617b8a5806b1feb6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721163"
---
# <a name="monitor-migration-activity"></a>Övervaka migreringsaktiviteter
I den här artikeln får lära du att övervaka förloppet för en migrering på både en databasnivå och en tabell.

## <a name="monitor-at-the-database-level"></a>Övervaka på databasnivå
Visa bladet på databasnivå för att övervaka aktiviteten på databasnivå:

![Bladet på databasnivå](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Att välja databasen hyperlänken visar listan över tabeller och deras migreringens förlopp.

I följande tabell visar en lista över fält på bladet på databasnivå och beskriver olika statusvärdena som är associerade med varje.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Fältnamn</strong></th>
      <th><strong>Fältet understatus</strong></th>
      <th><strong>Beskrivning</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Aktivitetsstatus</strong></td>
      <td>Körs</td>
      <td>Migreringsaktiviteter körs.</td>
    </tr>
    <tr>
      <td>Lyckades</td>
      <td>Migreringen har slutförts utan problem.</td>
    </tr>
    <tr>
      <td>Fel</td>
      <td>Migreringen misslyckades. Välj länken ”se felinformationen' under information om migrering för fullständig felmeddelande.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Initieras</td>
      <td>DMS är konfiguration av pipelinen för migrering.</td>
    </tr>
    <tr>
      <td>Körs</td>
      <td>DMS-pipeline som körs och utför migrering.</td>
    </tr>
    <tr>
      <td>Slutför</td>
      <td>Migreringen har slutförts.</td>
    </tr>
    <tr>
      <td>Misslyckad</td>
      <td>Migreringen misslyckades. Klicka på information om migrering att se migreringsfel.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Information om migrering</strong></td>
      <td>Initiera pipeline för migrering</td>
      <td>DMS är konfiguration av pipelinen för migrering.</td>
    </tr>
    <tr>
      <td>Fullständig datainläsning pågår</td>
      <td>DMS utför första.</td>
    </tr>
    <tr>
      <td>Redo för startpunkt</td>
      <td>När den inledande inläsningen har slutförts markerar DMS databasen redo för snabb lösning. Användare bör kontrollera om data har fått allt på kontinuerlig synkronisering.</td>
    </tr>
    <tr>
      <td>Alla ändringar har tillämpats</td>
      <td>Första och kontinuerlig synkronisering har slutförts. Den här statusen visas också när databasen är har snabb lösning.</td>
    </tr>
    <tr>
      <td>Se felinformationen</td>
      <td>Klicka på länken för att visa felinformation.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Varaktighet</strong></td>
      <td>Gäller inte</td>
      <td>Total tid från migreringsaktiviteter som håller på att initieras för migreringen har slutförts eller faulted migrering.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Övervaka på tabellen nivå – Snabbsammanfattning
Visa bladet tabell på servernivå för att övervaka aktiviteten på tabellnivå. Den övre delen av bladet visar detaljerad antalet rader som ska migreras i full belastning och inkrementella uppdateringar. 

Den nedre delen av bladet listar tabellerna och visar en snabb översikt över migreringens förlopp.

![Tabellen på servernivå bladet – snabb sammanfattning](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

I följande tabell beskrivs de fält som visas i tabellen på servernivå information.

| Fältnamn        | Beskrivning       |
| ------------- | ------------- |
| **Fullständig inläsning slutförd**      | Antal tabeller slutföra fullständiga datainläsningen. |
| **Fullständig inläsning köad**      | Antalet tabeller som ställts i kö för full belastning.      |
| **Fullständig inläsning pågår** | Det gick inte att antal tabeller.      |
| **Inkrementella uppdateringar**      | Antal sammanställning capture (CDC) uppdateringar i rader som tillämpas på målet. |
| **Inkrementell infogningar**      | Antal CDC infogar i rader som tillämpas på målet.      |
| **Inkrementell borttagningar** | Antal CDC tar bort i rader som tillämpas på målet.      |
| **Väntande ändringar**      | Antal CDC i rader som väntar fortfarande på att tillämpas till målet. |
| **Tillämpade ändringar**      | Totalt CDC uppdateringar, infogningar, och tar bort i rader som tillämpas på målet.      |
| **Tabellerna i feltillstånd** | Antalet tabeller som har statusen ”fel” under migreringen. Det är några exempel som tabeller kan gå till feltillstånd när det finns dubbletter som identifieras i mål- eller data kan inte användas läser in i måltabellen.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Övervaka på tabellen nivå – detaljerad sammanfattning
Det finns två flikar som visar migreringens förlopp i Full belastning och inkrementell datasynkronisering.
    
![Fullständig inläsning fliken](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Inkrementella data sync-fliken](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

I följande tabell beskrivs de fält som visas i tabellen på migreringens förlopp.

| Fältnamn        | Beskrivning       |
| ------------- | ------------- |
| **Status - synkronisering**      | Kontinuerlig synkronisering körs. |
| **Infoga**      | Antal CDC infogar i rader som tillämpas på målet.      |
| **Uppdatering** | Antalet CDC-uppdateringar i rader som tillämpas på målet.      |
| **Ta bort**      | Antal CDC tar bort i rader som tillämpas på målet. |
| **Totalt antal tillämpade**      | Totalt CDC uppdateringar, infogningar, och tar bort i rader som tillämpas på målet. |
| **Datafel** | Antal datafel inträffade i den här tabellen. Några exempel på felen är *511: Det går inte att skapa en rad med storleken %d som är större än den maximalt tillåtna Radstorleken av %d, 8114: Fel vid datatyp konvertering %ls till %ls.*  Kunden ska fråga från dms_apply_exceptions tabell i Azure-målet på se felinformationen.    |

> [!NOTE]
> CDC-värdena för att infoga, uppdatera och ta bort och totala tillämpas kan minska när databasen är startpunkt eller migrering har startats om.

## <a name="next-steps"></a>Nästa steg
- Granska migreringsvägledningen i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).