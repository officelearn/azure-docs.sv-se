---
title: Skapa händelsebaserade utlösare i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline som svar på en händelse.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347729"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Skapa en utlösare som kör en pipeline som svar på en händelse

Den här artikeln beskriver de händelsebaserade utlösare som du kan skapa i Data Factory-pipeliner.

Händelsedriven arkitektur (EDA) är ett vanligt integration mönster för data som inbegriper produktion, identifiering, förbrukning och reaktionen på händelser. Dataintegrering kräver ofta Data Factory-kunder som utlöser pipelines baserat på händelser. Data Factory är nu integrerat med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), där kan du utlöser pipelines på en händelse.

Titta på följande videoklipp för en tio minuter introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integrering som beskrivs i den här artikeln är beroende av [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Se till att prenumerationen är registrerad med Event Grid-resursprovidern. Mer information finns i [resursproviders och resurstyper](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

### <a name="create-a-new-event-trigger"></a>Skapa en ny händelseutlösare

En typisk händelse är till exempel ankomsten av en fil eller borttagningen av en fil i Azure Storage-kontot. Du kan skapa en utlösare som svarar på händelsen i din Data Factory-pipeline.

> [!NOTE]
> Den här integreringen stöder endast version 2 Storage-konton (Allmänt).

![Skapa en ny utlösare](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Konfigurera event-utlösare

Med den **blobbsökvägen börjar med** och **blobbsökvägen slutar med** egenskaper, kan du ange den behållare, mappar och blob-namnet som du vill ta emot händelser. Du kan använda olika mönster för både **blobbsökvägen börjar med** och **blobbsökvägen slutar med** egenskaper, som visas i exemplen senare i den här artikeln. Minst en av dessa egenskaper krävs.

![Konfigurera event-utlösare](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Välj typ av händelse-utlösare

När filen tas emot i din lagringsplats och motsvarande blob skapas den här händelsen utlöses och kör din Data Factory-pipeline. Du kan skapa en utlösare som svarar på en händelse för skapande av blob, en händelse för borttagning av blob eller båda händelser i Data Factory-pipeliner.

![Välj Utlösartyp som händelse](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mappa egenskaper för utlösare till pipeline-parametrar

När en händelseutlösare utlöses för en specifik blob händelsen fångar mappnamn för sökvägen och filnamnet för bloben till egenskaperna `@triggerBody().folderPath` och `@triggerBody().fileName`. Om du vill använda värdena för dessa egenskaper i en pipeline måste du mappa egenskaperna till pipeline-parametrar. När du mappar egenskaperna till parametrar, du kan komma åt de värden som avbildas av utlösaren via den `@pipeline().parameters.parameterName` uttryck i hela pipelinen.

![Mappa egenskaper till pipeline-parametrar](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Till exempel på föregående skärmbild. utlösaren har konfigurerats för att utlöses när en blobbsökväg som slutar på `.csv` skapas i Lagringskontot. Resultatet blir att, när en blob med det `.csv` tillägget skapas var som helst i Storage-konto i `folderPath` och `fileName` egenskaper avbilda platsen för den nya bloben. Till exempel `@triggerBody().folderPath` har ett värde som `/containername/foldername/nestedfoldername` och `@triggerBody().fileName` har ett värde som `filename.csv`. De här värdena mappas i exemplet till pipeline-parametrar `sourceFolder` och `sourceFile`. Du kan använda dem i hela pipelinen som `@pipeline().parameters.sourceFolder` och `@pipeline().parameters.sourceFile` respektive.

## <a name="json-schema"></a>JSON-schema

Följande tabell innehåller en översikt över de element som är relaterade till händelsebaserade utlösare:

| **JSON-Element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Krävs** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Omfång** | Resurs-ID för Azure Resource Manager för Lagringskontot. | String | Azure Resource Manager ID | Ja |
| **händelser** | Typ av händelser som orsakar den här utlösaren utlöses. | Matris    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Ja, valfri kombination av dessa värden. |
| **blobPathBeginsWith** | Blobbsökvägen måste börja med det angivna mönstret för utlösaren utlöses. Till exempel `/records/blobs/december/` bara utlöses utlösaren för blobbar i den `december` mapp under den `records` behållare. | String   | | Du måste ange ett värde för minst en av de här egenskaperna: `blobPathBeginsWith` eller `blobPathEndsWith`. |
| **blobPathEndsWith** | Blobbsökväg måste avslutas med det angivna mönstret för utlösaren utlöses. Till exempel `december/boxes.csv` bara utlöses utlösaren för BLOB-objekt med namnet `boxes` i en `december` mapp. | String   | | Du måste ange ett värde för minst en av de här egenskaperna: `blobPathBeginsWith` eller `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Exempel på händelsebaserade utlösare

Det här avsnittet innehåller exempel på inställningar för händelsebaserade utlösare.

> [!IMPORTANT]
> Du behöver ta den `/blobs/` segmentet i sökvägen som visas i följande exempel, när du anger behållaren och mappen, behållare och filen eller behållare, mapp och fil.

| Egenskap  | Exempel | Beskrivning |
|---|---|---|
| **Sökväg till BLOB börjar med** | `/containername/` | Tar emot händelser för alla blobar i behållaren. |
| **Sökväg till BLOB börjar med** | `/containername/blobs/foldername/` | Tar emot händelser för alla blobbar i den `containername` behållare och `foldername` mapp. |
| **Sökväg till BLOB börjar med** | `/containername/blobs/foldername/subfoldername/` | Du kan också referera till en undermapp. |
| **Sökväg till BLOB börjar med** | `/containername/blobs/foldername/file.txt` | Tar emot händelser för en blob med namnet `file.txt` i den `foldername` mapp under den `containername` behållare. |
| **Blobbsökväg slutar med** | `file.txt` | Tar emot händelser för en blob med namnet `file.txt` i valfri sökväg. |
| **Blobbsökväg slutar med** | `/containername/blobs/file.txt` | Tar emot händelser för en blob med namnet `file.txt` under behållaren `containername`. |
| **Blobbsökväg slutar med** | `foldername/file.txt` | Tar emot händelser för en blob med namnet `file.txt` i `foldername` mapp under någon behållare. |

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
