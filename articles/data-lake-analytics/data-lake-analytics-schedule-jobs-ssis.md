---
title: Schemalägga U-SQL-jobb i Azure Data Lake Analytics med SSIS
description: Lär dig hur du använder SQL Server Integration Services för att schemalägga U-SQL-jobb med infogade skript eller från U-SQL-frågefiler.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 0650fcc5023ac57b193fa23b0dedf65113fd64e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672899"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Schemalägga U-SQL-jobb med SQL Server Integration Services (SSIS)

I det här dokumentet får du lära dig hur du dirigerar och skapar U-SQL-jobb med HJÄLP AV SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Krav

[Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) tillhandahåller Azure Data Lake [Analytics-uppgiften](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) och [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) som hjälper till att ansluta till Azure Data Lake Analytics-tjänsten. Om du vill använda den här uppgiften måste du installera:

- [Ladda ned och installera SQL Server Data Tools (SSDT) för Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Installera Azure Feature Pack for Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics-uppgift

Med Azure Data Lake Analytics-uppgiften kan användare skicka U-SQL-jobb till Azure Data Lake Analytics-kontot. 

[Lär dig hur du konfigurerar Azure Data Lake Analytics-uppgift](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Azure Data Lake Analytics-uppgift i SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Du kan hämta U-SQL-skriptet från olika platser med hjälp av SSIS inbyggda funktioner och uppgifter, nedan visar scenarier hur kan du konfigurera U-SQL-skript för olika användarfall.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenario 1-Använd infogad skript samtal tvfs och lagrade procs

I Azure Data Lake Analytics Task Editor konfigurerar du **SourceType** som **DirectInput**och placerar U-SQL-uttrycken i **USQLStatement**.

För enkelt underhåll och kodhantering, bara sätta korta U-SQL-skript som infogade skript, till exempel, kan du anropa befintliga tabell värderade funktioner och lagrade procedurer i dina U-SQL-databaser. 

![Redigera infogat U-SQL-skript i SSIS-uppgift](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Relaterad artikel: [Så här skickar du parametern till lagrade procedurer](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenario 2-Använda U-SQL-filer i Azure Data Lake Store

Du kan också använda U-SQL-filer i Azure Data Lake Store med hjälp av **Azure Data Lake Store File System Task** i Azure Feature Pack. Med den här metoden kan du använda skripten som lagras i molnet.

Följ stegen nedan för att konfigurera anslutningen mellan Azure Data Lake Store File System Task och Azure Data Lake Analytics Task.

### <a name="set-task-control-flow"></a>Ange flöde för uppgiftskontroll

Lägg till en **Azure Data Lake Store-filsystemuppgift**i SSIS-paketdesignvyn, en **Foreach Loop Container** och en Azure Data Lake **Analytics-uppgift** i Foreach Loop Container. Azure Data Lake Store File System Task hjälper till att hämta U-SQL-filer i ditt ADLS-konto till en tillfällig mapp. Foreach Loop Container och Azure Data Lake Analytics Task hjälper till att skicka alla U-SQL-filer under den tillfälliga mappen till Azure Data Lake Analytics-kontot som ett U-SQL-jobb.

![Använda U-SQL-filer i Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurera Azure Data Lake Store-filsystemuppgift

1. Ange **åtgärden** till **CopyFromADLS**.
2. Konfigurera **AzureDataLakeConnection**, läs mer om [Azure Data Lake Store Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Ange **AzureDataLakeDirectory**. Peka på mappen som lagrar dina U-SQL-skript. Använd relativ sökväg som är relativ till rotmappen för Azure Data Lake Store-kontot.
4. Ange **mål** till en mapp som cachelagrar de nedladdade U-SQL-skripten. Den här mappsökvägen används i Foreach Loop Container för U-SQL-jobbinlämning. 

![Konfigurera Azure Data Lake Store-filsystemuppgift](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Läs mer om Azure Data Lake Store File System Task](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfigurera Foreach-loopbehållare

1. På **samlingssidan** anger du **Uppräknare** till **Foreach File Enumerator**.

2. Ange **mapp** under **Konfigurationsgruppen för Uppräknare** till den tillfälliga mappen som innehåller de nedladdade U-SQL-skripten.

3. Ange **filer** under **Uppräkningskonfiguration** så `*.usql` att loopbehållaren bara `.usql`fångar filerna som slutar med .

    ![Konfigurera Foreach-loopbehållare](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. På sidan **Variabel mappningar** lägger du till en användardefinierad variabel för att hämta filnamnet för varje U-SQL-fil. Ange **indexet** till 0 för att hämta filnamnet. I det här exemplet `User::FileName`definierar du en variabel som kallas . Den här variabeln används för att dynamiskt hämta U-SQL-skriptfilsanslutning och ange U-SQL-jobbnamn i Azure Data Lake Analytics Task.

    ![Konfigurera Foreach Loop Container för att hämta filnamn](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurera Azure Data Lake Analytics-uppgift 

1. Ange **SourceType** till **FileConnection**.

2. Ange **FileConnection** till filanslutningen som pekar på de filobjekt som returneras från Foreach Loop Container.
    
    Så här skapar du den här filanslutningen:

   1. Välj ** \<Ny anslutning...>** i fileconnection-inställningen.
   2. Ange **användningstyp** till **befintlig fil**och ange **filen** på en befintlig filsökväg.

       ![Konfigurera Foreach-loopbehållare](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Högerklicka på den filanslutning som skapats just nu i vyn **Anslutningshanterare** och välj **Egenskaper**.

   4. Expandera **uttryck**i fönstret **Egenskaper** och ange **ConnectionString** till den variabel som definierats i Foreach Loop Container, `@[User::FileName]`till exempel .

       ![Konfigurera Foreach-loopbehållare](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Ange **AzureDataLakeAnalyticsConnection** till Azure Data Lake Analytics-kontot som du vill skicka jobb till. Läs mer om [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Ange andra jobbkonfigurationer. [Läs mer](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Använd **uttryck** för att dynamiskt ange U-SQL-jobbnamn:

    1. På sidan **Uttryck** lägger du till ett nytt uttrycksnyckelvärdepar för **JobName**.
    2. Ange värdet för JobName till den variabel som definierats `@[User::FileName]`i Foreach Loop Container, till exempel .
    
        ![Konfigurera SSIS-uttryck för U-SQL-jobbnamn](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenario 3-Använda U-SQL-filer i Azure Blob Storage

Du kan använda U-SQL-filer i Azure Blob Storage med hjälp av **Azure Blob Download Task** i Azure Feature Pack. Med den här metoden kan du använda skripten i molnet.

Stegen liknar [Scenario 2: Använd U-SQL-filer i Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Ändra Azure Data Lake Store File System-uppgiften till Azure Blob Download Task. [Läs mer om Azure Blob Download Task](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Kontrollflödet är som nedan.

![Använda U-SQL-filer i Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenario 4-Använda U-SQL-filer på den lokala datorn

Förutom att använda U-SQL-filer som lagras i molnet kan du också använda filer på din lokala dator eller filer som distribueras med dina SSIS-paket.

1. Högerklicka på **Anslutningsansvariga** i SSIS-projektet och välj **Ny anslutningshanterare**.

2. Välj **Filtyp** och klicka på **Lägg till...**.

3. Ange **användningstyp** till **befintlig fil**och ange **filen** till filen på den lokala datorn.

    ![Lägga till filanslutning till den lokala filen](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Lägg till **Azure Data Lake Analytics-uppgift** och:
    1. Ange **SourceType** till **FileConnection**.
    2. Ange **FileConnection** till filanslutningen som skapats nyss.

5. Slutför andra konfigurationer för Azure Data Lake Analytics Task.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenario 5-Använd U-SQL-uttryck i SSIS-variabel

I vissa fall kan du behöva dynamiskt generera U-SQL-uttryck. Du kan använda **SSIS-variabeln** med **SSIS-uttryck** och andra SSIS-uppgifter, till exempel Skriptuppgift, för att hjälpa dig att generera U-SQL-uttrycket dynamiskt.

1. Öppna verktygsfönstret variabler via **SSIS > Variabler** på den översta menyn.

2. Lägg till en SSIS-variabel och ange värdet direkt eller använd **uttryck** för att generera värdet.

3. Lägg till **Azure Data Lake Analytics-uppgift** och:
    1. Ange **SourceType** till **Variabel**.
    2. Ange **SourceVariable** till SSIS-variabeln som skapats just nu.

4. Slutför andra konfigurationer för Azure Data Lake Analytics Task.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenario 6-Pass-parametrar till U-SQL-skript

I vissa fall kanske du vill ange variabelvärdet U-SQL dynamiskt i U-SQL-skriptet. **Parametermappningsfunktionen** i Azure Data Lake Analytics Task hjälp med det här scenariot. Det finns vanligtvis två typiska användarfall:

- Ange indata- och utdatafilsökvägsvariablerna dynamiskt baserat på aktuellt datum och aktuell tid.
- Ange parametern för lagrade procedurer.

[Läs mer om hur du ställer in parametrar för U-SQL-skriptet](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Nästa steg

- [Kör SSIS-paket i Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure Feature Pack for Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Schemalägga U-SQL-jobb med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
