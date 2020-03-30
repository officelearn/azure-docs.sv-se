---
title: Så här testar du din Azure Data Lake Analytics-kod
description: Lär dig hur du lägger till testfall för U-SQL och utökad C#-kod för Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913961"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testa din Azure Data Lake Analytics-kod

Azure Data Lake ger [U-SQL-språket.](data-lake-analytics-u-sql-get-started.md) U-SQL kombinerar deklarativa SQL med tvingande C # för att bearbeta data i valfri skala. I det här dokumentet får du lära dig hur du skapar testfall för U-SQL- och utökad UDO-kod (User-defined Operator).

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

U-SQL-skriptet kompileras och optimeras för körbar kod som ska köras i Azure eller på din lokala dator. Kompilerings- och optimeringsprocessen behandlar hela U-SQL-skriptet som helhet. Du kan inte göra ett traditionellt enhetstest för varje uttalande. Men genom att använda U-SQL-test SDK och den lokala köra SDK, kan du göra skript-nivå tester.

### <a name="create-test-cases-for-u-sql-script"></a>Skapa testfall för U-SQL-skript

Med Azure Data Lake Tools för Visual Studio kan du skapa testfall för U-SQL-skript.

1. Högerklicka på ett U-SQL-skript i Solution Explorer och välj sedan **Skapa enhetstest**.

1. Skapa ett nytt testprojekt eller infoga testfallet i ett befintligt testprojekt.

   ![DataSjöverktyg för Visual Studio – skapa en U-SQL-testprojektkonfiguration](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Hantera testdatakällan

När du testar U-SQL-skript behöver du testa indatafiler. Om du vill hantera testdata högerklickar du på U-SQL-projektet i **Solution Explorer**och väljer **Egenskaper**. Du kan ange en källa i **Testdatakällan**.

![DataSjöverktyg för Visual Studio – konfigurera datakälla för projekttest](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

När du `Initialize()` anropar gränssnittet i U-SQL-test-SDK skapas en tillfällig lokal datarotmapp under testprojektets arbetskatalog. Alla filer och mappar i testdatakällmappen kopieras till den tillfälliga lokala datarotmappen innan du kör testfallen för U-SQL-skript. Du kan lägga till fler testdatakällmappar genom att dela upp sökvägen till testdatamappen med ett semikolon.

### <a name="manage-the-database-environment-for-testing"></a>Hantera databasmiljön för testning

Om dina U-SQL-skript använder eller frågar med U-SQL-databasobjekt måste du initiera databasmiljön innan du kör U-SQL-testfall. Den här metoden kan vara nödvändig när du anropar lagrade procedurer. Gränssnittet `Initialize()` i U-SQL-test SDK hjälper dig att distribuera alla databaser som refereras av U-SQL-projektet till den tillfälliga lokala datarotmappen i testprojektets arbetskatalog.

Mer information om hur du hanterar U-SQL-databasprojektreferenser för ett U-SQL-projekt finns i Referera till [ett U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verifiera testresultat

Gränssnittet `Run()` returnerar ett resultat för jobbkörning. *0* betyder framgång, och *1* betyder misslyckande. Du kan också använda C# assert-funktioner för att verifiera utgångarna.

### <a name="run-test-cases-in-visual-studio"></a>Kör testfall i Visual Studio

Ett testprojekt för U-SQL-skript bygger ovanpå ett testramverk för C#-enheten. När du har byggt projektet väljer du **Testa** > **Utforskaren**för**Windows.** >  Du kan köra testfall från **Test Explorer**. Du kan också högerklicka på CS-filen i enhetstestet och välja **Kör tester**.

## <a name="test-c-udos"></a>Testa C# UDOs

### <a name="create-test-cases-for-c-udos"></a>Skapa testfall för C# UDOs

Du kan använda ett C#-testramverk för att testa dina C#-användardefinierade operatorer. När du testar osa måste du förbereda motsvarande **IRowset-objekt** som indata.

Det finns två sätt att skapa ett **IRowset-objekt:**

- Läsa in data från en fil för att skapa **IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Använd data från en datainsamling för att skapa **IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verifiera testresultat

När du har anropat UDO-funktioner kan du verifiera resultaten via schema- och Rowset-värdeverifieringen med hjälp av C# assert-funktioner. Du kan lägga till ett **U-SQL C# UDO Unit Test Project** i din lösning. Det gör du genom att välja **Fil > Nytt > Projekt** i Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Kör testfall i Visual Studio

När du har byggt projektet väljer du **Testa** > **Utforskaren**för**Windows.** >  Du kan köra testfall från **Test Explorer**. Du kan också högerklicka på CS-filen i enhetstestet och välja **Kör tester**.

## <a name="run-test-cases-in-azure-pipelines"></a>Köra testfall i Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Både **U-SQL script testprojekt** och **C # UDO testprojekt** ärver C # enhet testprojekt. [Testuppgiften för Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) i Azure Pipelines kan köra dessa testfall.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Köra U-SQL-testfall i Azure Pipelines

För ett U-SQL-test kontrollerar du att du läser in `CPPSDK` på din byggdator och sedan skickar `CPPSDK` sökvägen till `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`.

#### <a name="what-is-cppsdk"></a>Vad är CPPSDK?

CPPSDK är ett paket som innehåller Microsoft Visual C++ 14 och Windows SDK 10.0.10240.0. Det här paketet innehåller den miljö som behövs av U-SQL-körningen. Du kan hämta det här paketet under installationsmappen för Azure Data Lake Tools för Visual Studio:

- För Visual Studio 2015 är det under`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- För Visual Studio 2017 är det under`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- För Visual Studio 2019 är det under`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Förbered CPPSDK i Azure Pipelines-byggagenten

Det vanligaste sättet att förbereda CPPSDK-beroendet i Azure Pipelines är följande:

1. Zip mappen som innehåller CPPSDK-biblioteken.

1. Checka in ZIP-filen till källkontrollsystemet. Zip-filen ser till att du checkar in alla bibliotek under CPPSDK-mappen så `.gitignore` att filer inte ignoreras på grund av en fil.

1. Packa upp ZIP-filen i byggpipelinen.

1. Peka `USqlScriptTestRunner` på den här uppackade mappen på byggdatorn.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Kör C# UDO-testfall i Azure Pipelines

För ett C# UDO-test, se till att referera till följande sammansättningar, som behövs för UDOs.

- Microsoft.Analytics.Interfaces
- Typer av Microsoft.Analytics.
- Microsoft.Analytics.UnitTest

Om du refererar till dem via [Nuget-paketet Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)kontrollerar du att du lägger till en NuGet Restore-uppgift i byggpipeline.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Kör U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md)
- [Använda U-SQL-databasprojekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md)
