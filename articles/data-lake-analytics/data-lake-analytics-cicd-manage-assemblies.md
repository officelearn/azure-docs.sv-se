---
title: Hantera U-SQL-sammansättningar i en CI/CD-pipeline - Azure Data Lake
description: Lär dig de bästa metoderna för att hantera U-SQL C#-sammansättningar i en CI/CD-pipeline med Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315849"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Metodtips för hantering av U-SQL-sammansättningar i en CI/CD-pipeline

I den här artikeln får du lära dig hur du hanterar U-SQL-sammansättnings källkod med det nyligen introducerade U-SQL-databasprojektet. Du lär dig också hur du konfigurerar en ci/cd-pipeline (continuous integration and deployment) för monteringsregistrering med hjälp av Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Använda U-SQL-databasprojektet för att hantera monteringskällan

[U-SQL-databasprojektet](data-lake-analytics-data-lake-tools-develop-usql-database.md) är en projekttyp i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt. Du kan hantera alla U-SQL-databasobjekt (förutom autentiseringsuppgifter) med U-SQL-databasprojektet. 

Om du vill hantera C#-monteringskällakoden och monteringsregistreringsregistreringen DDL U-SQL-skript använder du:

* U-SQL databas projekt för att hantera monteringsregistrering U-SQL-skript.
* Klassbibliotek (För U-SQL-program) för att hantera C#-källkoden och beroenden för användardefinierade operatorer, funktioner och sammanställare (UDOs, UDFs och UDAGs).
* U-SQL-databasprojekt som refererar till projektet Klassbibliotek. 

Ett U-SQL-databasprojekt kan referera till ett klassbiblioteksprojekt (för U-SQL Application). Du kan skapa sammansättningar som är registrerade i U-SQL-databasen med hjälp av refererad C#-källkod från det här klassbiblioteksprojektet (för U-SQL Application).

Följ dessa steg för att skapa projekt och lägga till referenser.
1. Skapa ett klassbiblioteksprojekt (för U-SQL Application) genom att välja **Arkiv** > **nytt** > **projekt**. Projektet är under **Azure Data Lake > U-SQL-noden.**

   ![Datasjöverktyg för Visual Studio – Skapa C#-klassbiblioteksprojekt](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Lägg till din användardefinierade C#-kod i projektet Class Library (For U-SQL Application).

1. Skapa ett U-SQL-projekt genom att välja **Arkiv** > **nytt** > **projekt**. Projektet är under **Azure Data Lake** > **U-SQL-noden.**

   ![DataSjöverktyg för Visual Studio – Skapa U-SQL-databasprojekt](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Lägg till en referens till C#-klassbiblioteksprojektet för U-SQL-databasprojektet.

    ![Datasjöverktyg för Visual Studio – Lägg till projektreferens för U-SQL-databas](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Datasjöverktyg för Visual Studio – Lägg till projektreferens för U-SQL-databas](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Skapa ett sammansättningsskript i U-SQL-databasprojektet genom att högerklicka på projektet och välja **Lägg till nytt objekt**.

   ![Datasjöverktyg för Visual Studio – Lägg till sammansättningsskript](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Öppna sammansättningsskriptet i sammansättningsdesignvyn. Välj den refererade sammansättningen på listrutan **Skapa sammansättning från referensmenyn.**

    ![Datasjöverktyg för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Lägg till **hanterade beroenden** och **ytterligare filer**, om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att se till att den kan hitta sammansättningarna på den lokala datorn och på byggdatorn senare.

_DeployTempDirectory i redigeringsfönstret längst ned finns en fördefinierad variabel som pekar verktyget på byggutdatamappen. ** \@** Under byggutdatamappen har varje sammansättning en undermapp med namnet sammansättningsnamn. Alla DLL-filer och ytterligare filer finns i den undermappen.

## <a name="build-a-u-sql-database-project"></a>Skapa ett U-SQL-databasprojekt

Build-utdata för ett U-SQL-databasprojekt är ett U-SQL-databasdistributionspaket. Det heter med suffixet `.usqldbpack`. Paketet `.usqldbpack` är en ZIP-fil som innehåller alla DDL-satser i ett enda U-SQL-skript i DDL-mappen. Alla inbyggda DLL-filer och ytterligare filer för sammansättningar finns i mappen Temp.

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

Paketet `.usqldbpack` kan distribueras till antingen ett lokalt konto eller ett Azure Data Lake Analytics-konto. Använd Visual Studio eller distributionen SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas med hjälp `.usqldbpack` av ett U-SQL-databasprojekt eller ett paket i Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Distribuera med hjälp av ett U-SQL-databasprojekt

1.  Högerklicka på U-SQL-databasprojektet och välj sedan **Distribuera**.
2.  I guiden **Distribuera U-SQL-databas** väljer du det **ADLA-konto** som du vill distribuera databasen till. Både lokala konton och ADLA-konton stöds.
3.  **Databaskällan** fylls i automatiskt. Det pekar på .usqldbpack-paketet i projektets byggutdatamapp.
4.  Ange ett namn i **Databasnamn** för att skapa en databas. Om det redan finns en databas med samma namn i kontot Azure Data Lake Analytics skapas alla objekt som definieras i databasprojektet utan att databasen återskapas.
5.  Om du vill distribuera U-SQL-databasen väljer du **Skicka**. Alla resurser, till exempel sammansättningar och ytterligare filer, överförs. Ett U-SQL-jobb som innehåller alla DDL-satser skickas.

    ![Datasjöverktyg för Visual Studio – distribuera U-SQL-databasprojekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Datasjöverktyg för Visual Studio – distribuera U-SQL-databasprojektguiden](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Distribuera en U-SQL-databas i Azure DevOps

`PackageDeploymentTool.exe`tillhandahåller programmerings- och kommandoradsgränssnitt som hjälper till att distribuera U-SQL-databaser. SDK ingår i [U-SQL SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns på `build/runtime/PackageDeploymentTool.exe`.

I Azure DevOps kan du använda en kommandoradsuppgift och den här SDK för att konfigurera en automatiseringspipeline för uppdatering av U-SQL-databasen. [Läs mer om SDK och hur du konfigurerar en CI/CD-pipeline för U-SQL-databasdistribution](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testa din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
* [Kör U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md)
