---
title: Så här testar du din Azure Data Lake Analytics-kod
description: Lär dig hur du lägger till testfall för U-SQL och utökade C#-kod för Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: ae34355485f7d5081cc11ce4dd36df5ba81ae320
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041236"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testa din Azure Data Lake Analytics-kod

Azure Data Lake innehåller U-SQL-språk, som kombinerar deklarativt Frågespråk med tvingande C# för att bearbeta data i valfri skala. I det här dokumentet lär du dig att skapa testfall för U-SQL och utökade C# UDO (användardefinierade operatorn)-kod.

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

U-SQL-skriptet kompileras och optimerade för körbar kod kan köras på datorer i molnet eller på den lokala datorn. Processen för kompilering och optimering av behandlar hela U-SQL-skriptet som helhet. Du kan inte göra en traditionell ”enhetstest” för varje instruktion. Testa dock SDK med hjälp av U-SQL och den lokala kör SDK, som du kan göra skript på servernivå tester.

### <a name="create-test-cases-for-u-sql-script"></a>Skapa testfall för U-SQL-skript

Azure Data Lake Tools för Visual Studio kan du skapa testfall för U-SQL-skript.

1.  Högerklicka på ett U-SQL-skript i Solution Explorer och välj sedan **skapa enhetstest**.
2.  Skapa ett nytt projekt för testning eller infoga testfall i ett befintligt testprojekt.

    ![Data Lake Tools för Visual Studio – skapa ett testprojekt för U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools för Visual Studio – skapa en projektkonfiguration för U-SQL-test](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Hantera test-datakälla

När du testar U-SQL-skript, måste du testa indatafiler. Du kan hantera testdata genom att konfigurera **testa datakällan** projektet egenskaper i U-SQL. 

När du anropar den `Initialize()` gränssnittet i U-SQL-testet SDK, rotmappen för en tillfällig lokala data skapas under arbetskatalogen i test-projektet och alla filer och undermappar (och filer under undermappar) i källmappen test data kopieras till den temporära lokala data rot mapp innan du kör testfall för U-SQL-skript. Du kan lägga till fler test källa mappar genom att dela test data-mappsökväg med ett semikolon.

![Data Lake Tools för Visual Studio – konfigurera projektet test-datakälla](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Hantera databasmiljö för testning

Om dina U-SQL-skript använder eller fråga med objekt U-SQL-databas (till exempel när du anropar lagrade procedurer) måste du initiera databasmiljön innan du kör U-SQL-testfall. Den `Initialize()` gränssnittet i U-SQL-testet SDK hjälper dig att distribuera alla databaser som refereras av U-SQL-projektet till rotmappen temporära lokala data i arbetskatalogen i test-projektet. 

Läs mer om [hantera projektreferenserna för U-SQL-databas för ett U-SQL-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Kontrollera testresultat

Den `Run()` gränssnittet returnerar ett resultat för körning av jobbet. 0 betyder, eller 1 innebär att misslyckas. Du kan också använda C# assert-funktioner för att verifiera utdata. 

### <a name="run-test-cases-in-visual-studio"></a>Kör testfall i Visual Studio

Ett testprojekt för U-SQL-skriptet är byggt på en C# enhet Testramverk. När du har skapat projektet kan du köra alla testfall via **testa Explorer > spellistan**. Du kan också högerklicka på filen .cs och välj sedan **kör tester**.

## <a name="test-c-udos"></a>Testa C# UDO

### <a name="create-test-cases-for-c-udos"></a>Skapa testfall för C# UDO

Du kan använda en C# enhet Testramverk för att testa ditt C#-Katalogentiteter (användardefinierade operatörer). När du testar UDO, måste du förbereda motsvarande **IRowset** objekt som indata.

Det finns två sätt att skapa ett IRowset objekt:

- Läsa in data från en fil för att skapa IRowset:

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

- Använd data från en insamling av data för att skapa IRowset:

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

### <a name="verify-test-results"></a>Kontrollera testresultat

När du anropa UDO-funktioner måste kontrollera du resultat via schema- och verifiering av raduppsättningen värde med hjälp av C# assert-funktioner. Du kan använda exempelkoden i ett U-SQL C# UDO enhet test exempelprojektet via **fil > Nytt > projekt** i Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Kör testfall i Visual Studio

När du bygger ett testprojekt kan du köra alla testfall dock **testa Explorer > spellistan**, eller högerklicka på filen .cs och välj **kör tester**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Kör testfall i Visual Studio Team Service

Båda **testprojekt för U-SQL-skriptet** och **C# UDO testprojekt** ärver testprojekt för C#-enhet. Den [Visual Studio test uppgift](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) i Visual Studio Team Services kan köra dessa testfall. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Kör U-SQL-testfall i Visual Studio Team Service

Kontrollera att du läser in för ett U-SQL-prov `CPPSDK` på build-datorn, och sedan pass den `CPPSDK` sökvägen till USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Vad är CPPSDK?**

CPPSDK är ett paket som innehåller Microsoft Visual C++-14 och Windows SDK 10.0.10240.0. Det här är den miljö som behövs av U-SQL-körningen. Du kan få det här paketet under Azure Data Lake Tools för Visual Studio-installationsmappen:

- För Visual Studio 2015 är det under `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- För Visual Studio 2017 är det under `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Förbereda CPPSDK i Visual Studio Team Services build-agent**

Det vanligaste sättet att förbereda CPPSDK beroendet i Visual Studio Team Service är följande:

1.  ZIP-mappen som innehåller CPPSDK-bibliotek.
2.  Kontrollera i ZIP-filen till ditt källkontrollsystem. (.Zip-filen säkerställer att du kontrollerar alla bibliotek under mappen CPPSDK så att vissa filer inte ignoreras av ”.gitignore”).   
3.  Packa upp .zip-filen i build-pipeline.
4.  Punkt `USqlScriptTestRunner` till den här uppzippade mappen på build-datorn.

### <a name="run-c-udo-test-cases-in-visual-studio-team-services"></a>Kör C# UDO testfall i Visual Studio Team Services

Se till att referera till följande sammansättningar, vilket behövs för Katalogentiteter för ett C# UDO-test. Om du refererar till dem via [Nuget-paketet Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), se till att du lägger till en NuGet återställa uppgift i build-pipeline.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Nästa steg

- [Hur du ställer in CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
- [Använd U-SQL database-projekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md)

