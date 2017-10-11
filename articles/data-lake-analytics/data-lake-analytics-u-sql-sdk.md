---
title: "Skala lokal U-SQL-köra och testa med Azure Data Lake U-SQL-SDK | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake U-SQL-SDK för att skala U-SQL lokalt körs jobben och testning med kommandoraden och programmeringsgränssnitt på din lokala arbetsstationen."
services: data-lake-analytics
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: yanacai
ms.openlocfilehash: 55242bcf644ca0e7f30cfe7eada2130451c36e64
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="scale-u-sql-local-run-and-test-with-azure-data-lake-u-sql-sdk"></a>Skala lokal U-SQL-köra och testa med Azure Data Lake U-SQL-SDK

När du utvecklar U-SQL-skript, är det vanligt att köra och testa U-SQL-skript lokalt skicka innan den till molnet. Azure Data Lake innehåller en Nuget-paketet kallas SDK för Azure Data Lake U-SQL i det här scenariot, via som du sedan kan du skala lokal U-SQL kör och test. Det är också möjligt att integrera U-SQL testet med CI (kontinuerlig Integration) system för att automatisera kompileringen och testa.

Om du bryr dig om hur för att manuellt lokala körs och felsöka U-SQL-skript med GUI-tooling kan du använda Azure Data Lake-verktyg för Visual Studio för att. Mer information från [här](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Installera Azure Data Lake U-SQL-SDK

Du kan hämta SDK för Azure Data Lake U-SQL [här](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) på Nuget.org. Och innan du använder den, måste du kontrollera att du har beroenden på följande sätt.

### <a name="dependencies"></a>Beroenden

Data Lake U-SQL-SDK kräver följande beroenden:

- [Microsoft .NET Framework 4.6 eller nyare](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++-14 och Windows SDK 10.0.10240.0 eller senare (som kallas CppSDK i den här artikeln). Det finns två sätt att få CppSDK:

    - Installera [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Du har en \Windows Kits\10 mappen under mappen Program – till exempel C:\Program Files (x86) \Windows Kits\10\. Du hittar också SDK för Windows 10-version i \Windows Kits\10\Lib. Om du inte ser dessa mappar, installera om Visual Studio och se till att välja Windows 10 SDK under installationen. Om du har det installeras med Visual Studio, hittar lokal U-SQL-kompileraren den automatiskt.

    ![Data Lake-verktyg för Visual Studio lokala kör Windows 10-SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Installera [Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs). Du kan hitta färdigförpackade Visual C++ och Windows SDK filer i C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. I det här fallet hittar lokal U-SQL-kompileraren inte beroenden automatiskt. Du måste ange CppSDK sökvägen för den. Du kan kopiera filer till en annan plats, eller så kan du använda den som den är.

## <a name="understand-basic-concepts"></a>Förstå grundläggande begrepp

### <a name="data-root"></a>Dataroten

Data-rotmappen är ”lokalt Arkiv” för den lokala beräkningskonto. Det motsvarar att Azure Data Lake Store-konto för Data Lake Analytics-konto. Växla till en annan data-rotmapp är precis som om du växlar till en annan store-konto. Om du vill komma åt ofta delade data med olika dataroten mappar måste du använda absoluta sökvägar i skript. Skapa filen system symboliska länkar (till exempel **mklink** på NTFS) under data till rotmappen att peka till delade data.

Data-rotmappen används för att:

- Lagra lokala metadata, inklusive databaser, tabeller, tabellvärdesfunktioner (Tabellvärdesfunktioner) och sammansättningar.
- Leta upp de inkommande och utgående sökvägar som har definierats som relativa sökvägar i U-SQL. Använda relativa sökvägar gör det enklare att distribuera dina U-SQL-projekt till Azure.

### <a name="file-path-in-u-sql"></a>Sökvägen i U-SQL

Du kan använda både en relativ sökväg och en lokal absolut sökväg i U-SQL-skript. Den relativa sökvägen är i förhållande till angivna data-sökvägen till rotmappen. Vi rekommenderar att du använder ”/” som sökväg avgränsare att göra skript som är kompatibel med servern. Här följer några exempel på relativa sökvägar och deras motsvarande absoluta sökvägar. I det här exemplet är C:\LocalRunDataRoot data till rotmappen.

|Relativ sökväg|Absolut sökväg|
|-------------|-------------|
|/ABC/def/Input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|ABC/def/Input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/ABC/def/Input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Arbetskatalog

När du kör U-SQL-skript lokalt, skapas en arbetskatalog under kompilering under katalogen körs. Förutom kompilering-utdata blir nödvändiga runtime-filer för lokala körningen shadow kopieras till den här arbetskatalogen. Rotmapp för aktiv katalog kallas ”ScopeWorkDir” och filer under arbetskatalogen är följande:

|Filen/katalogen|Filen/katalogen|Filen/katalogen|Definition|Beskrivning|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hash-sträng av körningsversion|Skuggkopia av runtime-filer som behövs för lokal körning|
| |Script_66AE4909AA0ED06C| |Script namn + hash-sträng med sökvägen för skriptet|Utdata för kompilering och körning steg loggning|
| | |\_skriptet\_.abr|Utdata för kompilator|Algebra fil|
| | |\_ScopeCodeGen\_. *|Utdata för kompilator|Genererade hanterad kod|
| | |\_ScopeCodeGenEngine\_. *|Utdata för kompilator|Genererade maskinkod|
| | |Refererade sammansättningar|Sammansättningsreferensen|Sammansättningsfiler|
| | |deployed_resources|Resurs-distribution|Resursfiler för distribution|
| | |xxxxxxxx.xxx[1..n]\_\*. *|Körningsloggen|Logg över utförande|


## <a name="use-the-sdk-from-the-command-line"></a>Använd SDK: N från kommandoraden

### <a name="command-line-interface-of-the-helper-application"></a>Kommandoradsgränssnitt för hjälpprogrammet

Under SDK directory\build\runtime är LocalRunHelper.exe kommandoradsverktyget hjälpprogram som tillhandahåller gränssnitt till de flesta av funktionerna används ofta kör lokalt. Observera att både kommandot och växlarna argumentet är skiftlägeskänsliga. Att anropa den:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Kör LocalRunHelper.exe utan argument eller med den **hjälp** växla till Visa hjälp:

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
-  **Argumentet är obligatoriskt** listas argument måste anges.  
-  **Valfria argumentet** listas argument som är valfria med standardvärden.  Valfri boolesk argument ha inte parametrar, och deras utseende innebär negativt till standardvärdet.

### <a name="return-value-and-logging"></a>Returvärdet och loggning

Hjälpprogrammet returnerar **0** för lyckad och **-1** för felet. Som standard skickar alla meddelanden i hjälpen till den aktuella konsolen. De flesta av kommandona stöder dock den **- MessageOut sökväg_till_loggfil** valfritt argument som omdirigerar utdata till en loggfil.

### <a name="environment-variable-configuring"></a>Miljö variabeln konfigurera

Lokal U-SQL kör behov angivna data från en rotcertifikatutfärdare som konto för lokal lagring, samt en angiven CppSDK sökväg för beroenden. Du kan både ange argumentet i kommandorads- eller ange miljövariabeln för dessa.

- Ange den **SCOPE_CPP_SDK** miljövariabeln.

    Om du får Microsoft Visual C++ och Windows SDK genom att installera Data Lake-verktyg för Visual Studio kan du kontrollera att du har följande mapp:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definiera en ny miljövariabel som heter **SCOPE_CPP_SDK** så att den pekar till den här katalogen. Eller kopiera mappen till den andra platsen och ange **SCOPE_CPP_SDK** som.

    Förutom att konfigurera denna variabel kan du ange den **- CppSDK** argumentet när du använder kommandoraden. Det här argumentet skriver över dina standard CppSDK miljövariabeln.

- Ange den **LOCALRUN_DATAROOT** miljövariabeln.

    Definiera en ny miljövariabel som heter **LOCALRUN_DATAROOT** som pekar på dataroten.

    Förutom att konfigurera denna variabel kan du ange den **- DataRoot** argumentet med data-rotsökvägen när du använder en kommandorad. Det här argumentet skriver över dina standard dataroten miljövariabeln. Du måste lägga till det här argumentet var kommandoraden som du kör så att du kan skriva över miljövariabeln standard data roten för alla åtgärder.

### <a name="sdk-command-line-usage-samples"></a>SDK kommandoraden användning prover

#### <a name="compile-and-run"></a>Kompilera och kör

Den **kör** kommandot används för att kompilera skriptet och sedan köra kompilerade resultatet. Dess kommandoradsargument är en kombination av dessa från **Kompilera** och **köra**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Följande är valfria argument för **kör**:


|Argumentet|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|-Bakomliggande koden|False|Skriptet har .cs koden bakom|
|-CppSDK| |CppSDK Directory|
|-DataRoot| DataRoot miljövariabel|DataRoot för lokal körning standard miljövariabeln 'LOCALRUN_DATAROOT'|
|-MessageOut| |Dump meddelanden på konsolen till en fil|
|-Parallell|1|Kör planen med angivna parallellitet|
|-Referenser| |Lista över sökvägar till extra referenssammansättningar eller datafiler i koden bakom, avgränsade med ';'|
|-UdoRedirect|False|Generera Udo sammansättningen omdirigering config|
|-UseDatabase|master|Databasen som ska användas för koden bakom tillfällig sammansättning registrering|
|-Verbose|False|Visa detaljerade utdata från runtime|
|-WorkDir|Aktuell katalog|Katalogen för kompileraren användnings- och utdata|
|-RunScopeCEP|0|ScopeCEP läge ska använda|
|-ScopeCEPTempPath|Temp|Temporär sökväg för strömmande data|
|-OptFlags| |Kommaavgränsad lista över optimering flaggor|


Här är ett exempel:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Förutom att kombinera **Kompilera** och **köra**, kan du kompilera och köra kompilerade körbara filer separat.

#### <a name="compile-a-u-sql-script"></a>Kompilera ett U-SQL-skript

Den **Kompilera** kommandot används för att kompilera ett U-SQL-skript för att körbara filer.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Följande är valfria argument för **Kompilera**:


|Argumentet|Beskrivning|
|--------|-----------|
| -Bakomliggande koden [standardvärdet 'False']|Skriptet har .cs koden bakom|
| -CppSDK [standardvärde '']|CppSDK Directory|
| -DataRoot [standardvärdet 'DataRoot miljövariabeln']|DataRoot för lokal körning standard miljövariabeln 'LOCALRUN_DATAROOT'|
| -MessageOut [standardvärde '']|Dump meddelanden på konsolen till en fil|
| -Hänvisar till [standardvärde '']|Lista över sökvägar till extra referenssammansättningar eller datafiler i koden bakom, avgränsade med ';'|
| -Kort [standardvärdet 'False']|Lite kompilera|
| -UdoRedirect [standardvärdet 'False']|Generera Udo sammansättningen omdirigering config|
| -UseDatabase [standardvärdet 'master']|Databasen som ska användas för koden bakom tillfällig sammansättning registrering|
| -WorkDir [standardvärdet 'Katalogen']|Katalogen för kompileraren användnings- och utdata|
| -RunScopeCEP [standardvärdet '0']|ScopeCEP läge ska använda|
| -ScopeCEPTempPath [standardvärdet 'temp']|Temporär sökväg för strömmande data|
| -OptFlags [standardvärde '']|Kommaavgränsad lista över optimering flaggor|


Här följer några exempel på användning.

Kompilera ett U-SQL-skript:

    LocalRunHelper compile -Script d:\test\test1.usql

Kompilera ett U-SQL-skript och ange data-rotmappen. Observera att detta ersätter miljövariabeln uppsättningen.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Kompilera ett U-SQL-skript och ange en arbetskatalog och referenssammansättning databasen:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Köra kompilerade resultat

Den **köra** kommandot används för att köra kompilerade resultatet.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Följande är valfria argument för **köra**:

|Argumentet|Beskrivning|
|--------|-----------|
|-DataRoot [standardvärde '']|Dataroten för körning av metadata. Används som standard den **LOCALRUN_DATAROOT** miljövariabeln.|
|-MessageOut [standardvärde '']|Dumpa meddelanden på konsolen till en fil.|
|-Parallell [standardvärdet '1']|Indikator för att köra de genererade lokala kör steg med det angivna parallellitet.|
|-Verbose [standardvärde 'False']|Indikator för att visa detaljerade utdata från körning.|

Här är ett exempel på användning:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Använd SDK: N med programmeringsgränssnitt

Programmeringsgränssnitt finns i LocalRunHelper.exe. Du kan använda dem för att integrera funktionerna i SDK U-SQL och C# test framework för att skala din lokala test för U-SQL-skript. I den här artikeln använder jag standard C# enhet test-projektet för att visa hur du använder dessa gränssnitt för att testa U-SQL-skript.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Steg 1: Skapa C# enhet test-projekt och konfiguration

- Skapa ett C# enhet testprojekt via fil > Nytt > Projekt > Visual C# > Testa > enhet Testa projektet.
- Lägg till LocalRunHelper.exe som en referens för projektet. LocalRunHelper.exe finns i \build\runtime\LocalRunHelper.exe i Nuget-paketet.

    ![Azure Data Lake U-SQL-SDK Lägg till referens](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL-SDK **endast** x64 Stödmiljö, se till att ange build platform mål som x64. Du kan ange som via projektegenskapen > Skapa > plattform mål.

    ![Azure Data Lake U-SQL-SDK konfigurera x64 projekt](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Se till att ange din testmiljö som x64. I Visual Studio kan du ändra det genom testning > Testa inställningar > standard processorarkitektur > x64.

    ![Azure Data Lake U-SQL-SDK konfigurera x64 testmiljö](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Se till att kopiera alla beroende filer under NugetPackage\build\runtime\ till projektet arbetskatalog som vanligtvis är under ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Steg 2: Skapa testfall för U-SQL-skript

Nedan visas exempelkod för U-SQL-skript. För att testa måste du förbereda skript indata och utdata som förväntas-filer.

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

LocalRunHelper.exe ger programmeringsgränssnitt för U-SQL lokalt kompilera kör, osv. Gränssnitt som visas nedan.

**Konstruktorn**

offentliga LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Parameter|Typ|Beskrivning|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|Ange null-konsolen för utgående meddelanden.|

**Egenskaper**

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|AlgebraPath|Sträng|Sökvägen till filen algebra (algebra filen är ett resultat för kompilering)|
|CodeBehindReferences|Sträng|Om skriptet har ytterligare kod bakom referenser, ange sökvägar avgränsade med ';'|
|CppSdkDir|Sträng|CppSDK directory|
|CurrentDir|Sträng|Aktuell katalog|
|DataRoot|Sträng|Rotsökvägen för data|
|DebuggerMailPath|Sträng|Sökvägen till felsökare mailslot|
|GenerateUdoRedirect|bool|Om vi vill generera sammansättningen som lästes in omdirigering åsidosätta config|
|HasCodeBehind|bool|Om skriptet har bakomliggande kod|
|InputDir|Sträng|Katalogen för indata|
|MessagePath|Sträng|Meddelandet dump sökväg|
|OutputDir|Sträng|Katalogen för utdata|
|Parallellitet|int|Parallellitet att köra algebra|
|ParentPid|int|Process-ID för den överordnade där tjänsten övervakar om du vill avsluta, värdet 0 eller ett negativt om du vill ignorera|
|ResultPath|Sträng|Resultatet dump sökväg|
|RuntimeDir|Sträng|Runtime directory|
|ScriptPath|Sträng|Var du hittar skriptet|
|Lite|bool|Ytlig kompilera eller inte|
|TempDir|Sträng|Temp-katalogen|
|UseDataBase|Sträng|Ange databasen som ska användas för koden bakom tillfällig sammansättning registrering, master som standard|
|WorkDir|Sträng|Prioriterade arbetskatalogen|


**Metoden**

|Metod|Beskrivning|Returnera|Parameter|
|------|-----------|------|---------|
|offentliga bool DoCompile()|Kompilera U-SQL-skript|SANT lyckades| |
|offentliga bool DoExec()|Köra kompilerade resultatet|SANT lyckades| |
|offentliga bool DoRun()|Kör U-SQL-skript (kompilera + Execute)|SANT lyckades| |
|offentliga bool IsValidRuntimeDir (sträng sökväg)|Kontrollera om den angivna sökvägen är giltig runtime sökväg|Sant för giltigt|Sökvägen till katalogen för körning|


## <a name="faq-about-common-issue"></a>Vanliga frågor och svar om vanliga problem

### <a name="error-1"></a>Fel 1:
E_CSC_SYSTEM_INTERNAL: Internt fel! Det gick inte att läsa in filen eller sammansättningen 'ScopeEngineManaged.dll' eller en av dess beroenden. Det gick inte att hitta den angivna modulen.

Kontrollera följande:

- Kontrollera att du har x64 miljö. Build-målplattform och testmiljön ska vara x64, referera till **steg 1: skapa C# enhet Testa projektet och konfiguration** ovan.
- Kontrollera att du har kopierat alla beroende filer under NugetPackage\build\runtime\ till projektet arbetskatalog.


## <a name="next-steps"></a>Nästa steg

* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Om du vill logga diagnostikinformation, se [åtkomst till diagnostik loggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Om du vill se en mer komplex fråga, se [analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill visa jobbinformation [Använd jobbet webbläsare och visa jobb för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
* Om du vill använda vyn vertex körning finns [använda vyn Vertex körning i Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
