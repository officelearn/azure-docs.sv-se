---
title: Använd U-SQL database-projekt att utveckla U-SQL-databas för Azure Data Lake | Microsoft Docs
description: Lär dig hur du utvecklar U-SQL-databas med Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889683"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Använd U-SQL database-projekt för att utveckla U-SQL-databas för Azure Data Lake

U-SQL-databasen innehåller strukturerade vyer över Ostrukturerade data, hantera strukturerade data i tabeller och tillhandahåller ett allmänt metadata catalog system för att organisera dina strukturerade data och anpassad kod. Databasen är konceptet som grupperar dessa relaterade objekt tillsammans.

Läs mer om [U-SQL-databas och Data Definition Language (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

U-SQL database-projekt är av projekttypen i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt.

## <a name="create-a-u-sql-database-project"></a>Skapa ett projekt för U-SQL-databas

Azure Data Lake Tools för Visual Studio till en projektmall för nya som heter U-SQL database-projekt efter version 2.3.3000.0. För att skapa ett U-SQL-projekt, gå igenom **fil > Nytt > projekt**, U-SQL Database-projekt kan hittas **Azure Data Lake > U-SQL nod**.

![Data Lake Tools för Visual Studio skapa u-sql database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Utveckla U-SQL-databasobjekt med hjälp av databasprojekt

Högerklicka på projektet för U-SQL-databasen, klickar du på **Lägg till > Nytt objekt**, alla objekt av typen finns i guiden Lägg till nytt objekt. 

1.  För icke-sammansättningsobjektet, till exempel tabellvärdesfunktion, ett nytt U-SQL-skript skapas när du lägger till nytt objekt. Du kan börja utveckla DDL-instruktion för objektet i redigeraren.
2.  För sammansättningsobjektet ger verktyget en användarvänliga UI-redigerare som hjälper dig att registrera sammansättningen och distribuera .dll och ytterligare filer. Följ stegen nedan för att lägga till en sammansättning objektdefinition i U-SQL-databasprojektet:

1.  Lägg till referenser för C#-projekt som innehåller UDO/UDAG/UDF-filen i projektet för U-SQL-databas.

    ![Data Lake Tools för Visual Studio lägger du till project-referens för u-sql-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools för Visual Studio lägger du till project-referens för u-sql-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  I designvyn i sammansättningen, väljer du den refererade sammansättningen från **skapa sammansättning från referens** utskriftsjobb.

    ![Data Lake Tools för Visual Studio skapa sammansättning från referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Lägg till **hanteras beroenden** och **ytterligare filer** eventuellt. När du lägger till ytterligare filer, använder verktyget den relativa sökvägen för att kontrollera att den kan hitta sammansättningarna både på den lokala datorn och build-datorn senare. @_DeployTempDirectory är en fördefinierad variabel som pekar Utdatamappen build verktyget. Under build utdata har varje sammansättningen en undermapp med namnet som sammansättningsnamnet alla DLL: er och ytterligare filer finns det i undermappen. 
 
## <a name="build-u-sql-database-project"></a>Skapa U-SQL database-projekt

Skapa utdata för U-SQL database-projekt är ett U-SQL-databas distributionspaket, med namnet med suffixet `.usqldbpack`. Den `.usqldbpack` paketet är en zip-fil innehåller alla DDL-instruktionerna i ett enda U-SQL-skript i **DDL** mappen och alla DLL: er och ytterligare filer för sammansättningar i **Temp** mapp.

Läs mer om [skapa U-SQL database-projekt med MSBuild-kommandoraden och Visual Studio Team Service build uppgift](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Distribuera U-SQL-databas

.Usqldbpack paketet kan distribueras till både lokalt konto eller Azure Data Lake Analytics-konto med hjälp av Visual Studio eller distributionen SDK. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Distribuera U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas via ett projekt för U-SQL-databas eller en .usqldbpack-paketet i Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Distribuera via U-SQL database-projekt

1.  Högerklicka på projektet för U-SQL-databasen och välj **distribuera**.
2.  I guiden Distribuera U-SQL-databas, väljer du den **ADLA konto** du vill distribuera databasen till. Kontona (lokalt) och ADLA konto stöds.
3.  **Databasen källa** fylls i automatiskt som pekar på .usqldbpack paketet i projektet build utdatamapp
4.  Ange **databasnamn** för att skapa en databas. Om det finns en databas med samma finns i Azure Data Lake Analytics-kontot, skapas alla objekt som definieras i databasprojektet utan att återskapa databasen.
5.  Klicka på **skicka** att distribuera U-SQL-databas. Alla resurser (sammansättningar och ytterligare filer) överförs och ett U-SQL-jobb som innehåller alla DDL-instruktionerna skickas.

    ![Data Lake Tools för Visual Studio distribuerar u-sql database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools för Visual Studio distribuerar projektguiden för u-sql-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Distribuera via distributionspaketet för U-SQL-databas

1.  Öppna **Server Explorer**, expandera den **Azure Data Lake Analytics-konto** du vill distribuera databasen till.
2.  Högerklicka på U-SQL-databaser och välj **distribuera databasen**.
3.  Ange **databaskällan** till U-SQL-databas distribution paket (.usqldbpack-fil) sökväg.
4.  Ange den **databasnamn** för att skapa en databas. Om det finns en databas med samma finns i Azure Data Lake Analytics-kontot, skapas alla objekt som definieras i databasprojektet utan att återskapa databasen.

    ![Data Lake Tools för Visual Studio distribuerar paketet för u-sql-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools för Visual Studio distribuerar guidepaket för u-sql-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Distribuera U-SQL-databas med hjälp av SDK

`PackageDeploymentTool.exe` innehåller programmering och kommandoradsverktyget gränssnitt som hjälper dig för att distribuera U-SQL-databaser. SDK: N som ingår i den [U-SQL SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), hitta på `build/runtime/PackageDeploymentTool.exe`.

[Mer information om SDK och hur du ställer in CI/CD-pipeline för U-SQL-databasdistribution](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Referera till ett projekt för U-SQL-databas

Ett U-SQL-projekt kan referera till ett projekt för U-SQL-databas. Referensen påverkar två arbetsbelastningar:

- Projektet build: de refererade databasmiljöer ställs in innan du skapar U-SQL-skript. 
- Lokal körning mot kontot (Local-projekt): de refererade databasmiljöer distribueras till (Local-projekt)-konto innan U-SQL-skript kan köras. [Läs mer om lokal körning och skillnaden mellan (lokal dator) och (Local-projekt) konto här](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Hur du lägger till referens för U-SQL-databas

1. Högerklicka på U-SQL-projektet i **Solution Explorer**, och välj **Lägg till referens för U-SQL-databas...** .

    ![Data Lake Tools för Visual Studio lägger du till databasen projektreferens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfigurera database referens från ett U-SQL-databas-projekt i denna lösning eller en paketfil för U-SQL-databas.
3. Ange namn för databasen.

    ![Data Lake Tools för Visual Studio lägger du till Databasguiden projekt-referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Nästa steg

- [Hur du ställer in CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
