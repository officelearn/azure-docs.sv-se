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
ms.date: 06/24/2018
ms.author: douglasl
ms.openlocfilehash: 2bcb0d4e6af00b56d083690439be45379ce4d175
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752817"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Skapa en utlösare som kör en pipeline som svar på en händelse

Den här artikeln beskriver händelsebaserade utlösare som du kan skapa i din Data Factory pipelines.

Händelsedriven arkitektur (EDA) är en gemensam integration datamönster som inbegriper produktion, identifiering, förbrukning och reaktion på händelser. Data integrationsscenarier kräver ofta Data Factory kunder att utlösa pipelines baserat på händelser.

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

### <a name="create-a-new-event-trigger"></a>Skapa en ny händelseutlösare

En typisk händelse är till exempel ankomsten av en fil eller borttagningen av en fil i Azure Storage-konto. Du kan skapa en utlösare som svarar på den här händelsen i din Data Factory-pipelinen.

> [!NOTE]
> Denna integration stöder endast version 2 Storage-konton (generella).

![Skapa en ny utlösare](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Välj typen av händelse-utlösare

När filen når din lagringsplats och motsvarande blob skapas, utlöser den här händelsen och kör din Data Factory-pipelinen. Du kan skapa en utlösare som svarar på en händelse för blob-skapa en händelse för borttagning av blob eller båda händelserna i din Data Factory pipelines.

![Välj utlösartypen som händelse](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Konfigurera händelseutlösaren

Med den **blobbsökvägen börjar med** och **blobbsökvägen slutar med** egenskaper, kan du ange behållare, mappar och blob-namn som du vill ta emot händelser. Du kan använda olika mönster för både **blobbsökvägen börjar med** och **blobbsökvägen slutar med** egenskaper, som visas i exemplen nedan. Minst en av dessa egenskaper krävs.

![Konfigurera händelseutlösaren](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON-schema

Följande tabell innehåller en översikt över schemaelement som är relaterade till händelsebaserade utlösare:

| **JSON-Element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Krävs** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Omfång** | Resurs-ID för Azure Resource Manager för Lagringskontot. | Sträng | Azure Resource Manager-ID | Ja |
| **Händelser** | Typ av händelser som orsakar den här utlösaren att utlösas. | Matris    | Microsoft.Storage.BlobCreated Microsoft.Storage.BlobDeleted | Ja, vilken kombination som helst. |
| **blobPathBeginsWith** | Blobbsökvägen måste börja med det angivna mönstret för utlösare ska starta. Till exempel utlöses '/ poster/blobbar/december /' endast utlösare för blobbar i mappen december under behållaren poster. | Sträng   | | Minst en av dessa egenskaper måste anges: blobPathBeginsWith blobPathEndsWith. |
| **blobPathEndsWith** | Blobbsökvägen måste avslutas med det angivna mönstret för utlösare ska starta. Till exempel 'december/boxes.csv' endast att starta utlösare för blobbar med namnet rutor i en mapp för december. | Sträng   | | Minst en av dessa egenskaper måste anges: blobPathBeginsWith blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Exempel på händelsebaserade utlösare

Det här avsnittet innehåller exempel på inställningar för händelsebaserad utlösare.

-   **BLOB-sökväg som börjar med**('/ containername /') – tar emot händelser för alla blob-behållare.
-   **BLOB-sökväg som börjar med**('/ containername/mappnamn') – tar emot händelser för alla blobbar i behållaren för containername och mappnamn mappen.
-   **BLOB-sökväg som börjar med**('/ containername/foldername/file.txt') – tar emot händelser för en blob med namnet fil.txt i mappen mappnamn under behållaren containername.
-   **Blobbsökvägen som slutar med**('fil.txt') – mottagningar händelser för en blob med namnet fil.txt på valfri sökväg.
-   **Blobbsökvägen som slutar med**('/ containername/file.txt') – tar emot händelser för en blob med namnet fil.txt under behållaren containername.
-   **Blobbsökvägen som slutar med**('foldername/file.txt') – mottagningar händelser för en blob med namnet fil.txt mappnamn mappen under en behållare.

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
