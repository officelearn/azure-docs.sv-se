---
title: Skapa händelsebaserade utlösare i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline som svar på en händelse.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443929"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Skapa en utlösare som kör en pipeline som svar på en händelse

I den här artikeln beskrivs de händelsebaserade utlösare som du kan skapa i datafabrikens pipelines.

Händelsedriven arkitektur (EDA) är ett vanligt dataintegrationsmönster som omfattar produktion, identifiering, förbrukning och reaktion på händelser. Dataintegrationsscenarier kräver ofta att Data Factory-kunder utlöser pipelines baserat på händelser som ankomst eller borttagning av en fil i ditt Azure Storage-konto. Data Factory är nu integrerat med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), som låter dig utlösa pipelines på en händelse.

För en tio minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integreringen som beskrivs i den här artikeln beror på [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Kontrollera att din prenumeration är registrerad hos resursleverantören Event Grid. Mer information finns i [Resursleverantörer och typer](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

I det här avsnittet visas hur du skapar en händelseutlösare i Azure Data Factory User Interface.

1. Gå till **redigeringsarbetsytan**

1. I det nedre vänstra hörnet klickar du på knappen **Utlösare**

1. Klicka på **+ Ny** som öppnar skapa utlösa sidoskepp

1. Välj utlösartyp **Händelse**

    ![Skapa ny händelseutlösare](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Välj ditt lagringskonto i listrutan Azure-prenumeration eller manuellt med hjälp av dess lagringskontoresurs-ID. Välj vilken behållare du vill att händelserna ska inträffa på. Val av behållare är valfritt, men tänk på att om du väljer alla behållare kan det leda till ett stort antal händelser.

   > [!NOTE]
   > Händelseutlösaren stöder för närvarande endast Azure Data Lake Storage Gen2 och General-Purpose version 2-lagringskonton. På grund av en Begränsning av Azure Event Grid stöder Azure Data Factory endast högst 500 händelseutlösare per lagringskonto.

1. **Blob-sökvägen börjar med** och **Blob-sökvägen slutar med** egenskaper som gör att du kan ange behållare, mappar och blob-namn som du vill ta emot händelser för. Händelseutlösaren kräver att minst en av dessa egenskaper har definierats. Du kan använda olika mönster för både **Blob-sökvägen börjar med** och **Blob-sökvägen slutar med** egenskaper, som visas i exemplen senare i den här artikeln.

    * **Blob-sökvägen börjar med:** Blob-sökvägen måste börja med en mappsökväg. Giltiga värden `2018/` `2018/april/shoes.csv`inkluderar och . Det går inte att markera det här fältet om en behållare inte är markerad.
    * **Blob-banan avslutas med:** Blob-sökvägen måste sluta med ett filnamn eller filtillägg. Giltiga värden `shoes.csv` `.csv`inkluderar och . Behållar- och mappnamn är valfria, men när `/blobs/` det anges måste de avgränsas med ett segment. En behållare med namnet "order" kan `/orders/blobs/2018/april/shoes.csv`till exempel ha värdet . Om du vill ange en mapp i en behållare utelämnar du det inledande /-tecknet. `april/shoes.csv` Utlöser till exempel en händelse `shoes.csv` på alla filer som namnges i mappen och som kallas april i valfri behållare. 

1. Välj om utlösaren ska svara på en **Blob skapad** händelse, **Blob-borttagen** händelse eller både och. På den angivna lagringsplatsen utlöser varje händelse datafabrikens pipelines som är associerade med utlösaren.

    ![Konfigurera händelseutlösaren](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Välj om utlösaren ska ignorera blobbar med noll byte.

1. När du har konfigurerat utlösaren klickar du på **Nästa: Förhandsgranskning av data**. På den här skärmen visas de befintliga blobbar som matchas av konfigurationen av händelseutlösaren. Kontrollera att du har specifika filter. Konfigurera filter som är för breda kan matcha ett stort antal filer som skapats/tagits bort och kan påverka kostnaden avsevärt. När filtervillkoren har verifierats klickar du på **Slutför**.

    ![Förhandsgranskning av händelseutlösaredata](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Om du vill koppla en pipeline till den här utlösaren går du till pipeline-arbetsytan och klickar på **Lägg till utlösare** och väljer **Ny/Redigera**. När sidoskeppet visas klickar du på listrutan **Välj utlösare och** väljer den utlösare du skapade. Klicka på **Nästa: Förhandsgranskning** av data för att bekräfta att konfigurationen är korrekt och sedan **nästa för** att verifiera att förhandsgranskningen av data är korrekt.

1. Om pipelinen har parametrar kan du ange dem på parameterns sidonavigeringsnaviger. Händelseutlösaren fångar mappsökvägen och filnamnet för `@triggerBody().folderPath` bloben i egenskaperna och `@triggerBody().fileName`. Om du vill använda värdena för dessa egenskaper i en pipeline måste du mappa egenskaperna till pipelineparametrar. När du har mappat egenskaperna till parametrar kan du `@pipeline().parameters.parameterName` komma åt de värden som fångas upp av utlösaren via uttrycket i hela pipelinen. Klicka på **Slutför** när du är klar.

    ![Mappa egenskaper till pipelineparametrar](media/how-to-create-event-trigger/event-based-trigger-image4.png)

I föregående exempel konfigureras utlösaren att aktiveras när en blob-sökväg som slutar på CSV skapas i mapphändelsetestning i behållarexempeldata. Egenskaperna **folderPath** och **fileName** fångar platsen för den nya bloben. När MoviesDB.csv till exempel läggs till i `@triggerBody().folderPath` sökvägen exempel-data/händelsetestning, har ett värde på `sample-data/event-testing` och `@triggerBody().fileName` har värdet . `moviesDB.csv` Dessa värden mappas i exemplet `sourceFolder` till `sourceFile` pipelineparametrarna och `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` som kan användas i hela pipelinen som respektive.

## <a name="json-schema"></a>JSON-schema

I följande tabell finns en översikt över de schemaelement som är relaterade till händelsebaserade utlösare:

| **JSON-element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Obligatoriskt** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Omfattning** | Azure Resource Manager-resurs-ID för lagringskontot. | String | Azure Resource Manager ID | Ja |
| **Händelser** | Den typ av händelser som orsakar denna utlösare att skjuta. | Matris    | Microsoft.Storage.BlobSkaperad, Microsoft.Storage.BlobDeleted | Ja, en kombination av dessa värden. |
| **blobPathBeginsWith** | Blob-banan måste börja med det mönster som tillhandahålls för att utlösaren ska avfyras. Till exempel `/records/blobs/december/` avfyrar endast utlösaren för `december` blobbar i mappen under behållaren. `records` | String   | | Du måste ange ett värde för minst `blobPathBeginsWith` en `blobPathEndsWith`av dessa egenskaper: eller . |
| **blobPathEndsWith** | Blob-banan måste sluta med det mönster som anges för att utlösaren ska avfyras. Till exempel `december/boxes.csv` avfyrar endast utlösaren `boxes` för `december` blobbar som namnges i en mapp. | String   | | Du måste ange ett värde för minst `blobPathBeginsWith` en `blobPathEndsWith`av dessa egenskaper: eller . |
| **ignoreraEmptyBlobs** | Om blobbar med noll byte utlöses eller inte. Som standard är detta inställt på true. | Boolean | sant eller falskt | Inga |

## <a name="examples-of-event-based-triggers"></a>Exempel på händelsebaserade utlösare

Det här avsnittet innehåller exempel på händelsebaserade utlösarinställningar.

> [!IMPORTANT]
> Du måste inkludera `/blobs/` segmentet för sökvägen, som visas i följande exempel, när du anger behållare och mapp, behållare och fil eller behållare, mapp och fil. För **blobPathBeginsWith**lägger datafabriksgränssnittet `/blobs/` automatiskt mellan mappen och behållarnamnet i utlösaren JSON.

| Egenskap | Exempel | Beskrivning |
|---|---|---|
| **Blob-sökvägen börjar med** | `/containername/` | Tar emot händelser för alla blob i behållaren. |
| **Blob-sökvägen börjar med** | `/containername/blobs/foldername/` | Tar emot händelser för alla `containername` blobbar i behållaren och `foldername` mappen. |
| **Blob-sökvägen börjar med** | `/containername/blobs/foldername/subfoldername/` | Du kan också referera till en undermapp. |
| **Blob-sökvägen börjar med** | `/containername/blobs/foldername/file.txt` | Tar emot händelser för `file.txt` en `foldername` blob `containername` som namnges i mappen under behållaren. |
| **Blob-banan slutar med** | `file.txt` | Tar emot händelser för `file.txt` en blob som namnges i valfri sökväg. |
| **Blob-banan slutar med** | `/containername/blobs/file.txt` | Tar emot händelser för `file.txt` en `containername`blob som namnges under behållaren . |
| **Blob-banan slutar med** | `foldername/file.txt` | Tar emot händelser för `file.txt` `foldername` en blob som namnges i mappen under valfri behållare. |

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns i [Pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
