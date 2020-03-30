---
title: Kör U-SQL-jobb lokalt - Azure Data Lake U-SQL SDK
description: Lär dig hur du kör och testar U-SQL-jobb lokalt med hjälp av kommandoraden och programmeringsgränssnitten på din lokala arbetsstation.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309687"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Köra och testa U-SQL med Azure Data Lake U-SQL SDK

När du utvecklar U-SQL-skript är det vanligt att köra och testa U-SQL-skript lokalt innan du skickar det till molnet. Azure Data Lake tillhandahåller ett Nuget-paket som heter Azure Data Lake U-SQL SDK för det här scenariot, genom vilket du enkelt kan skala U-SQL-körning och test. Det är också möjligt att integrera detta U-SQL-test med CI (Continuous Integration) system för att automatisera kompileringen och testet.

Om du bryr dig om hur du manuellt kör och felsöker U-SQL-skript med GUI-verktyg kan du använda Azure Data Lake Tools för Visual Studio för det. Du kan läsa mer [härifrån.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="install-azure-data-lake-u-sql-sdk"></a>Installera Azure Data Lake U-SQL SDK

Du kan få Azure Data Lake U-SQL SDK [här](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) på Nuget.org. Och innan du använder den måste du se till att du har beroenden enligt följande.

### <a name="dependencies"></a>Beroenden

Data Lake U-SQL SDK kräver följande beroenden:

- [Microsoft .NET Framework 4.6 eller nyare](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 och Windows SDK 10.0.10240.0 eller nyare (som kallas CppSDK i den här artikeln). Det finns två sätt att få CppSDK:

  - Installera [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Du har mappen \Windows Kits\10 under mappen Program – till exempel C:\Program-filer (x86)\Windows Kits\10\. Du hittar också Windows 10 SDK-versionen under \Windows Kits\10\Lib. Om du inte ser dessa mappar installerar du om Visual Studio och väljer Windows 10 SDK under installationen. Om du har installerat detta med Visual Studio hittar den lokala kompilatorn U-SQL automatiskt.

    ![Datasjöverktyg för Visual Studio lokalt drivna Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Installera [datasjöverktyg för Visual Studio](https://aka.ms/adltoolsvs). Du hittar de färdigförpackade Visual C++- och Windows SDK-filerna på C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. I det här fallet kan U-SQL lokala kompilatorn inte hitta beroenden automatiskt. Du måste ange CppSDK-sökvägen för den. Du kan antingen kopiera filerna till en annan plats eller använda dem som de är.

## <a name="understand-basic-concepts"></a>Förstå grundläggande begrepp

### <a name="data-root"></a>Datarot

Datarotmappen är ett "lokalt arkiv" för det lokala beräkningskontot. Det motsvarar Azure Data Lake Store-kontot för ett Data Lake Analytics-konto. Att byta till en annan datarotmapp är precis som att byta till ett annat butikskonto. Om du vill komma åt vanliga delade data med olika datarotmappar måste du använda absoluta sökvägar i skripten. Du kan också skapa symboliska länkar i filsystemet (till exempel **mklink** på NTFS) under datarotmappen för att peka på delade data.

Datarotmappen används för att:

- Lagra lokala metadata, inklusive databaser, tabeller, tabellvärderade funktioner (TV-filer) och sammansättningar.
- Slå upp in- och utdatabanorna som definieras som relativa banor i U-SQL. Om du använder relativa sökvägar blir det enklare att distribuera dina U-SQL-projekt till Azure.

### <a name="file-path-in-u-sql"></a>Sökväg till fil i U-SQL

Du kan använda både en relativ sökväg och en lokal absolut sökväg i U-SQL-skript. Den relativa sökvägen är relativ till den angivna sökvägen till datarotmappen. Vi rekommenderar att du använder "/" som sökvägsavgränsare för att göra skripten kompatibla med serversidan. Här är några exempel på relativa banor och deras motsvarande absoluta vägar. I de här exemplen är C:\LocalRunDataRoot datarotmappen.

|Relativ sökväg|Absolut väg|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Arbetskatalog

När U-SQL-skriptet körs lokalt skapas en arbetskatalog under kompileringen under aktuell katalog som körs. Förutom kompileringsutdata kopieras de nödvändiga körningsfilerna för lokal körning till den här arbetskatalogen. Arbetskatalogrotmappen kallas "ScopeWorkDir" och filerna under arbetskatalogen är följande:

|Katalog/fil|Katalog/fil|Katalog/fil|Definition|Beskrivning|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hash-sträng med runtime-version|Skuggkopia av körningsfiler som behövs för lokal körning|
| |Script_66AE4909AA0ED06C| |Skriptnamn + hash-sträng med skriptsökväg|Loggning av kompileringsutdata och körningsstegloggning|
| | |\_skriptet\_.abr|Kompilatorutdata|Algebra-fil|
| | |\_ScopeCodeGen\_.*|Kompilatorutdata|Genererad hanterad kod|
| | |\_ScopeCodeGenEngine\_.*|Kompilatorutdata|Genererad inbyggd kod|
| | |refererade sammansättningar|Sammansättningsreferens|Refererade sammansättningsfiler|
| | |deployed_resources|Resursdistribution|Resursdistributionsfiler|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Körningslogg|Logga körningssteg|


## <a name="use-the-sdk-from-the-command-line"></a>Använda SDK från kommandoraden

### <a name="command-line-interface-of-the-helper-application"></a>Kommandoradsgränssnitt för hjälpprogrammet

Under SDK-katalog\build\runtime är LocalRunHelper.exe kommandoradshjälpprogrammet som tillhandahåller gränssnitt till de flesta vanliga lokala funktioner. Observera att både kommandot och argumentväxarna är skiftlägeskänsliga. Så här anropar du den:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Kör LocalRunHelper.exe utan argument **help** eller med hjälpväxeln för att visa hjälpinformationen:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

I hjälpinformationen:

-  **Kommandot** ger kommandots namn.  
-  **Obligatoriskt argument** listar argument som måste anges.  
-  **Valfritt argument** visar argument som är valfria, med standardvärden.  Valfria booleska argument har inga parametrar och deras utseende betyder negativt för deras standardvärde.

### <a name="return-value-and-logging"></a>Returvärde och loggning

Hjälpprogrammet returnerar **0** för lyckad och **-1** för fel. Som standard skickar hjälparen alla meddelanden till den aktuella konsolen. De flesta kommandon stöder dock **-MessageOut path_to_log_file** valfria argument som omdirigerar utdata till en loggfil.

### <a name="environment-variable-configuring"></a>Miljövariabel konfigurera

U-SQL lokal körning behöver en angiven datarot som lokalt lagringskonto, samt en angiven CppSDK-sökväg för beroenden. Du kan både ange argumentet i kommandoraden eller ange miljövariabel för dem.

- Ange **variabeln SCOPE_CPP_SDK** miljö.

    Om du får Microsoft Visual C++ och Windows SDK genom att installera DataSjöverktyg för Visual Studio kontrollerar du att du har följande mapp:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definiera en ny miljövariabel som kallas **SCOPE_CPP_SDK** för att peka på den här katalogen. Eller kopiera mappen till den andra platsen och ange **SCOPE_CPP_SDK** som det.

    Förutom att ange miljövariabeln kan du ange argumentet **-CppSDK** när du använder kommandoraden. Det här argumentet skriver över din standard-CppSDK-miljövariabel.

- Ange **variabeln LOCALRUN_DATAROOT** miljö.

    Definiera en ny miljövariabel som kallas **LOCALRUN_DATAROOT** som pekar på dataroten.

    Förutom att ange miljövariabeln kan du ange **argumentet -DataRoot** med datarotsökvägen när du använder en kommandorad. Det här argumentet skriver över standardvariabeln för datarotmiljö. Du måste lägga till det här argumentet på varje kommandorad som du kör så att du kan skriva över standardvariabeln för datarotmiljö för alla åtgärder.

### <a name="sdk-command-line-usage-samples"></a>Användningsexempel för SDK-kommandorad

#### <a name="compile-and-run"></a>Kompilera och kör

**Kommandot kör** används för att kompilera skriptet och sedan köra kompilerade resultat. Dess kommandoradsargument är en kombination av argument från **kompilering** och **körning**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Följande är valfria argument för **körning:**


|Argument|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|-KodBänd|False|Skriptet har .cs-kod bakom|
|-CppSDK| |CppSDK-katalog|
|-DataRoot (DataRoot)| Variabel för DataRoot-miljö|DataRoot för lokal körning, som standard till LOCALRUN_DATAROOT-miljövariabeln|
|-MessageOut| |Dumpa meddelanden på konsolen till en fil|
|-Parallell|1|Kör planen med den angivna parallellismen|
|-Referenser| |Lista över sökvägar till extra referensenheter eller datafiler med kod bakom, avgränsade med ';'|
|-UdoRedirect|False|Generera konfiguration av omdirigering av Udo-montering|
|-AnvändDatabas|master|Databas som ska användas för kod bakom tillfällig monteringsregistrering|
|-Utförlig|False|Visa detaljerade utdata från körning|
|-WorkDir|Aktuell katalog|Katalog för kompilatoranvändning och utdata|
|-RunScopeCEP|0|ScopeCEP-läge som ska användas|
|-ScopeCEPTempPath|Temp|Temp-sökväg som ska användas för strömmande data|
|-OptFlags| |Kommaavgränsad lista över optimerarflaggor|


Här är ett exempel:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Förutom att kombinera **kompilering** och **körning**kan du kompilera och köra de kompilerade körbara filer separat.

#### <a name="compile-a-u-sql-script"></a>Kompilera ett U-SQL-skript

**Kompileringskommandot** används för att kompilera ett U-SQL-skript till körbara filer.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Följande är valfria argument för **kompilering:**


|Argument|Beskrivning|
|--------|-----------|
| -CodeBehind [standardvärde 'Falskt']|Skriptet har .cs-kod bakom|
| -CppSDK [standardvärde '']|CppSDK-katalog|
| -DataRoot [standardvärde "DataRoot-miljövariabeln"]|DataRoot för lokal körning, som standard till LOCALRUN_DATAROOT-miljövariabeln|
| -MessageOut [standardvärde '']|Dumpa meddelanden på konsolen till en fil|
| -Referenser [standardvärde'']|Lista över sökvägar till extra referensenheter eller datafiler med kod bakom, avgränsade med ';'|
| -Shallow [standardvärde 'Falskt']|Grunt kompilera|
| -UdoRedirect [standardvärde 'Falskt']|Generera konfiguration av omdirigering av Udo-montering|
| -UseDatabase [standardvärdet 'master']|Databas som ska användas för kod bakom tillfällig monteringsregistrering|
| -WorkDir [standardvärde "Aktuell katalog"]|Katalog för kompilatoranvändning och utdata|
| -RunScopeCEP [standardvärde '0']|ScopeCEP-läge som ska användas|
| -ScopeCEPTempPath [standardvärdet 'temp']|Temp-sökväg som ska användas för strömmande data|
| -OptFlags [standardvärde '']|Kommaavgränsad lista över optimerarflaggor|


Här är några användningsexempel.

Kompilera ett U-SQL-skript:

    LocalRunHelper compile -Script d:\test\test1.usql

Kompilera ett U-SQL-skript och ange datarotmappen. Observera att detta kommer att skriva över den inställda miljövariabeln.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Kompilera ett U-SQL-skript och ange en arbetskatalog, referenssammansättning och databas:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Kör kompilerade resultat

**Körningskommandot** används för att köra kompilerade resultat.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Följande är valfria argument för **körning:**

|Argument|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|-DataRoot (DataRoot) | '' |Datarot för körning av metadata. Det är standard till **LOCALRUN_DATAROOT** miljövariabeln.|
|-MessageOut | '' |Dumpa meddelanden på konsolen till en fil.|
|-Parallell | '1' |Indikator för att köra de genererade lokala stegen med den angivna parallellitetsnivån.|
|-Utförlig | "Falskt" |Indikator för att visa detaljerade utdata från körning.|

Här är ett användningsexempel:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Använda SDK med programmeringsgränssnitt

Programmeringsgränssnitten finns alla i LocalRunHelper.exe. Du kan använda dem för att integrera funktionerna i U-SQL SDK och C# testramverket för att skala ditt lokala U-SQL-skripttest. I den här artikeln kommer jag att använda standard C # enhet testprojekt för att visa hur du använder dessa gränssnitt för att testa din U-SQL-skript.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Steg 1: Skapa C# enhet testprojekt och konfiguration

- Skapa ett testprojekt för C#-enheten via File > New > Project > Visual C# > Test > Unit Test Project.
- Lägg till LocalRunHelper.exe som referens för projektet. LocalRunHelper.exe finns på paketet \build\runtime\LocalRunHelper.exe i Nuget.

    ![Azure Data Lake U-SQL SDK Add Reference](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK stöder **endast** x64-miljö, se till att ställa in byggplattformsmål som x64. Du kan ange det via Project Property > Build > Platform target.

    ![Azure Data Lake U-SQL SDK Konfigurera x64-projekt](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Se till att ställa in testmiljön som x64. I Visual Studio kan du ställa in den via Test > Testinställningar > Standardprocessorarkitektur > x64.

    ![Azure Data Lake U-SQL SDK Konfigurera x64-testmiljö](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Se till att kopiera alla beroendefiler under NugetPackage\build\runtime\ till projektarbetskatalog som vanligtvis finns under ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Steg 2: Skapa testfall för U-SQL-skript

Nedan visas exempelkoden för U-SQL-skripttest. För testning måste du förbereda skript, indatafiler och förväntade utdatafiler.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Programmeringsgränssnitt i LocalRunHelper.exe

LocalRunHelper.exe tillhandahåller programmeringsgränssnitt för U-SQL lokal kompilering, körning, etc. Gränssnitten visas på följande sätt.

**Konstruktor**

offentliga LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parameter|Typ|Beskrivning|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|för utdatameddelanden, inställd på null för att använda Konsol|

**Egenskaper**

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|AlgebraPath (algebrapath)|sträng|Sökvägen till algebra-filen (algebrafil är ett av kompileringsresultaten)|
|KodBehindReferences|sträng|Om skriptet har ytterligare kod bakom referenser anger du sökvägarna som avgränsas med ';'|
|CppSdkDir|sträng|CppSDK-katalog|
|CurrentDir (aktuellaDir)|sträng|Aktuell katalog|
|DataRoot (DataRoot)|sträng|Datarotsökväg|
|FelsökareMailPath|sträng|Sökvägen till felsökare mailslot|
|GenereraUdoRedirect|bool|Om vi vill generera monteringsladdningsomdirigering åsidosätta config|
|HasCodeBehind (HasCodeBehind)|bool|Om skriptet har kod bakom|
|InputDir|sträng|Katalog för indata|
|MessagePath (meddelandeväg)|sträng|Sökväg till meddelandedumpfil|
|OutputDir|sträng|Katalog för utdata|
|Parallellitet|int|Parallellism för att köra algebra|
|ParentPid|int|PID för den överordnade som tjänsten övervakar att avsluta, inställd på 0 eller negativ för att ignorera|
|Resultpath|sträng|Sökväg till filsökväg för resultatdump|
|RuntimeDir (körtid)|sträng|Körningskatalog|
|ScriptPath (Skriptpath)|sträng|Var hittar man skriptet|
|Grunt|bool|Ytlig kompilering eller inte|
|TempDir (tempdir)|sträng|Temp-katalog|
|AnvändaDataBase|sträng|Ange den databas som ska användas för kod bakom tillfällig monteringsregistrering, huvudsida som standard|
|WorkDir (arbete)|sträng|Önskad arbetskatalog|


**Metod**

|Metod|Beskrivning|Återvända|Parameter|
|------|-----------|------|---------|
|offentliga bool DoCompile()|Kompilera U-SQL-skriptet|Sant om framgång| |
|offentliga bool DoExec()|Kör det kompilerade resultatet|Sant om framgång| |
|offentliga bool DoRun()|Kör U-SQL-skriptet (Kompilera + Kör)|Sant om framgång| |
|offentliga bool IsValidRuntimeDir(strängsökväg)|Kontrollera om den angivna sökvägen är giltig körningssökväg|Sant för giltigt|Sökvägen till körningskatalogen|


## <a name="faq-about-common-issue"></a>Vanliga frågor och svar om vanliga frågor

### <a name="error-1"></a>Fel 1:
E_CSC_SYSTEM_INTERNAL: Internt fel! Det gick inte att läsa in filen eller sammansättningen ScopeEngineManaged.dll eller något av dess beroenden. Det gick inte att hitta den angivna modulen.

Kontrollera följande:

- Kontrollera att du har x64-miljö. Byggmålplattformen och testmiljön ska vara x64, se **steg 1: Skapa C# enhetstestprojekt och konfiguration** ovan.
- Kontrollera att du har kopierat alla beroendefiler under NugetPackage\build\runtime\ till projektarbetskatalogen.


## <a name="next-steps"></a>Nästa steg

* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hur du loggar diagnostikinformation finns i [Komma åt diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* En mer komplex fråga finns i [Analysera webbplatsloggar med Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om hur du visar jobbinformation finns i [Använda jobbwebbläsare och jobbvy för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
* Information om hur du använder hörnkörningsvyn finns [i Använda vertexutförandevyn i DataSjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
