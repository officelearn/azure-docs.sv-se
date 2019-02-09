---
title: Kopiera filer från flera behållare med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösningsmall för att kopiera filer från flera behållare med Azure Data Factory.
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
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967416"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiera filer från flera behållare med Azure Data Factory

Lösningsmallen som beskrivs i den här artikeln hjälper dig att kopiera filer från flera filer, behållare eller buckets mellan fil. Till exempel kanske vill du migrera dina data lake från AWS S3 till Azure Data Lake Store. Eller kanske du vill replikera allt från ett Azure Blob Storage-konto till ett annat Azure Blob Storage-konto. Den här mallen är utformad för dessa användningsfall.

Om du vill kopiera filer från en enda behållare eller bucket är det mer effektivt att använda den **Data Kopieringsverktyget** att skapa en pipeline med en enda Kopieringsaktivitet. Den här mallen är högre än vad du behöver för det här enkla användningsfall.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen räknar upp behållare från din butik för lagring av källa och kopior av behållare från källagringen spara till målarkivet. 

Mallen innehåller tre aktiviteter:
-   En **GetMetadata** aktivitet att söka igenom din butik för lagring av källa och hämta listan över behållare.
-   En **ForEach** aktivitet för att hämta listan över behållare från den **GetMetadata** aktivitet och sedan iterera över listan och skicka varje behållare till kopieringsaktiviteten.
-   En **kopia** aktivitet som kopierar varje behållare från storage källagringen till målarkiv.

Mallen definierar två parametrar:
-   Parametern *SourceFilePath* är sökvägen till din datakällagring, där du kan få en lista över behållare eller buckets. I de flesta fall är sökvägen rotkatalog, som innehåller flera mappar i behållaren. Standardvärdet för den här parametern är `/`.
-   Parametern *DestinationFilePath* är sökvägen dit filerna ska kopieras i din målarkiv. Standardvärdet för den här parametern är `/`.

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Gå till mallen **kopiera flera filer behållare mellan filen**, och skapa en **ny anslutning** till källan storage store. Storage källagringen är den plats där du vill kopiera filer från flera behållare eller buckets.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Skapa en **ny anslutning** till din målarkiv för lagring.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Klicka på **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Pipelinen i panelen visas enligt följande exempel:

    ![Visa pipelinen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Klicka på **felsöka**, Fyll **parametrar** och klicka på **Slutför**.

    ![Köra en pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
