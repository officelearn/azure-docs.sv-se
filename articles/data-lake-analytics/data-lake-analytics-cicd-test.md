---
title: Så här testar du din Azure Data Lake Analytics-kod | Microsoft Docs
description: Lär dig hur du lägger till testfall för U-SQL och utökade C#-kod för Azure Data Lake Analytics.
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
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889674"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Så här testar du din Azure Data Lake Analytics-kod

Azure Data Lake innehåller U-SQL som ett språk som kombinerar deklarativt Frågespråk med tvingande C# för att bearbeta data i valfri skala. I det här dokumentet lär du dig att skapa testfall för U-SQL och utökade C# UDO (användardefinierade operatorn)-kod.

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

U-SQL-skriptet kompileras och som optimerats för körbar kod och köra över datorer i molnet eller den lokala datorn. Processen för kompilering och optimering av behandlar hela U-SQL-skriptet som helhet att köra. Du kan inte göra traditionella ”enhetstest” för varje instruktion. Med U-SQL-testning SDK och lokal körning SDK kan du göra skript på tester.

### <a name="create-test-cases-for-u-sql-script"></a>Skapa testfall för U-SQL-skript

Azure Data Lake Tools för Visual Studio är det bra om du vill skapa testfall för U-SQL-skript.

1.  Högerklicka på ett U-SQL-skript i Solution Explorer och välj **skapa enhetstest**.
2.  Konfigurera om du vill skapa ett nytt projekt för testning eller infoga testfall till ett befintligt testprojekt.

    ![Data Lake Tools för Visual Studio skapa projekt u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools för Visual Studio skapar projektkonfigurationen för u-sql-test](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Hantera test-datakälla

När testa U-SQL-skript, test indatafilerna behövs. Du kan hantera dessa testdata genom att konfigurera **testa datakällan** projektegenskap i U-SQL. När du anropar den `Initialize()` gränssnittet i U-SQL-testet SDK, en temporär lokal Data rotmapp skapas under arbetskatalogen för testprojekt och alla filer och undermappar (och filer under undermappar) i källmappen test data kopieras till tillfälligt lokala data rotmapp innan du kör testfall för U-SQL-skript. Du kan lägga till fler test källa mappar genom spotta test data-mappsökväg med semikolon.

![Data Lake Tools för Visual Studio konfigurera projektet test-datakälla](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Hantera databasmiljö för testning

Om dina U-SQL-skript använder eller fråga med U-SQL database-objekt, till exempel anropa lagrade procedurer, måste du initiera databasmiljön innan du kör U-SQL-testfall. Den `Initialize()` gränssnittet i U-SQL-testet SDK hjälper dig att distribuera alla databaser som refereras av U-SQL-projektet till tillfällig lokala Data rotmappen i arbetskatalogen för testprojekt. 

Läs mer om [hur du kan hantera U-SQL-databas projekt referenser för U-SQL-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Kontrollera testresultat

Den `Run()` gränssnittet returnerar jobbet Körningsresultat, 0 innebär att lyckas och 1 innebär misslyckades. Du kan också använda C# assert-funktioner för att verifiera utdata. 

### <a name="execute-test-cases-in-visual-studio"></a>Köra testfall i Visual Studio

Testprojekt för U-SQL-skriptet är byggt på Testramverk för C#-enhet. Efter att bygga projektet, du kan köra alla testfall **testa Explorer > spellistan**, eller högerklicka på filen .cs och välj **kör tester**.

## <a name="test-c-udos"></a>Testa C# UDO

### <a name="create-test-cases-for-c-udos"></a>Skapa testfall för C# UDO

Du kan använda Testramverk för C#-enhet för att testa ditt C#-UDOs(User-Defined Operator). När du testar UDO, måste du förbereda motsvarande **IRowset** objekt som indata.

Det finns två sätt att skapa IRowset:

1.  Läsa in data från en fil som ska skapas IRowset

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

2.  Använda data från insamling av data för att skapa IRowset

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

När du anropar UDO-funktioner kan kontrollera du resultatet via schema- och raduppsättningen värdet verifiering med hjälp av C# Assert-funktioner. Exempelkoden kan vara i U-SQL C# UDO enhet Test exempelprojektet via **fil > Nytt > projekt** i Visual Studio.

### <a name="execute-test-cases-in-visual-studio"></a>Köra testfall i Visual Studio

När build testprojekt, du kan köra alla testfall dock **testa Explorer > spellistan**, eller högerklicka på filen .cs och välj **kör tester**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Kör testfall i Visual Studio Team Service

Båda **testprojekt för U-SQL-skriptet** och **C# UDO testprojekt** ärver testprojekt för C#-enhet. [Visual Studio Test uppgift](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) i Visual Studio Team Service kan köra dessa testfall. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Kör U-SQL-testfall i Visual Studio Team Service

För U-SQL-testning, kontrollera att du läser in `CPPSDK` på build-datorn och pass den `CPPSDK` sökvägen till USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Vad är CPPSDK?**

CPPSDK är ett paket som innehåller Microsoft Visual C++-14 och Windows SDK 10.0.10240.0, vilket är den miljö som krävs av U-SQL-körning. Du kan få det här paketet under Azure Data Lake Tools för Visual Studio-installationsmappen:

- För Visual Studio 2015 är det under `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- För Visual Studio 2017 är det under `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Så här förbereder du CPPSDK i Visual Studio Team Service skapandeagent**

Det vanliga sättet för att förbereda CPPSDK sambandet i Visual Studio Team Service är:

1.  ZIP mappen innehåller CPPSDK bibliotek.
2.  Kontrollera i zip-filen till ditt källkontrollsystem. (Zip-filen kan kontrollera att du checkar in alla bibliotek CPPSDK i mappen eller vissa filer kommer att ignoreras av ”.gitignore”.)
3.  Packa upp zip-filen i Build-pipeline.
4.  Punkt `USqlScriptTestRunner` till den här uppzippade mappen på build-datorn.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Kör C# UDO testfall i Visual Studio Team Service

Se till att referera till nedan sammansättningar som behövs för Katalogentiteter för C# UDO-test. Om du refererar till dem via [Nuget-paketet Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), se till att du lägger till en NuGet återställa uppgift i build-pipeline.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Nästa steg

- [Hur du ställer in CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
- [Använd U-SQL database-projekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md)

