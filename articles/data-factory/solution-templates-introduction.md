---
title: Översikt över mallar
description: Lär dig hur du använder en fördefinierad mall för att komma igång snabbt med Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: f150234ecd3446858e8a6aa7d224eb3ad3d0efd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927348"
---
# <a name="templates"></a>Mallar

Mallar är fördefinierade Azure Data Factory-pipelines som gör att du kan komma igång snabbt med Data Factory. Mallar är användbara när du inte har startat Data Factory och vill komma igång snabbt. Dessa mallar minskar utvecklingstiden för att bygga dataintegrationsprojekt och därigenom förbättra utvecklarproduktiviteten.

## <a name="create-data-factory-pipelines-from-templates"></a>Skapa datafabrikspipelor från mallar

Du kan komma igång med att skapa en Data Factory-pipeline från en mall på följande två sätt:

1.  Välj **Skapa pipeline från mall** på sidan Översikt för att öppna mallgalleriet.

    ![Öppna mallgalleriet från sidan Översikt](media/solution-templates-introduction/templates-intro-image1.png)

1.  På fliken Författare i Resursutforskaren väljer du **+** och sedan Pipeline från **mall** för att öppna mallgalleriet.

    ![Öppna mallgalleriet på fliken Författare](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Mallgalleriet

![Mallgalleriet](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Ur rutan Data Factory-mallar

Data Factory använder Azure Resource Manager-mallar för att spara pipelinemallar för datafabrik. Du kan se alla Resource Manager-mallar, tillsammans med manifestfilen som används för färdiga datafabriksmallar, i den [officiella Azure Data Factory GitHub-repo .](https://github.com/Azure/Azure-DataFactory/tree/master/templates) De fördefinierade mallarna som tillhandahålls av Microsoft innehåller men är inte begränsade till följande objekt:

-   Kopiera mallar:

    -   [Masskopia från databas](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopiera nya filer genom LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopiera flera filbehållare mellan filbaserade butiker](solution-template-copy-files-multiple-containers.md)

    -   [Flytta filer](solution-template-move-files.md)

    -   [Delta-kopia från databas](solution-template-delta-copy-with-control-table.md)

    -   Kopiera \<från\> \<källa till mål\>

        -   [Från Amazon S3 till Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Från Google Big Query till Azure Data Lake Store Gen 2

        -   Från HDF till Azure Data Lake Store Gen 2

        -   Från Netezza till Azure Data Lake Store Gen 1

        -   Från SQL Server lokalt till Azure SQL Database

        -   Från SQL Server lokalt till Azure SQL Data Warehouse

        -   Från Oracle lokalt till Azure SQL Data Warehouse

-   SSIS-mallar

    -   Schemalägga Azure-SSIS Integration Runtime för att köra SSIS-paket

-   Omforma mallar

    -   [ETL med Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mina mallar

Du kan också spara en pipeline som en mall genom att välja **Spara som mall** på fliken Pipeline.

![Spara en pipeline som en mall](media/solution-templates-introduction/templates-intro-image4.png)

Du kan visa pipelines som sparats som mallar i avsnittet **Mina mallar** i mallgalleriet. Du kan också se dem i avsnittet **Mallar** i Resursutforskaren.

![Mina mallar](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Om du vill använda funktionen Mina mallar måste du aktivera GIT-integrering. Både Azure DevOps GIT och GitHub stöds.
