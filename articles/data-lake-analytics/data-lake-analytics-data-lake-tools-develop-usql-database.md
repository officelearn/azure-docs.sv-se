---
title: Utveckla ett U-SQL-databasprojekt - Azure Data Lake
description: Lär dig hur du utvecklar en U-SQL-databas med Hjälp av Azure Data Lake Tools för Visual Studio.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309933"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Använda ett U-SQL-databasprojekt för att utveckla en U-SQL-databas för Azure Data Lake

U-SQL-databasen ger strukturerade vyer över ostrukturerade data och hanterade strukturerade data i tabeller. Det ger också ett allmänt metadatakatalogsystem för att organisera dina strukturerade data och anpassade kod. Databasen är det begrepp som grupperar dessa relaterade objekt tillsammans.

Läs mer om [U-SQL-databas och datadefinitionsspråk (DDL)](/u-sql/data-definition-language-ddl-statements). 

U-SQL-databasprojektet är en projekttyp i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt.

## <a name="create-a-u-sql-database-project"></a>Skapa ett U-SQL-databasprojekt

Azure Data Lake Tools för Visual Studio har lagt till en ny projektmall som heter U-SQL-databasprojekt efter version 2.3.3000.0. Om du vill skapa ett U-SQL-projekt väljer du **> Nytt > Project**. U-SQL Database Project finns under **Azure Data Lake > U-SQL-nod**.

![DataSjöverktyg för Visual Studio – skapa U-SQL-databasprojekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Utveckla U-SQL-databasobjekt med hjälp av ett databasprojekt

Högerklicka på U-SQL-databasprojektet. Välj **Lägg till > Nytt objekt**. Du hittar alla nya objekttyper som stöds i guiden **Lägg till nytt objekt.** 

För ett icke-sammansättningsobjekt (till exempel en tabellvärderad funktion) skapas ett nytt U-SQL-skript när du har lagt till ett nytt objekt. Du kan börja utveckla DDL-satsen för objektet i redigeraren.

För ett sammansättningsobjekt tillhandahåller verktyget en användarvänlig gränssnittsredigerare som hjälper dig att registrera sammansättningen och distribuera DLL-filer och andra ytterligare filer. Följande steg visar hur du lägger till en sammansättningsobjektdefinition i U-SQL-databasprojektet:

1.  Lägg till referenser till C#-projektet som innehåller UDO/UDAG/UDF för U-SQL-databasprojektet.

    ![Datasjöverktyg för Visual Studio – Lägg till projektreferens för U-SQL-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Datasjöverktyg för Visual Studio – Lägg till projektreferens för U-SQL-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  I sammansättningsdesignvyn väljer du den refererade sammansättningen från **Skapa sammansättning från referensmenyn.**

    ![Datasjöverktyg för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Lägg till **hanterade beroenden** och **ytterligare filer** om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att se till att den kan hitta sammansättningarna både på den lokala datorn och på byggdatorn senare. 

@_DeployTempDirectoryär en fördefinierad variabel som pekar verktyget mot byggutdatamappen. Under byggutdatamappen har varje sammansättning en undermapp med namnet sammansättningsnamn. Alla DLL-filer och ytterligare filer finns i den undermappen. 
 
## <a name="build-a-u-sql-database-project"></a>Skapa ett U-SQL-databasprojekt

Byggutdata för ett U-SQL-databasprojekt är ett U-SQL-databasdistributionspaket med namnet suffixet `.usqldbpack`. Paketet `.usqldbpack` är en ZIP-fil som innehåller alla DDL-satser i ett enda U-SQL-skript i **DDL-mappen** och alla DLL-filer och ytterligare filer för sammansättningar i **Temp** temp-mappen.

Läs mer om hur du [skapar ett U-SQL-databasprojekt med MSBuild-kommandoraden och en Azure DevOps Services-bygguppgift](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

.usqldbpack-paketet kan distribueras till antingen ett lokalt konto eller ett Azure Data Lake Analytics-konto med hjälp av Visual Studio eller distributionen SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas via ett U-SQL-databasprojekt eller ett .usqldbpack-paket i Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Distribuera via ett U-SQL-databasprojekt

1.  Högerklicka på U-SQL-databasprojektet och välj sedan **Distribuera**.
2.  I **guiden Distribuera U-SQL-databas**väljer du det **ADLA-konto** som du vill distribuera databasen till. Både lokala konton och ADLA-konton stöds.
3.  **Databaskällan** fylls i automatiskt och pekar på .usqldbpack-paketet i projektets byggutdatamapp.
4.  Ange ett namn i **Databasnamn** för att skapa en databas. Om det redan finns en databas med samma namn i kontot Azure Data Lake Analytics skapas alla objekt som definieras i databasprojektet utan att databasen återskapas.
5.  Om du vill distribuera U-SQL-databasen väljer du **Skicka**. Alla resurser (sammansättningar och ytterligare filer) överförs och ett U-SQL-jobb som innehåller alla DDL-satser skickas.

    ![Datasjöverktyg för Visual Studio – distribuera U-SQL-databasprojekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Datasjöverktyg för Visual Studio – distribuera U-SQL-databasprojektguiden](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Distribuera via ett U-SQL-databasdistributionspaket

1.  Öppna **Server Explorer**. Expandera sedan **Azure Data Lake Analytics-kontot** som du vill distribuera databasen till.
2.  Högerklicka på **U-SQL-databaser**och välj sedan **Distribuera databas**.
3.  Ange **databaskällan** till driftssökvägen för U-SQL-databasdistributionspaketet (.usqldbpack file).
4.  Ange **databasnamnet** för att skapa en databas. Om det finns en databas med samma namn som redan finns i målet Azure Data Lake Analytics-konto skapas alla objekt som definieras i databasprojektet utan att återskapa databasen.

    ![Datasjöverktyg för Visual Studio – distribuera U-SQL-databaspaket](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Datasjöverktyg för Visual Studio – distribuera U-SQL-databaspaketguiden](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Distribuera U-SQL-databas med hjälp av SDK

`PackageDeploymentTool.exe`tillhandahåller programmerings- och kommandoradsgränssnitt som hjälper till att distribuera U-SQL-databaser. SDK ingår i [U-SQL SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns på `build/runtime/PackageDeploymentTool.exe`.

[Läs mer om SDK och hur du konfigurerar CI/CD-pipeline för U-SQL-databasdistribution](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Referera till ett U-SQL-databasprojekt

Ett U-SQL-projekt kan referera till ett U-SQL-databasprojekt. Referensen påverkar två arbetsbelastningar:

- *Projektuppbyggnad*: Ställ in refererade databasmiljöer innan du skapar U-SQL-skripten. 
- *Lokal körning mot (ett lokalt projekt) konto:* De refererade databasmiljöerna distribueras till (ett lokalt projekt) konto innan U-SQL-skriptkörning. [Läs mer om lokala körningar och skillnaden mellan (den lokala datorn) och (ett lokalt projekt) konto här](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Så här lägger du till en U-SQL-databasreferens

1. Högerklicka på U-SQL-projektet i **Solution Explorer**och välj sedan Lägg **till U-SQL-databasreferens...**.

    ![Datasjöverktyg för Visual Studio – lägg till databasprojektreferens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfigurera en databasreferens från ett U-SQL-databasprojekt i den aktuella lösningen eller i en U-SQL-databaspaketfil.
3. Ange namnet på databasen.

    ![Referensguiden för datasjöverktyg för Visual Studio lägger till databasprojektreferens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
- [Kör U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md)
