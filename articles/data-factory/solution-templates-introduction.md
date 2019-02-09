---
title: Översikt över mallar för Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en fördefinierad mall för att snabbt komma igång med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: c3571aec55d5050df01f740a163db18e9c6c1095
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967620"
---
# <a name="templates"></a>Mallar

Mallar är fördefinierade Azure Data Factory-pipelines som gör det möjligt att snabbt komma igång med Data Factory. Mallar är användbara när du inte har arbetat med Data Factory och om du vill komma igång snabbt. Dessa mallar minska utvecklingstiden för att skapa dataintegreringsprojekt därmed förbättra produktivitet för utvecklare.

## <a name="create-data-factory-pipelines-from-templates"></a>Skapa Data Factory-pipeliner från mallar

Du kan börja skapa Data Factory-pipeline från en mall i följande två sätt:

1.  Välj **skapa pipeline från mall** på översiktssidan för att öppna mallgalleriet.

    ![Öppna mallgalleriet från sidan Översikt](media/solution-templates-introduction/templates-intro-image1.png)

1.  På fliken författare i Resursläsaren väljer **+**, sedan **Pipeline från mallen** att öppna mallgalleriet.

    ![Öppna mallgalleriet från fliken författare](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galleri över mallar

![Mallgalleriet](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Utanför rutan Data Factory-mallar

Data Factory använder Azure Resource Manager-mallar för att spara data factory-pipeline-mallar. Du kan se alla Resource Manager-mallar, tillsammans med manifestfilen används för utanför rutan Data Factory-mallar i den [officiella Azure Data Factory Github-lagringsplatsen](https://github.com/Azure/Azure-DataFactory/tree/master/templates). De fördefinierade mallarna som tillhandahålls av Microsoft inkludera, men är inte begränsad till följande objekt:

-   Kopiera mallar:

    -   [Masskopiering från databasen](solution-template-bulk-copy-with-control-table.md)

    -   [Kopiera flera filen behållare mellan filbaserade](solution-template-copy-files-multiple-containers.md)

    -   [Deltakopiering från databasen](solution-template-delta-copy-with-control-table.md)

    -   Kopiera från \<källa\> till \<mål\>

        -   Generation 2 från Amazon S3 till Azure Data Lake Store

        -   Generation 2 från Google Big Query till Azure Data Lake Store

        -   Generation 2 från HDF till Azure Data Lake Store

        -   Från Netezza till Azure Data Lake Store generation 1

        -   Från SQL Server lokalt till Azure SQL Database

        -   Från SQL Server lokalt till Azure SQL Data Warehouse

        -   Från Oracle på plats till Azure SQL Data Warehouse

-   SSIS-mallar

    -   Schemalägga Azure SSIS Integration Runtime för att köra SSIS-paket

-   Omvandla mallar

    -   [ETL med Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mina mallar

Du kan också spara en pipeline som en mall genom att välja **Spara som mall** på fliken Pipeline.

![Spara en pipeline som en mall](media/solution-templates-introduction/templates-intro-image4.png)

Du kan visa pipelines sparas som mallar i den **Mina mallar** avsnittet galleriets mall. Du kan också se dem i den **mallar** avsnittet i Resursläsaren.

![Mina mallar](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Du måste aktivera GIT-integrering för att använda funktionen Mina mallar. Både Azure DevOps GIT och GitHub stöds.
