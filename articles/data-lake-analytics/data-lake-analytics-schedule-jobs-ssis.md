---
title: Schemalägga Azure Data Lake Analytics U-SQL-jobb med SSIS
description: Lär dig hur du använder SQL Server Integration Services för att schemalägga U-SQL-jobb.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 6894486118f69e682353142be04821e1d28440e5
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523179"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Schemalägga U-SQL-jobb med hjälp av SQL Server Integration Services (SSIS)

Du lär dig hur du dirigera och skapa U-SQL-jobb med hjälp av SQL Server Integration Service (SSIS) i det här dokumentet. 

## <a name="prerequisites"></a>Nödvändiga komponenter

[Azure Feature Pack för Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) ger den [Azure Data Lake Analytics-aktivitet](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) och [Anslutningshanteraren för Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) att hjälper dig att ansluta till Azure Data Lake Analytics-tjänsten. Om du vill använda den här uppgiften, måste du installera:

- [Ladda ned och installera SQL Server Data Tools (SSDT) för Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Installera Azure Feature Pack för Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics-aktivitet

Azure Data Lake Analytics-uppgiften att användarna kan skicka U-SQL-jobb på Azure Data Lake Analytics-konto. 

[Lär dig hur du konfigurerar Azure Data Lake Analytics-aktivitet](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Azure Data Lake Analytics-aktivitet i SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Du kan hämta U-SQL-skriptet från olika platser med hjälp av inbyggda funktioner för SSIS och uppgifter nedan scenarier visar hur du konfigurerar U-SQL-skript för olika användare fall.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenario 1 – Använd funktionen skriptet anrop tabellvärdesfunktioner och lagrade procs

Konfigurera i Azure Data Lake Analytics uppgift Editor **SourceType** som **DirectInput**, och placera U-SQL-instruktioner i **USQLStatement**.

För enkelt underhåll och hantering av koden, bara lägga kort U-SQL-skript som infogade skript, till exempel kan du anropa den befintliga tabellvärderade funktioner och lagrade procedurer i dina U-SQL-databaser. 

![Redigera infogat U-SQL-skript i SSIS-aktivitet](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Relaterad artikel: [Hur du skicka parametern till lagrade procedurer](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenario 2 – Använd U-SQL-filer i Azure Data Lake Store

Du kan också använda U-SQL-filer i Azure Data Lake Store med hjälp av **systemaktivitet för Azure Data Lake Store-filen** i Azure Feature Pack. Den här metoden kan du använda de skript som lagras i molnet.

Följ stegen nedan för att konfigurera anslutningen mellan systemaktivitet för Azure Data Lake Store-fil och Azure Data Lake Analytics-aktivitet.

### <a name="set-task-control-flow"></a>Kontrollflöde för set-aktivitet

I designvyn för SSIS-paket, lägger du till en **systemaktivitet för Azure Data Lake Store-filen**, ett **Foreach-Loop behållare** och en **Azure Data Lake Analytics uppgift** i Foreach-Loop Behållaren. Det hjälper dig för att hämta U-SQL-filer i ditt ADLS-konto till en tillfällig mapp systemaktivitet för Azure Data Lake Store-filen. Foreach-Loop behållare och Azure Data Lake Analytics-aktivitet kan så du skicka alla U-SQL-filer under den tillfälliga mappen i Azure Data Lake Analytics-konto som ett U-SQL-jobb.

![Använda U-SQL-filer i Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurera systemaktivitet för Azure Data Lake Store-fil

1. Ange **åtgärden** till **CopyFromADLS**.
2. Konfigurera **AzureDataLakeConnection**, Lär dig mer om [Anslutningshanteraren för Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Ange **AzureDataLakeDirectory**. Pekar till den mapp som lagrar dina U-SQL-skript. Använda relativ sökväg som är relativ till rotmappen för Azure Data Lake Store-konto.
4. Ange **mål** till en mapp som cachelagrar U-SQL-skript som laddas ned. Den här mappsökväg används i Foreach-Loop behållare för jobbinlämningar för U-SQL. 

![Konfigurera systemaktivitet för Azure Data Lake Store-fil](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Mer information om systemaktivitet för Azure Data Lake Store-filen](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfigurera Foreach-Loop-behållaren

1. I **samling** ställer du in **uppräknare** till **Foreach filen uppräknare**.

2. Ange **mappen** under **uppräknare configuration** grupp till den tillfälliga mappen som innehåller U-SQL-skript som laddas ned.

3. Ange **filer** under **uppräknare configuration** till `*.usql` så att behållaren loop fångar endast de filer som slutar med `.usql`.

    ![Konfigurera Foreach-Loop-behållaren](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. I **variabeln mappningar** lägger du till en användardefinierad variabel för att få namnet på filen för varje U-SQL-fil. Ange den **Index** på 0 för att erhålla filnamnet. I det här exemplet definierar du en variabel med namnet `User::FileName`. Den här variabeln används för att hämta filanslutning för U-SQL-skript och ange namnet på U-SQL-jobb i Azure Data Lake Analytics uppgiften dynamiskt.

    ![Konfigurera Foreach-Loop behållare för att hämta filnamnet](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurera Azure Data Lake Analytics-uppgift 

1. Ange **SourceType** till **FileConnection**.

2. Ange **FileConnection** till filen anslutning som pekar på filen objekt returnerades från Foreach-Loop behållare.
    
    Skapa den här filen-anslutningen:

   1. Välj  **\<ny anslutning... >** i FileConnection inställningen.
   2. Ange **användningstyp** till **befintlig fil**, och ange den **filen** till en befintlig fil filsökväg.

       ![Konfigurera Foreach-Loop-behållaren](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. I **anslutning chefer** visa, högerklicka på filen anslutningen skapas just nu och välj **egenskaper**.

   4. I den **egenskaper** fönstret expanderar **uttryck**, och Ställ in **ConnectionString** till den variabel som anges i Foreach-Loop behållare, till exempel `@[User::FileName]`.

       ![Konfigurera Foreach-Loop-behållaren](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Ange **AzureDataLakeAnalyticsConnection** till Azure Data Lake Analytics-kontot som du vill skicka jobb till. Läs mer om [Anslutningshanteraren för Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Ange andra jobbkonfigurationer. [Läs mer](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Använd **uttryck** dynamiskt ange jobbnamn för U-SQL:

    1. I **uttryck** lägger du till ett nytt uttryck nyckel / värde-par för **JobName**.
    2. Ange värdet för JobName till den variabel som anges i Foreach-Loop behållare, till exempel `@[User::FileName]`.
    
        ![Konfigurera SSIS-uttryck för U-SQL-Jobbnamnet](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenario 3 – Använd U-SQL-filer i Azure Blob Storage

Du kan använda U-SQL-filer i Azure Blob Storage med hjälp av **Azure Blob hämta uppgift** i Azure Feature Pack. Den här metoden kan du använda skripten i molnet.

Stegen är liknande med [Scenario 2: Använda U-SQL-filer i Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Ändra systemaktivitet för Azure Data Lake Store-filen till Azure Blob Download uppgift. [Läs mer om Azure Blob hämta uppgift](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Kontrollflödet är som nedan.

![Använda U-SQL-filer i Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenario 4 Använd U-SQL-filer på den lokala datorn

Förutom för att använda U-SQL-filer som lagras i molnet, kan du också använda filer på den lokala datorn eller filer som distribueras med dina SSIS-paket.

1. Högerklicka på **anslutning chefer** i SSIS programprojektet och väljer **nya Anslutningshanteraren**.

2. Välj **filen** skriver och klickar på **Lägg till...** .

3. Ange **användningstyp** till **befintlig fil**, och ange den **filen** till filen på den lokala datorn.

    ![Lägga till filen anslutning till den lokala filen](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Lägg till **Azure Data Lake Analytics** uppgift och:
    1. Ange **SourceType** till **FileConnection**.
    2. Ange **FileConnection** till filen anslutningen skapas just nu.

5. Avsluta andra konfigurationer för Azure Data Lake Analytics-aktivitet.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenario 5 – Använd U-SQL-instruktionen i SSIS-variabel

I vissa fall kan behöva du dynamiskt generera U-SQL-uttryck. Du kan använda **SSIS variabeln** med **SSIS uttryck** och andra SSIS aktiviteter, till exempel skript-aktivitet för att dynamiskt skapa U-SQL-instruktionen.

1. Öppna variabler verktygsfönster via **SSIS > variabler** översta menyn.

2. Lägg till en SSIS-variabel och ställer in värdet direkt eller använda **uttryck** att generera värdet.

3. Lägg till **Azure Data Lake Analytics uppgift** och:
    1. Ange **SourceType** till **variabeln**.
    2. Ange **SourceVariable** till SSIS-variabeln ska skapas just nu.

4. Avsluta andra konfigurationer för Azure Data Lake Analytics-aktivitet.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenario 6 – skicka parametrar till U-SQL-skript

I vissa fall kan du dynamiskt ange variabelvärdet U-SQL i U-SQL-skript. **Parametermappning** funktion i Azure Data Lake Analytics uppgift hjälp med det här scenariot. Det är vanligtvis två typiska användare fall:

- Ställ in indata och utdata filen sökvägsvariabler dynamiskt baserat på aktuellt datum och tid.
- Ange parametern för lagrade procedurer.

[Mer information om hur du ställer in parametrarna för U-SQL-skriptet](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Nästa steg

- [Köra SSIS-paket i Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure Feature Pack för Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Schemalägga U-SQL-jobb med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

