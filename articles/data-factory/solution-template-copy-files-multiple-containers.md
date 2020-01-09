---
title: Kopiera filer från flera containrar
description: Lär dig hur du använder en lösnings mall för att kopiera filer från flera behållare med hjälp av Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439751"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiera filer från flera behållare med Azure Data Factory

Den här artikeln beskriver en lösnings mall som du kan använda för att kopiera filer från flera behållare mellan fil arkiv. Du kan till exempel använda den för att migrera data Lake från AWS S3 till Azure Data Lake Store. Eller så kan du använda mallen för att replikera allt från ett Azure Blob Storage-konto till ett annat.

> [!NOTE]
> Om du vill kopiera filer från en enda behållare är det mer effektivt att använda [Kopiera data-verktyget](copy-data-tool.md) för att skapa en pipeline med en enda kopierings aktivitet. Mallen i den här artikeln är mer än du behöver för det enkla scenariot.

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen räknar upp behållarna från din käll lagrings lagring. Den kopierar sedan dessa behållare till mål lagret.

Mallen innehåller tre aktiviteter:
- **GetMetaData** genomsöker ditt käll lagrings lager och hämtar behållar listan.
- Med **början hämtar du** behållar listan från **getMetaData** -aktiviteten och itererar sedan över listan och överför varje behållare till kopierings aktiviteten.
- **Kopiera** kopierar varje behållare från käll lagrings lagret till mål arkivet.

Mallen definierar följande parametrar:
- *SourceFileFolder* är mappsökvägen till data käll arkivet där du kan hämta en lista över behållarna. Sökvägen är rot katalogen, som innehåller flera behållar-mappar. Standardvärdet för den här parametern är `sourcefolder`.
- *SourceFileDirectory* är sökvägen till undermappen under rot katalogen i data käll arkivet. Standardvärdet för den här parametern är `subfolder`.
- *DestinationFileFolder* är sökvägen till mappen där filerna kopieras till i mål arkivet. Standardvärdet för den här parametern är `destinationfolder`.
- *DestinationFileDirectory* är sökvägen till undermappen där filerna kopieras till i mål arkivet. Standardvärdet för den här parametern är `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till mallen **Kopiera flera filer mellan fil Arkiv** . Skapa en **ny** anslutning till ditt käll lagrings lager. Käll lagrings platsen är den plats där du vill kopiera filer från flera behållare från.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Skapa en **ny** anslutning till mål lagrings platsen.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Du ser pipelinen, som i följande exempel:

    ![Visa pipelinen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Välj **Felsök**, ange **parametrarna**och välj sedan **Slutför**.

    ![Köra en pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Nästa steg

- [Mass kopiering från en databas med hjälp av en kontroll tabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
