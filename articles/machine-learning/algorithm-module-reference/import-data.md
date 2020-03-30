---
title: 'Importera data: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Importera data i Azure Machine Learning för att läsa in data i en pipeline för maskininlärning från befintliga molndatatjänster.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456207"
---
# <a name="import-data-module"></a>Importera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att läsa in data i en machine learning pipeline från befintliga molndatatjänster. 

> [!Note]
> Alla funktioner som tillhandahålls av denna modul kan göras av **datalager** och **datauppsättningar** i worksapce målsidan. Vi rekommenderar att du använder **datalager** och **datauppsättning** som innehåller ytterligare funktioner som dataövervakning. Mer information finns i Artikeln [Om hur du kommer åt data](../how-to-access-data.md) och hur du registrerar [datauppsättningar.](../how-to-create-register-datasets.md)
> När du har registrerat en datauppsättning hittar du den i kategorin **Datauppsättningar** -> **Mina datauppsättningar** i designergränssnittet. Den här modulen är reserverad för Studio(klassiska) användare för en välbekant upplevelse. 
>

Modulen **Importdata** stöder lästa data från följande källor:

- URL via HTTP
- Azure molnlagringar via [**datalager**](../how-to-access-data.md))
    - Azure Blob-behållare
    - Azure-filresurs
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

Innan du använder molnlagring måste du registrera ett datalager på din Azure Machine Learning-arbetsyta först. Mer information finns i [Så här kommer du åt data](../how-to-access-data.md). 

När du har definierat de data som du vill ha och ansluter till källan härleder **[ImportData](./import-data.md)** datatypen för varje kolumn baserat på de värden den innehåller och läser in data i designerpipelinen. Utdata **från importdata** är en datauppsättning som kan användas med valfri designerpipeline.

Om källdata ändras kan du uppdatera datauppsättningen och lägga till nya data genom att köra [importera data igen](./import-data.md).

## <a name="how-to-configure-import-data"></a>Konfigurera importdata

1. Lägg till modulen **Importera data** i pipelinen. Du hittar den här modulen i kategorin **Datainmatning och utdata** i designern.

1. Välj den modul som ska öppnas i den högra rutan.

1. Välj **Datakälla**och välj datakälltyp. Det kan vara HTTP eller datastore.

    Om du väljer datalager kan du välja befintliga datacenter som redan är registrerade på din Azure Machine Learning-arbetsyta eller skapa ett nytt datalager. Definiera sedan sökvägen till data som ska importeras i datalagret. Du kan enkelt bläddra i sökvägen genom att klicka på **Bläddra sökväg** ![import-data-sökväg](media/module/import-data-path.png)

1. Markera förhandsgranskningsschemat om du vill filtrera de kolumner som du vill inkludera. Du kan också definiera avancerade inställningar som Avgränsare i tolkningsalternativ.

    ![importera-data-förhandsgranskning](media/module/import-data.png)

1. Kryssrutan, **Regenerate output**, avgör om modulen ska köras för att återskapa utdata vid drift. 

    Det är som standard omarkerat, vilket innebär att om modulen har körts med samma parametrar tidigare, kommer systemet att återanvända utdata från senaste körningen för att minska körningstiden. 

    Om det väljs kommer systemet att köra modulen igen för att återskapa utdata. Så välj det här alternativet när underliggande data i lagring uppdateras, kan det hjälpa till att få de senaste uppgifterna.


1. Skicka pipelinen.

    När Importdata läser in data i designern härleds datatypen för varje kolumn baserat på de värden den innehåller, antingen numeriska eller kategoriska.

    Om det finns ett huvud används huvudet för att namnge kolumnerna i utdatauppsättningen.

    Om det inte finns några befintliga kolumnrubriker i data genereras nya kolumnnamn med formatet col1, col2,... , coln*.

## <a name="results"></a>Resultat

När importen är klar klickar du på utdatauppsättningen och väljer **Visualisera** för att se om data har importerats.

Om du vill spara data för återanvändning i stället för att importera en ny uppsättning data varje gång pipelinen körs väljer du ikonen **Registrera datauppsättning** under fliken **Utdata** på modulens högra panel. Välj ett namn på datauppsättningen. Den sparade datauppsättningen bevarar data vid tidpunkten för sparandet, datauppsättningen uppdateras inte när pipelinen körs igen, även om datauppsättningen i pipelinen ändras. Detta kan vara användbart för att ta ögonblicksbilder av data.

När du har importerat data kan det behövas ytterligare förberedelser för modellering och analys:

- Använd [Redigera metadata](./edit-metadata.md) för att ändra kolumnnamn, för att hantera en kolumn som en annan datatyp eller för att ange att vissa kolumner är etiketter eller funktioner.

- Använd [Välj kolumner i datauppsättningen](./select-columns-in-dataset.md) för att välja en delmängd kolumner som ska omformas eller användas vid modellering. De omformade eller borttagna kolumnerna kan enkelt återanslutas till den ursprungliga datauppsättningen med hjälp av modulen [Lägg till kolumner.](./add-columns.md)  

- Använd [Partition och Exempel](./partition-and-sample.md) för att dela upp datauppsättningen, utföra sampling eller hämta de översta n raderna.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
