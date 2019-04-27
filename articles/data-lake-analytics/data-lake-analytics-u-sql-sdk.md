---
title: Kör och testa U-SQL-jobb lokalt med Azure Data Lake U-SQL-SDK
description: Lär dig hur du kör och testa U-SQL-jobb lokalt från kommandoraden och programmeringsgränssnitt på den lokala arbetsstationen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 14908225e78b79cb748e712ae23643ddde4a4242
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813549"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Kör och testa U-SQL med Azure Data Lake U-SQL SDK

När du utvecklar U-SQL-skript, är det vanligt att köra och testa U-SQL-skript lokalt den skickas innan den till molnet. Azure Data Lake innehåller ett Nuget-paket som heter Azure Data Lake U-SQL SDK: N för det här scenariot, via som du kan enkelt skala kör U-SQL och testning. Det är också möjligt att integrera den här U-SQL-test med CI (kontinuerlig integrering) system att automatisera kompileringen och testa.

Om du bryr dig om hur för att manuellt lokala köra och felsöka U-SQL-skript med GUI-verktyg kan du använda Azure Data Lake Tools för Visual Studio för att. Du kan lära dig mer från [här](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Installera Azure Data Lake U-SQL SDK

Du kan hämta Azure Data Lake U-SQL SDK [här](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) på Nuget.org. Och innan du använder den, måste du kontrollera att du har beroenden på följande sätt.

### <a name="dependencies"></a>Beroenden

Data Lake U-SQL SDK: N kräver följande beroenden:

- [Microsoft .NET Framework 4.6 eller senare](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++-14 och Windows SDK 10.0.10240.0 eller senare (som kallas CppSDK i den här artikeln). Det finns två sätt att hämta CppSDK:

  - Installera [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Du har en \Windows Kits\10 mapp under mappen program – till exempel C:\Program Files (x86) \Windows Kits\10\. Du hittar också Windows 10 SDK-version under \Windows Kits\10\Lib. Om du inte ser dessa mappar, installera om Visual Studio och se till att välja Windows 10 SDK under installationen. Om du har det installerat med Visual Studio, ska lokal U-SQL-kompilatorn hitta den automatiskt.

    ![Data Lake Tools för Visual Studio körs lokalt SDK för Windows 10](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Installera [Data Lake Tools för Visual Studio](https://aka.ms/adltoolsvs). Du kan hitta förpaketerade Visual C++ och Windows SDK filer i C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. I det här fallet hittar lokal U-SQL-kompilatorn beroenden automatiskt. Du måste ange CppSDK sökvägen för den. Du kan kopiera filer till en annan plats, eller så kan du använda det som det är.

## <a name="understand-basic-concepts"></a>Förstå grundläggande begrepp

### <a name="data-root"></a>Dataroten

Data-rotmappen är ett ”lokalt Arkiv” för den lokala beräkningskonto. Det motsvarar att Azure Data Lake Store-konto för ett Data Lake Analytics-konto. Växla till en annan data-rotmappen är precis som att byta till ett annat store-konto. Om du vill komma åt ofta delade data med olika data-rotmappar, måste du använda absoluta sökvägar i dina skript. Skapa symboliska länkar för file system (till exempel **mklink** på NTFS) under data till rotmappen så att den pekar till delade data.

Data-rotmappen används för att:

- Store lokala metadata, inklusive databaser, tabeller, tabellvärdesfunktioner (Tabellvärdesfunktioner) och sammansättningar.
- Leta upp de inkommande och utgående sökvägar som har definierats som relativa sökvägar i U-SQL. Använda relativa sökvägar gör det enklare att distribuera dina U-SQL-projekt till Azure.

### <a name="file-path-in-u-sql"></a>Sökväg i U-SQL

Du kan använda både en relativ sökväg och en lokal absolut sökväg i U-SQL-skript. Den relativa sökvägen är i förhållande till angivna data-sökvägen till rotmappen. Vi rekommenderar att du använder ”/” som avgränsare för sökvägen att skripten är kompatibel med servern. Här följer några exempel på relativa sökvägar och deras motsvarande absoluta sökvägar. I det här är C:\LocalRunDataRoot data-rotmappen.

|Relativ sökväg|Absolut sökväg|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Arbetskatalog

När du kör U-SQL-skript lokalt, skapas en arbetskatalog under kompilering under aktuella körningskatalogen. Förutom kompilering-utdata blir nödvändiga runtime-filer för lokal körning skuggkopior till den här arbetskatalog. Rotmappen fungerande directory kallas ”ScopeWorkDir” och filer under arbetskatalogen är följande:

|Directory/fil|Directory/fil|Directory/fil|Definition|Beskrivning|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hash-sträng med körningsversion|Skuggkopia av runtime-filer som behövs för lokal körning|
| |Script_66AE4909AA0ED06C| |Skriptets namn + hash-sträng med skriptets sökväg|Utdata för kompilering och körning steg loggning|
| | |\_skriptet\_.abr|Kompilatorutdata|Algebra fil|
| | |\_ScopeCodeGen\_.*|Kompilatorutdata|Genererade förvaltad kod|
| | |\_ScopeCodeGenEngine\_.*|Kompilatorutdata|Genererade intern kod|
| | |Refererade sammansättningar|Sammansättningsreferensen|Refererade sammansättningsfiler|
| | |deployed_resources|Resursdistributionen|Resursfiler för distribution|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Körningsloggen|Logg över utförande|


## <a name="use-the-sdk-from-the-command-line"></a>Använd SDK: N från kommandoraden

### <a name="command-line-interface-of-the-helper-application"></a>Kommandoradsgränssnitt för hjälpprogrammet

Under SDK directory\build\runtime är LocalRunHelper.exe kommandoradsverktyget hjälpprogram som tillhandahåller gränssnitt för att de flesta av de vanligaste funktionerna körs lokalt. Observera att både kommandot och växlarna argumentet är skiftlägeskänsliga. Att anropa den:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Kör LocalRunHelper.exe utan argument eller med den **hjälpa** växel för att visa hjälpinformation:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

I hjälpinformation:

-  **Kommandot** ger kommandots namn.  
-  **Argumentet är obligatoriskt** listas argument som måste anges.  
-  **Valfria Argument** listas argument som är valfria, med standardvärden.  Valfritt booleskt argument inte har parametrar och deras utseende betyder negativt till deras standardvärden.

### <a name="return-value-and-logging"></a>Returvärdet och loggning

Hjälpprogrammet returnerar **0** för att lyckas och **-1** till felet. Som standard skickar alla meddelanden till den aktuella konsolen i Hjälp. Men de flesta kommandon som stöder den **- MessageOut sökväg_till_loggfil** valfritt argument som omdirigerar utdata till en loggfil.

### <a name="environment-variable-configuring"></a>Miljö variabeln konfigurera

Lokal U-SQL kör behöver en rot för angivna data som konto för lokal lagring, samt en angiven CppSDK sökväg för beroenden. Du kan både ange argument på kommandoraden eller ange miljövariabeln för dessa.

- Ange den **SCOPE_CPP_SDK** miljövariabeln.

    Om du får Microsoft Visual C++ och Windows SDK genom att installera Data Lake Tools för Visual Studio kan du kontrollera att du har följande mapp:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definiera en ny miljövariabel som heter **SCOPE_CPP_SDK** så att den pekar till den här katalogen. Eller kopiera mappen till den andra platsen och ange **SCOPE_CPP_SDK** som.

    Utöver att ställa in miljövariabeln, kan du ange den **- CppSDK** argumentet när du använder från kommandoraden. Det här argumentet skriver över dina standard CppSDK-miljövariabeln.

- Ange den **LOCALRUN_DATAROOT** miljövariabeln.

    Definiera en ny miljövariabel som heter **LOCALRUN_DATAROOT** som pekar till dataroten.

    Utöver att ställa in miljövariabeln, kan du ange den **- DataRoot** argumentet med data-rotsökvägen när du använder en kommandorad. Det här argumentet skriver över miljövariabeln standard data rot. Du måste lägga till det här argumentet till varje kommandoraden som du kör så att du kan skriva över miljövariabeln standard data rot för alla åtgärder.

### <a name="sdk-command-line-usage-samples"></a>SDK kommandoraden användning-exempel

#### <a name="compile-and-run"></a>Kompilera och kör

Den **kör** används för att kompilera skriptet och sedan köra kompilerade resultat. Dess argument på kommandoraden är en kombination av dessa från **Kompilera** och **köra**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Följande är valfria argument för **kör**:


|Argument|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|-CodeBehind|False|Skriptet har .cs koden bakom|
|-CppSDK| |CppSDK Directory|
|-DataRoot| DataRoot miljövariabeln|DataRoot för lokal körning ”LOCALRUN_DATAROOT' miljövariabeln som standard|
|-MessageOut| |Dumpa meddelanden i konsolen till en fil|
|-Parallell|1|Kör planen med den angivna parallelliteten|
|-Referenser| |Lista över sökvägar till extra referenssammansättningar eller datafiler i koden bakom, avgränsade med ””;|
|-UdoRedirect|False|Generera Udo sammansättningen omdirigerings-config|
|-UseDatabase|master|Databasen som ska användas för koden bakom tillfälliga för registrering|
|-Verbose|False|Visa detaljerade utdata från körningen|
|-WorkDir|Aktuell katalog|Katalogen för kompilatorn användnings- och utdata|
|-RunScopeCEP|0|ScopeCEP läge som ska användas|
|-ScopeCEPTempPath|temp|Tillfällig sökväg för strömmande data|
|-OptFlags| |Kommaavgränsad lista över optimering flaggor|


Här är ett exempel:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Förutom att kombinera **Kompilera** och **köra**, kan du kompilera och köra den kompilerade körbara filer separat.

#### <a name="compile-a-u-sql-script"></a>Kompilera ett U-SQL-skript

Den **Kompilera** används för att kompilera ett U-SQL-skript för att körbara filer.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Följande är valfria argument för **Kompilera**:


|Argument|Beskrivning|
|--------|-----------|
| -Bakomliggande koden [standardvärdet 'False']|Skriptet har .cs koden bakom|
| -CppSDK [standardvärde '']|CppSDK Directory|
| -DataRoot [standardvärdet 'DataRoot miljövariabeln']|DataRoot för lokal körning ”LOCALRUN_DATAROOT' miljövariabeln som standard|
| -MessageOut [standardvärde '']|Dumpa meddelanden i konsolen till en fil|
| -Hänvisar till [standardvärde '']|Lista över sökvägar till extra referenssammansättningar eller datafiler i koden bakom, avgränsade med ””;|
| -Ytlig [standardvärdet 'False']|Ytlig kompilera|
| -UdoRedirect [standardvärdet 'False']|Generera Udo sammansättningen omdirigerings-config|
| -UseDatabase [standardvärdet ”master”]|Databasen som ska användas för koden bakom tillfälliga för registrering|
| -WorkDir [standardvärdet 'Aktuella katalogen']|Katalogen för kompilatorn användnings- och utdata|
| -RunScopeCEP [standardvärdet '0']|ScopeCEP läge som ska användas|
| -ScopeCEPTempPath [standardvärdet 'temp.]|Tillfällig sökväg för strömmande data|
| -OptFlags [standardvärde '']|Kommaavgränsad lista över optimering flaggor|


Här följer några användningsexempel.

Kompilera ett U-SQL-skript:

    LocalRunHelper compile -Script d:\test\test1.usql

Kompilera ett U-SQL-skript och ange data-rotmappen. Observera att detta ersätter set-miljövariabeln.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Kompilera ett U-SQL-skript och ange en arbetskatalog och referens sammansättningen databasen:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Köra kompilerade resultat

Den **köra** används för att köra kompilerade resultat.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Följande är valfria argument för **köra**:

|Argument|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|-DataRoot | '' |Dataroten för körning av metadata. Den som standard den **LOCALRUN_DATAROOT** miljövariabeln.|
|-MessageOut | '' |Dumpa meddelanden i konsolen till en fil.|
|-Parallell | '1' |Indikatorn att köra genererade körs lokalt stegen med angivna parallellitet.|
|-Verbose | 'False' |Indikator för att visa detaljerade utdata från körningen.|

Här är ett användningsexempel på:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Använd SDK med programmeringsgränssnitt

Programmeringsgränssnitt finns i LocalRunHelper.exe. Du kan använda dem för att integrera funktionerna i SDK U-SQL och C#-Testramverk för att skala din lokala test för U-SQL-skript. I den här artikeln använder jag standard C# enhet test-projektet för att visa hur du använder dessa gränssnitt för att testa U-SQL-skript.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Steg 1: Skapa C# enhetstestar projekt och konfiguration

- Skapa ett C# enhet testprojekt fil > Nytt > Projekt > Visual C# > Testa > enhet Testa projektet.
- Lägg till LocalRunHelper.exe som referens för projektet. LocalRunHelper.exe finns i \build\runtime\LocalRunHelper.exe i Nuget-paketet.

    ![Azure Data Lake U-SQL SDK Lägg till referens](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **endast** support x64 miljö, se till att ange mål för build-plattform som x64. Du kan ange som via projektegenskap > Skapa > plattform mål.

    ![Azure Data Lake U-SQL SDK konfigurera x64 projekt](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Se till att ställa in testmiljön som x64. I Visual Studio kan du ändra det genom testning > Testa inställningar > standard processorarkitektur > x64.

    ![Azure Data Lake U-SQL SDK konfigurera x64 testmiljö](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Se till att kopiera alla beroendefiler under NugetPackage\build\runtime\ till projektet arbetskatalog som vanligtvis är under ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Steg 2: Skapa testfall för U-SQL-skript

Nedan visas exempelkod för U-SQL-skriptet test. För att testa måste du förbereda skript, indata och utdata som förväntas-filer.

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

LocalRunHelper.exe innehåller programmeringsgränssnitt för U-SQL lokalt kompilera, kör och så vidare. Gränssnitt som visas på följande sätt.

**Konstruktor**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parameter|Typ|Beskrivning|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|Ange null för att använda konsolen för utgående meddelanden|

**Egenskaper**

|Egenskap |Typ|Beskrivning|
|--------|----|-----------|
|AlgebraPath|string|Sökvägen till filen algebra (algebra filen är ett av kompileringsresultaten)|
|CodeBehindReferences|string|Om skriptet innehåller ytterligare koden bakom referenser, anger sökvägar avgränsade med ””;|
|CppSdkDir|string|CppSDK directory|
|CurrentDir|string|Aktuell katalog|
|DataRoot|string|Rotsökvägen för data|
|DebuggerMailPath|string|Sökvägen till felsökare brevlåda|
|GenerateUdoRedirect|bool|Om vi vill generera sammansättningen som läser in omdirigering åsidosätta config|
|HasCodeBehind|bool|Om skriptet har koden bakom|
|InputDir|string|Katalogen för indata|
|MessagePath|string|Meddelandet dump-filsökväg|
|OutputDir|string|Katalogen för utdata|
|Parallellitet|int|Parallellitet för att köra algebra|
|ParentPid|int|Process-ID för den överordnade där tjänsten övervakar om du vill avsluta, har angetts till 0 eller ett negativt för att ignorera|
|ResultPath|string|Resultatet dump-filsökväg|
|RuntimeDir|string|Runtime-katalog|
|scriptPath|string|Var du hittar skriptet|
|Ytlig|bool|Ytlig kompilera eller inte|
|TempDir|string|Temp-katalogen|
|UseDataBase|string|Ange databasen som ska användas för koden bakom tillfälliga för registrering, master som standard|
|WorkDir|string|Prioriterade arbetskatalog|


**Metod**

|Metod|Beskrivning|Returnera|Parameter|
|------|-----------|------|---------|
|offentliga bool DoCompile()|Kompilera U-SQL-skript|SANT om åtgärden lyckades| |
|offentliga bool DoExec()|Kör det kompilerade resultat|SANT om åtgärden lyckades| |
|offentliga bool DoRun()|Kör U-SQL-skript (kompilera + kör)|SANT om åtgärden lyckades| |
|offentliga bool IsValidRuntimeDir (sträng sökväg)|Kontrollera om den angivna sökvägen är giltig runtime sökväg|Gäller för giltiga|Sökvägen till runtime-katalog|


## <a name="faq-about-common-issue"></a>Vanliga frågor och svar om vanliga problem

### <a name="error-1"></a>Fel 1:
E_CSC_SYSTEM_INTERNAL: Internt fel! Det gick inte att läsa in filen eller sammansättningen 'ScopeEngineManaged.dll ”eller något av dess beroenden. Det gick inte att hitta den angivna modulen.

Kontrollera följande:

- Kontrollera att du har x64 miljö. Målplattform build- och testmiljön bör vara x64, referera till **steg 1: Skapa C# enhetstestar projekt och konfiguration** ovan.
- Kontrollera att du har kopierat alla beroendefiler under NugetPackage\build\runtime\ till projektet arbetskatalog.


## <a name="next-steps"></a>Nästa steg

* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* För att du loggar diagnostikinformation finns [åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Om du vill se en mer komplex fråga, se [analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill visa jobbinformation Se [Använd Jobbwebbläsare och Jobbvy för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
* Om du vill använda Körningsvy Se [använda den Körningsvy i Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
