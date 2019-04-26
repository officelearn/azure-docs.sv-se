---
title: Metodtips för hantering av U-SQL-sammansättningar i en CI/CD-pipeline för Azure Data Lake
description: Metodtips för hantering av U-SQL C# sammansättningar i en CI/CD-pipeline med Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 27a873fac8bf2b53ee06780b8a348eaaa5c94e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334274"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Metodtips för hantering av U-SQL-sammansättningar i en CI/CD-pipeline

Lär dig hur du hanterar källkoden för U-SQL-sammansättningen med de nyligen införda U-SQL database-projektet i den här artikeln. Du också lära dig hur du ställer in en kontinuerlig integrering och distribution (CI/CD) pipeline för registrering med Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Använda U-SQL database-projekt för att hantera källkoden för sammansättningen

[U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) är av projekttypen i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt. Du kan hantera alla U-SQL database-objekt (förutom autentiseringsuppgifter) med U-SQL database-projekt. 

Att hantera den C# sammansättningen källkoden och sammansättningen registrering DDL U-SQL-skript, använda den:

* U-SQL database-projekt för att hantera sammansättningen registrering U-SQL-skript.
* Klassbibliotek (för U-SQL-program) att hantera den C# source kod och beroenden för användardefinierade operatörer, funktioner och aggregatorer (UDO, UDF: er och UDAGs).
* U-SQL database-projekt att referera till klassbiblioteket-projektet. 

Ett projekt för U-SQL-databas kan referera till ett projekt i klassbiblioteket (för U-SQL-program). Du kan skapa sammansättningar som registrerats i U-SQL-databasen med hjälp av refereras C# källkod från den här klassen biblioteksprojekt (för U-SQL-program).

Följ dessa steg för att skapa projekt och Lägg till referenser.
1. Skapa ett projekt för klassbibliotek (för U-SQL-program) genom att välja **filen** > **New** > **projekt**. Projektet är den **Azure Data Lake > U-SQL** noden.

   ![Data Lake Tools för Visual Studio – skapa C# klassbiblioteksprojektet](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Lägg till dina egna C# koden i projektet klassbiblioteket (för U-SQL-program).

1. Skapa ett U-SQL-projekt genom att välja **filen** > **New** > **projekt**. Projektet är den **Azure Data Lake** > **U-SQL** noden.

   ![Data Lake Tools för Visual Studio – skapa U-SQL database-projekt](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Lägg till en referens till den C# klassbiblioteksprojektet för U-SQL database-projekt.

    ![Projektreferens för data Lake Tools för Visual Studio – Lägg till U-SQL-databas](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Projektreferens för data Lake Tools för Visual Studio – Lägg till U-SQL-databas](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Skapa ett skript för sammansättningen i U-SQL database-projekt genom att högerklicka på projektet och välja **Lägg till nytt objekt**.

   ![Data Lake Tools för Visual Studio – Lägg till sammansättningen skript](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Öppna sammansättningen skriptet i designvyn sammansättningen. Välj den refererade sammansättningen från den **skapa sammansättning från referens** nedrullningsbara menyn.

    ![Data Lake Tools för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Lägg till **hanteras beroenden** och **ytterligare filer**, om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att se till att den kan hitta sammansättningarna på den lokala datorn och på build-datorn senare.

**\@_DeployTempDirectory** i redigeraren fönster längst ned på sidan är en fördefinierad variabel som pekar Utdatamappen build verktyget. Alla paket har en undermapp med namnet med sammansättningsnamnet under mappen build-utdata. Alla DLL: er och andra filer som finns i undermappen.

## <a name="build-a-u-sql-database-project"></a>Skapa ett projekt för U-SQL-databas

Resultatet för ett projekt för U-SQL-databas är ett distributionspaket för U-SQL-databas. Den heter med suffixet `.usqldbpack`. Den `.usqldbpack` paketet är en .zip-fil som innehåller alla DDL-instruktionerna i ett enda U-SQL-skript i DDL-mappen. Alla inbyggda DLL-filer och ytterligare filer för sammansättningar är i Temp-mappen.

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

Den `.usqldbpack` paket kan distribueras till ett lokalt konto eller ett Azure Data Lake Analytics-konto. Använd Visual Studio eller distributionen SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas med hjälp av ett projekt för U-SQL-databas eller en `.usqldbpack` paketet i Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Distribuera med hjälp av ett projekt för U-SQL-databas

1.  Högerklicka på projektet för U-SQL-databasen och välj sedan **distribuera**.
2.  I den **distribuera U-SQL-databas** guiden väljer du den **ADLA konto** som du vill distribuera databasen. Både lokala konton och ADLA-konton stöds.
3.  **Databasen källa** fylls i automatiskt. Den pekar på .usqldbpack paketet i projektet build utdatamappen.
4.  Ange ett namn i **databasnamn** att skapa en databas. Om det finns redan en databas med det här namnet i Azure Data Lake Analytics-kontot, skapas alla objekt som definieras i databas-projekt utan att återskapa databasen.
5.  Om du vill distribuera U-SQL-databas, Välj **skicka**. Alla resurser, till exempel sammansättningar och ytterligare filer laddas upp. Ett U-SQL-jobb som innehåller alla DDL-instruktionerna skickas.

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-guiden för projektet](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Distribuera en U-SQL-databas i Azure DevOps

`PackageDeploymentTool.exe` innehåller programmering och kommandoradsverktyget gränssnitt som hjälper dig för att distribuera U-SQL-databaser. SDK: N som ingår i den [U-SQL SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns i `build/runtime/PackageDeploymentTool.exe`.

I Azure DevOps, kan du använda en kommandoradsaktivitet och detta SDK för att ställa in en automation-pipeline för uppdatering för U-SQL-databas. [Mer information om SDK och hur du ställer in en CI/CD-pipeline för distribution av U-SQL-databaser](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testa din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
* [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
