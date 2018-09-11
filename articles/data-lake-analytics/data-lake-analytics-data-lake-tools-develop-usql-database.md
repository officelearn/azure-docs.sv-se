---
title: Använd en U-SQL database-projekt för att utveckla en U-SQL-databas för Azure Data Lake
description: Lär dig hur du utvecklar en U-SQL-databas med Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: c731fd78ed7052697b3a5bd7c4da3a743e5a208d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299621"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Använd en U-SQL database-projekt för att utveckla en U-SQL-databas för Azure Data Lake

U-SQL-databas ger strukturerade vyer över Ostrukturerade data och hanterade strukturerade data i tabeller. Det ger också ett allmänt metadata catalog system för att ordna dina strukturerade data och anpassad kod. Databasen är konceptet som grupperar dessa relaterade objekt tillsammans.

Läs mer om [U-SQL-databas och Data Definition Language (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

U-SQL database-projekt är av projekttypen i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt.

## <a name="create-a-u-sql-database-project"></a>Skapa ett projekt för U-SQL-databas

Azure Data Lake Tools för Visual Studio till en projektmall för nya som heter U-SQL database-projekt efter version 2.3.3000.0. Om du vill skapa ett U-SQL-projekt, Välj **fil > Nytt > projekt**. U-SQL Database-projekt kan hittas **Azure Data Lake > U-SQL nod**.

![Data Lake Tools för Visual Studio – skapa U-SQL database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Utveckla U-SQL-databasobjekt med hjälp av en databas-projekt

Högerklicka på projektet för U-SQL-databas. Välj **Lägg till > Nytt objekt**. Du kan hitta alla nya objekttyper som stöds i den **Lägg till nytt objekt** guiden. 

För icke-sammansättningen-objekt (till exempel en tabellvärdesfunktion) skapas ett nytt U-SQL-skript när du lägger till ett nytt objekt. Du kan börja utveckla DDL-instruktion för objektet i redigeraren.

Verktyget ger en användarvänliga UI-redigerare som hjälper dig att registrera sammansättningen och distribuera DLL-filer och andra ytterligare filer för ett sammansättningen-objekt. Följande steg visar hur du lägger till en sammansättning objektdefinition i U-SQL-databas-projektet:

1.  Lägg till referenser till C#-projekt som innehåller UDO/UDAG/UDF för U-SQL database-projekt.

    ![Projektreferens för data Lake Tools för Visual Studio – Lägg till U-SQL-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Projektreferens för data Lake Tools för Visual Studio – Lägg till U-SQL-databas](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  I designvyn sammansättningen väljer du den refererade sammansättningen från **skapa sammansättning från referens** nedrullningsbara menyn.

    ![Data Lake Tools för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Lägg till **hanteras beroenden** och **ytterligare filer** om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att se till att den kan hitta sammansättningarna både på den lokala datorn och på build-datorn senare. 

@_DeployTempDirectory är en fördefinierad variabel som pekar Utdatamappen build verktyget. Alla paket har en undermapp med namnet med sammansättningsnamnet under mappen build-utdata. Alla DLL: er och andra filer som finns i undermappen. 
 
## <a name="build-a-u-sql-database-project"></a>Skapa ett projekt för U-SQL-databas

Skapa utdata för ett projekt för U-SQL-databas är ett U-SQL-databas distributionspaket, med namnet med suffixet `.usqldbpack`. Den `.usqldbpack` paketet är en .zip-fil som innehåller alla DDL-instruktionerna i ett enda U-SQL-skript i den **DDL** mappen och alla DLL: er och ytterligare filer för sammansättningar i den **Temp** mapp.

Läs mer om [hur du skapar ett projekt med U-SQL-databas med MSBuild kommandoraden och en Azure-tjänster för DevOps Skapa uppgift](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

.Usqldbpack paketet kan distribueras till ett lokalt konto eller ett Azure Data Lake Analytics-konto med hjälp av Visual Studio eller distributionen SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas via ett projekt för U-SQL-databas eller en .usqldbpack-paketet i Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Distribuera via ett projekt för U-SQL-databas

1.  Högerklicka på projektet för U-SQL-databasen och välj sedan **distribuera**.
2.  I den **distribuera U-SQL Database-guiden**väljer den **ADLA konto** som du vill distribuera databasen. Både lokala konton och ADLA-konton stöds.
3.  **Databasen källa** fylls i automatiskt, och pekar på .usqldbpack paketet i projektet skapar utdatamappen.
4.  Ange ett namn i **databasnamn** att skapa en databas. Om det finns redan en databas med det här namnet i Azure Data Lake Analytics-kontot, skapas alla objekt som definieras i databas-projekt utan att återskapa databasen.
5.  Om du vill distribuera U-SQL-databas, Välj **skicka**. Alla resurser (sammansättningar och ytterligare filer) överförs och ett U-SQL-jobb som innehåller alla DDL-instruktionerna skickas.

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-guiden för projektet](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Distribuera via ett distributionspaket för U-SQL-databas

1.  Öppna **serverutforskaren**. Expandera sedan den **Azure Data Lake Analytics-konto** som du vill distribuera databasen.
2.  Högerklicka på **U-SQL-databaser**, och välj sedan **distribuera databasen**.
3.  Ange **databaskällan** till U-SQL-databas distribution paket (.usqldbpack-fil) sökväg.
4.  Ange den **databasnamn** att skapa en databas. Om det finns en databas med samma namn som redan finns i Azure Data Lake Analytics-kontot, skapas alla objekt som definieras i databas-projekt utan att återskapa databasen.

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-paket](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools för Visual Studio – distribuera U-SQL database-guiden för paketet](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Distribuera U-SQL-databas med hjälp av SDK

`PackageDeploymentTool.exe` innehåller programmering och kommandoradsverktyget gränssnitt som hjälper dig för att distribuera U-SQL-databaser. SDK: N som ingår i den [U-SQL SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns i `build/runtime/PackageDeploymentTool.exe`.

[Mer information om SDK och hur du ställer in CI/CD-pipeline för U-SQL-databasdistribution](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-devops).

## <a name="reference-a-u-sql-database-project"></a>Referera till ett projekt för U-SQL-databas

Ett U-SQL-projekt kan referera till ett projekt för U-SQL-databas. Referensen påverkar två arbetsbelastningar:

- *Projektet build*: ställa in de refererade databasmiljöer innan du skapar U-SQL-skript. 
- *Lokalt konto som kör mot (en lokal-projekt)*: de refererade databasmiljöer distribueras till (en lokal-projekt) konto innan U-SQL-skript kan köras. [Mer information om lokala körningar och skillnaden mellan (local-dator) och (en lokal-projekt) konto här](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Hur du lägger till en referens för U-SQL-databas

1. Högerklicka på U-SQL-projektet i **Solution Explorer**, och välj sedan **Lägg till referens för U-SQL-databas...** .

    ![Data Lake Tools för Visual Studio – Lägg till referens för databas-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfigurera en referens för databasen från en U-SQL database-projekt i den aktuella lösningen eller en paketfil för U-SQL-databas.
3. Ange namn för databasen.

    ![Data Lake Tools för Visual Studio lägger du till Databasguiden projekt-referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Nästa steg

- [Hur du ställer in en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
