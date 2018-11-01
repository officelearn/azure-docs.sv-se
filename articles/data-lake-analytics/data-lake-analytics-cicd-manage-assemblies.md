---
title: Bästa praxis för att hantera U-SQL-sammansättningar i CI/CD-pipeline för Azure Data Lake
description: Lär dig det bästa sättet för att hantera U-SQL C# sammansättningar i CI/CD-pipeline med Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634610"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Bästa praxis för att hantera U-SQL-sammansättningar i CI/CD-pipeline

Lär dig hur du hanterar källkoden för U-SQL-sammansättning med det nya introducerats U-SQL-databasprojektet i den här artikeln. Du kan också lära dig hur du ställer in en kontinuerlig integrering och distribution (CI/CD) pipeline för registrering med Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Använda U-SQL database-projekt för att hantera källkoden för sammansättningen

[U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) är av projekttypen i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt. Alla U-SQL-databasobjekt (förutom autentiseringsuppgifter) kan hanteras med U-SQL database-projekt. 

Det rekommenderade sättet att hantera den C# sammansättningen källa kod och sammansättningen registrering DDL U-SQL-skript är att:

* Använd **U-SQL Database-projekt** att hantera sammansättningen registrering U-SQL-skript.
* Använd **Class Library (för U-SQL-program)** att hantera den C# source kod och beroenden för användardefinierade operatörer, funktioner och aggregatorer (UDO/UDF/UDAGs).
* Använd U-SQL database-projekt för att referera till klassbiblioteksprojektet. 

Ett projekt för U-SQL-databas kan referera till ett projekt i klassbiblioteket (för U-SQL-program). Sammansättningar som är registrerade i U-SQL-databasen kan skapas med refereras C# källkod från den här klassen Biblioteksprojekt (för U-SQL-program).

Du kan följa nedanstående steg för att skapa projekt och lägga till referenser:
1. Skapa ett projekt för klassbibliotek (för U-SQL-program) via **fil > Nytt > projekt**. Projektet är **Azure Data Lake > U-SQL** noden.
   ![Data Lake Tools för Visual Studio – skapa C# klassbiblioteksprojektet](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Lägg till dina egna C# koden i projektet klassbiblioteket (för U-SQL-program). 
3. Skapa ett U-SQL-projekt genom **fil > Nytt > projekt**. Projektet är **Azure Data Lake > U-SQL** noden.
   ![Data Lake Tools för Visual Studio – skapa U-SQL database-projekt](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Lägg till referens till den C# klassbiblioteksprojektet för U-SQL database-projekt.

    ![Projektreferens för data Lake Tools för Visual Studio – Lägg till U-SQL-databas](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Projektreferens för data Lake Tools för Visual Studio – Lägg till U-SQL-databas](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Skapa ett skript för sammansättningen i projektet för U-SQL-databas med **Högerklicka på projektet > Lägg till nytt objekt**.
   ![Data Lake Tools för Visual Studio – Lägg till sammansättningen skript](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Öppna skriptet sammansättningen i designvyn sammansättningen väljer den refererade sammansättningen från **skapa sammansättning från referens** nedrullningsbara menyn.

    ![Data Lake Tools för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Lägg till **hanteras beroenden** och **ytterligare filer** om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att se till att den kan hitta sammansättningarna både på den lokala datorn och på build-datorn senare. 

**@_DeployTempDirectory** fönstret längst ned på sidan är en fördefinierad variabel som pekar Utdatamappen build verktyget i redigeraren. Alla paket har en undermapp med namnet med sammansättningsnamnet under mappen build-utdata. Alla DLL: er och andra filer som finns i undermappen. 

## <a name="build-a-u-sql-database-project"></a>Skapa ett projekt för U-SQL-databas

Skapa utdata för ett projekt för U-SQL-databas är ett U-SQL-databas distributionspaket, med namnet med suffixet `.usqldbpack`. Den `.usqldbpack` paketet är en .zip-fil som innehåller alla DDL-instruktionerna i ett enda U-SQL-skript i den **DDL** mappen och allt detta är inbyggt DLL-filer och ytterligare filer för sammansättningar i den **Temp** mapp.

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

Den `.usqldbpack` paket kan distribueras till ett lokalt konto eller ett Azure Data Lake Analytics-konto med hjälp av Visual Studio eller distributionen SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas via ett projekt för U-SQL-databas eller en `.usqldbpack` paketet i Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Distribuera via ett projekt för U-SQL-databas

1.  Högerklicka på projektet för U-SQL-databasen och välj sedan **distribuera**.
2.  I den **distribuera U-SQL Database-guiden**väljer den **ADLA konto** som du vill distribuera databasen. Både lokala konton och ADLA-konton stöds.
3.  **Databasen källa** fylls i automatiskt, och pekar på .usqldbpack paketet i projektet skapar utdatamappen.
4.  Ange ett namn i **databasnamn** att skapa en databas. Om det finns redan en databas med det här namnet i Azure Data Lake Analytics-kontot, skapas alla objekt som definieras i databas-projekt utan att återskapa databasen.
5.  Om du vill distribuera U-SQL-databas, Välj **skicka**. Alla resurser (sammansättningar och ytterligare filer) överförs och ett U-SQL-jobb som innehåller alla DDL-instruktionerna skickas.

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-guiden för projektet](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Distribuera U-SQL-databas i Azure DevOps

`PackageDeploymentTool.exe` innehåller programmering och kommandoradsverktyget gränssnitt som hjälper dig för att distribuera U-SQL-databaser. SDK: N som ingår i den [U-SQL SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns i `build/runtime/PackageDeploymentTool.exe`.

I Azure DevOps, kan du använda kommandoradsuppgift och detta SDK för att ställa in automation pipeline för uppdatering för U-SQL-databas. [Mer information om SDK och hur du ställer in CI/CD-pipeline för U-SQL-databasdistribution](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Nästa steg

* [Hur du ställer in en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
* [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
