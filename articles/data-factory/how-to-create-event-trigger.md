---
title: Skapa händelsebaserade utlösare i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline som svar på en händelse.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: douglasl
ms.openlocfilehash: 53ea7425f0497eca7c95ddefeaa09aa40259672b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216284"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Skapa en utlösare som kör en pipeline som svar på en händelse

Den här artikeln beskriver de händelsebaserade utlösare som du kan skapa i Data Factory-pipeliner.

Händelsedriven arkitektur (EDA) är ett vanligt integration mönster för data som inbegriper produktion, identifiering, förbrukning och reaktionen på händelser. Dataintegrering kräver ofta Data Factory-kunder som utlöser pipelines baserat på händelser. Data Factory är nu integrerat med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), där kan du utlöser pipelines på en händelse.

Titta på följande videoklipp för en tio minuter introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integrering som beskrivs i den här artikeln är beroende av [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Se till att prenumerationen är registrerad med Event Grid-resursprovidern. Mer information finns i [resursproviders och resurstyper](../azure-resource-manager/resource-manager-supported-services.md#portal).

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

När en händelseutlösare utlöses för en specifik blob händelsen fångar mappnamn för sökvägen och filnamnet för bloben till egenskaperna `@triggerBody().folderPath` och `@triggerBody().fileName`. Om du vill använda värdena för dessa egenskaper i en pipeline måste du mappa egenskaperna till pipeline-parametrar. När du mappar egenskaperna till parametrar, du kan komma åt de värden som avbildas av utlösaren via den `@pipeline.parameters.parameterName` uttryck i hela pipelinen.

![Mappa egenskaper till pipeline-parametrar](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Till exempel på föregående skärmbild. utlösaren har konfigurerats för att utlöses när en blobbsökväg som slutar på `.csv` skapas i Lagringskontot. Resultatet blir att, när en blob med det `.csv` tillägget skapas var som helst i Storage-konto i `folderPath` och `fileName` egenskaper avbilda platsen för den nya bloben. Till exempel `@triggerBody().folderPath` har ett värde som `/containername/foldername/nestedfoldername` och `@triggerBody().fileName` har ett värde som `filename.csv`. De här värdena mappas i exemplet till pipeline-parametrar `sourceFolder` och `sourceFile`. Du kan använda dem i hela pipelinen som `@pipeline.parameters.sourceFolder` och `@pipeline.parameters.sourceFile` respektive.

## <a name="json-schema"></a>JSON-schema

Följande tabell innehåller en översikt över de element som är relaterade till händelsebaserade utlösare:

| **JSON-Element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Krävs** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Omfång** | Resurs-ID för Azure Resource Manager för Lagringskontot. | Sträng | Azure Resource Manager-ID | Ja |
| **händelser** | Typ av händelser som orsakar den här utlösaren utlöses. | Matris    | Microsoft.Storage.BlobCreated Microsoft.Storage.BlobDeleted | Ja, vilken kombination som helst. |
| **blobPathBeginsWith** | Blobbsökvägen måste börja med det angivna mönstret för utlösare utlöses. Till exempel utlöses ”/ poster/objekt/december /' endast en utlösare för blobbar i mappen december under behållaren poster. | Sträng   | | Minst en av de här egenskaperna måste anges: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | Blobbsökväg måste avslutas med det angivna mönstret för utlösare utlöses. Till exempel kommer ”december/boxes.csv” endast innan utlösaren utlöser för BLOB-objekt med namnet rutor i en mapp för december. | Sträng   | | Minst en av de här egenskaperna måste anges: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Exempel på händelsebaserade utlösare

Det här avsnittet innehåller exempel på inställningar för händelsebaserade utlösare.

-   **Blobbsökväg som börjar med**('/ containername / ”) – tar emot händelser för alla blobar i behållaren.
-   **Blobbsökväg som börjar med**(”/ mappnamn/blobar/containername”) – tar emot händelser för alla blobar i behållaren för containername och mappnamn mappen.
-   **Blobbsökväg som börjar med**('/ containername/blobs/foldername/file.txt ”) – tar emot händelser för en blob med namnet fil.txt i mappnamn mapp under behållaren containername.
-   **Blobbsökväg slutar med**('fil.txt') – Receives händelser för en blob med namnet fil.txt på valfri sökväg.
-   **Blobbsökväg slutar med**('/ containername/blobs/file.txt ”) – tar emot händelser för en blob med namnet fil.txt under behållaren containername.
-   **Blobbsökväg slutar med**('foldername/file.txt') – Receives händelser för en blob med namnet fil.txt i mappnamn mapp under någon behållare.

> [!NOTE]
> Du behöver ta den `/blobs/` segmentet i sökvägen när du anger behållaren och mappen, behållare och filen eller behållare, mapp och fil.

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
