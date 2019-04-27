---
title: Kopiera filer från flera behållare med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösningsmall för att kopiera filer från flera behållare med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635211"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiera filer från flera behållare med Azure Data Factory

Den här artikeln beskrivs en mall som du kan använda för att kopiera filer från flera behållare mellan fil. Du kan till exempel använda den för att migrera dina data lake från AWS S3 till Azure Data Lake Store. Eller du kan använda mallen för att replikera allt från en Azure Blob storage-konto till en annan.

> [!NOTE]
> Om du vill kopiera filer från en enskild behållare är det mer effektivt att använda den [Data Kopieringsverktyget](copy-data-tool.md) att skapa en pipeline med en enda Kopieringsaktivitet. Mallen i den här artikeln är högre än vad du behöver för det enkla scenariot.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen räknar upp behållare från källan storage store. De här behållarna kopieras sedan till målarkivet.

Mallen innehåller tre aktiviteter:
- **GetMetadata** söker igenom din butik för lagring av källa och hämtar listan över behållare.
- **ForEach** hämtar listan över behållare från den **GetMetadata** aktiviteten itererar över listan och skickar varje behållare till kopieringsaktiviteten.
- **Kopiera** kopierar varje behållare från storage källagringen till målarkiv.

Mallen definierar två parametrar:
- *SourceFilePath* är sökvägen till din datakällagring, där du kan få en lista över behållarna. I de flesta fall är sökvägen rotkatalog, som innehåller flera mappar i behållaren. Standardvärdet för den här parametern är `/`.
- *DestinationFilePath* är den sökväg där filerna ska kopieras till i din målarkiv. Standardvärdet för den här parametern är `/`.

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Gå till den **kopiera flera filer behållare mellan filen** mall. Skapa en **New** anslutning till din butik för lagring av källa. Storage källagringen är där du vill kopiera filer från flera behållare från.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Skapa en **New** anslutning till din målarkiv för lagring.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Pipelinen, som i följande exempel visas:

    ![Visa pipelinen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Välj **felsöka**, ange den **parametrar**, och välj sedan **Slutför**.

    ![Köra en pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Nästa steg

- [Masskopiering från en databas med hjälp av en kontroll-tabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)