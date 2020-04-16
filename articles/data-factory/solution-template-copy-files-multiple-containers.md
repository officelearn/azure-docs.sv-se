---
title: Kopiera filer från flera containrar
description: Lär dig hur du använder en lösningsmall för att kopiera filer från flera behållare med hjälp av Azure Data Factory.
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
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414825"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiera filer från flera behållare med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs en lösningsmall som du kan använda för att kopiera filer från flera behållare mellan filarkiv. Du kan till exempel använda den för att migrera datasjön från AWS S3 till Azure Data Lake Store. Du kan också använda mallen för att replikera allt från ett Azure Blob-lagringskonto till ett annat.

> [!NOTE]
> Om du vill kopiera filer från en enda behållare är det mer effektivt att använda [verktyget Kopiera data](copy-data-tool.md) för att skapa en pipeline med en enda kopieringsaktivitet. Mallen i den här artikeln är mer än du behöver för det enkla scenariot.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen räknar upp behållarna från källlagringsarkivet. Därefter kopieras behållarna till destinationsarkivet.

Mallen innehåller tre aktiviteter:
- **GetMetadata** söker igenom källlagringsarkivet och hämtar behållarlistan.
- **ForEach** hämtar behållarlistan från **GetMetadata-aktiviteten** och itererar sedan över listan och skickar varje behållare till kopieringsaktiviteten.
- **Kopiera** kopior av varje behållare från källlagringsarkivet till målarkivet.

Mallen definierar följande parametrar:
- *SourceFileFolder* är mappsökvägen till ditt datakällarkiv, där du kan hämta en lista över behållarna. Sökvägen är rotkatalogen, som innehåller flera behållarmappar. Standardvärdet för den `sourcefolder`här parametern är .
- *SourceFileDirectory* är undermappssökvägen under rotkatalogen i datakällarkivet. Standardvärdet för den `subfolder`här parametern är .
- *DestinationFileFolder* är mappsökvägen där filerna kopieras till i målarkivet. Standardvärdet för den `destinationfolder`här parametern är .
- *DestinationFileDirectory* är den undermappssökväg där filerna kopieras till i målarkivet. Standardvärdet för den `subfolder`här parametern är .

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Gå till mallen Kopiera flera filer mellan mallen **Arkivbutiker.** Skapa en **ny** anslutning till källlagringsarkivet. Källlagringsarkivet är där du vill kopiera filer från flera behållare från.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Skapa en **ny** anslutning till ditt mållagringslager.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Du ser pipelinen, som i följande exempel:

    ![Visa pipelinen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.

    ![Köra en pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Nästa steg

- [Masskopiering från en databas med hjälp av en kontrolltabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
