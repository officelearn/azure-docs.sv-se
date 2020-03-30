---
title: Övervaka migreringsaktivitet - Azure Database Migration Service
description: Lär dig att använda Azure Database Migration Service för att övervaka migreringsaktivitet.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648520"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Övervaka migreringsaktivitet med hjälp av Tjänsten för migrering av Azure Database
I den här artikeln får du lära dig hur du övervakar förloppet för en migrering på både databasnivå och tabellnivå.

## <a name="monitor-at-the-database-level"></a>Övervaka på databasnivå
Om du vill övervaka aktiviteten på databasnivå visar du bladet på databasnivå:

![Blad på databasnivå](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Om du väljer databashyperlänken visas listan över tabeller och deras migreringsförlopp.

I följande tabell visas fälten på bladet på databasnivå och de olika statusvärden som är associerade med var och en beskrivs.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Fältnamn</strong></th>
      <th><strong>Understatus för fält</strong></th>
      <th><strong>Beskrivning</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Aktivitetsstatus</strong></td>
      <td>Körs</td>
      <td>Migreringsaktiviteten pågår.</td>
    </tr>
    <tr>
      <td>Lyckades</td>
      <td>Migreringsaktiviteten lyckades utan problem.</td>
    </tr>
    <tr>
      <td>Fel</td>
      <td>Migreringen misslyckades. Välj länken "Se felinformation" under migreringsinformation för det fullständiga felmeddelandet.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Initierar</td>
      <td>DMS ställer in migreringspipelinen.</td>
    </tr>
    <tr>
      <td>Körs</td>
      <td>DMS-pipelinen körs och utför migroreringen.</td>
    </tr>
    <tr>
      <td>Slutför</td>
      <td>Migreringen har slutförts.</td>
    </tr>
    <tr>
      <td>Misslyckades</td>
      <td>Migreringen misslyckades. Klicka på migreringsinformation för att se migreringsfel.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Information om migrering</strong></td>
      <td>Initiera migreringspipelinen</td>
      <td>DMS ställer in migreringspipelinen.</td>
    </tr>
    <tr>
      <td>Fullständig databelastning pågår</td>
      <td>DMS utför den första belastningen.</td>
    </tr>
    <tr>
      <td>Redo för Cutover</td>
      <td>När den första belastningen är klar markerar DMS databasen som klar för cutover. Användaren bör kontrollera om data har hunnit ikapp på kontinuerlig synkronisering.</td>
    </tr>
    <tr>
      <td>Alla ändringar tillämpas</td>
      <td>Inledande belastning och kontinuerlig synkronisering är klara. Den här statusen inträffar också när databasen har överrullning.</td>
    </tr>
    <tr>
      <td>Se felinformation</td>
      <td>Klicka på länken för att visa felinformation.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Varaktighet</strong></td>
      <td>Ej tillämpligt</td>
      <td>Total tid från migreringsaktivitet som initierades till migreringen har slutförts eller migreringsfel.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Övervaka på tabellnivå – Snabbsammanfattning
Om du vill övervaka aktiviteten på tabellnivå visar du bladet på tabellnivå. Den övre delen av bladet visar det detaljerade antalet rader som migrerats i full belastning och inkrementella uppdateringar. 

Den nedre delen av bladet visar tabellerna och visar en snabb sammanfattning av migreringsförloppet.

![Bladet på bordsnivå - snabb sammanfattning](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

I följande tabell beskrivs de fält som visas i information på tabellnivå.

| Fältnamn        | Beskrivning       |
| ------------- | ------------- |
| **Full belastning har slutförts**      | Antalet tabeller har slutfört fullständig datainläsning. |
| **Fullständig belastning i kö**      | Antal tabeller som köas för full belastning.      |
| **Full lastning** | Antalet tabeller misslyckades.      |
| **Inkrementella uppdateringar**      | Antal uppdateringar av ändringsdata (CDC) i rader som tillämpas på målet. |
| **Inkrementella skär**      | Antal CDC-infogningar i rader som tillämpas på målet.      |
| **Inkrementella borttagningar** | Antal CDC-borttagningar i rader som tillämpas på målet.      |
| **Väntande ändringar**      | Antal CDC i rader som fortfarande väntar på att få tillämpas på målet. |
| **Tillämpade ändringar**      | Totalt antal CDC-uppdateringar, infogningar och borttagningar i rader som tillämpas på målet.      |
| **Tabeller i feltillstånd** | Antal tabeller som är i feltillstånd under migreringen. Några exempel som tabeller kan gå in i feltillstånd är när det finns dubbletter som identifieras i målet eller data inte är kompatibla inläsning i måltabellen.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Övervaka på tabellnivå – Detaljerad sammanfattning
Det finns två flikar som visar migreringsstatus i fullständig inläsning och inkrementell datasynkronisering.
    
![Fliken Fullständig inläsning](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Fliken Inkryssning av data](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

I följande tabell beskrivs de fält som visas i migreringsstatus på tabellnivå.

| Fältnamn        | Beskrivning       |
| ------------- | ------------- |
| **Status - Synkronisering**      | Kontinuerlig synkronisering körs. |
| **Infoga**      | Antal CDC-infogningar i rader som tillämpas på målet.      |
| **Uppdatering** | Antal CDC-uppdateringar i rader som tillämpas på målet.      |
| **Ta bort**      | Antal CDC-borttagningar i rader som tillämpas på målet. |
| **Totalt tillämpat**      | Totalt antal CDC-uppdateringar, infogningar och borttagningar i rader som tillämpas på målet. |
| **Datafel** | Antal datafel som inträffat i den här tabellen. Några exempel på felen är *511: Det går inte att skapa en rad med storlek %d som är större än den tillåtna maximala radstorleken %d, 8114: Fel uppstod om du inte konverterar datatyp %ls till %ls.*  Kunden bör fråga från dms_apply_exceptions tabellen i Azure-målet för att se felinformationen.    |

> [!NOTE]
> CDC-värden för Infoga, Uppdatera och Ta bort och Totalt tillämpad kan minska när databasen är överskärning eller migrering startas om.

## <a name="next-steps"></a>Nästa steg
- Läs migreringsvägledningen i [Migreringsguiden för](https://datamigration.microsoft.com/)Microsoft Database .