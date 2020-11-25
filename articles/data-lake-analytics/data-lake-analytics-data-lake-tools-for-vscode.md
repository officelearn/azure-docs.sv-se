---
title: Använda Azure Data Lake Tools för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio Code för att skapa, testa och köra U-SQL-skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e7da5454581e0e414dc832d4fcec50277f3b7f40
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019063"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Använda Azure Data Lake Tools för Visual Studio Code

I den här artikeln lär du dig hur du kan använda Azure Data Lake verktyg för Visual Studio Code (VS Code) för att skapa, testa och köra U-SQL-skript. Informationen beskrivs också i följande videoklipp:

[![Videos pelare: Azure Data Lake verktyg för VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Förutsättningar

Azure Data Lake verktyg för VS Code stöder Windows, Linux och macOS. U-SQL lokal körning och lokal fel sökning fungerar bara i Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

För MacOS och Linux:

- [.NET Core SDK 2,0](https://www.microsoft.com/net/download/core)
- [Mono 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installera Azure Data Lake verktyg

När du har installerat kraven kan du installera Azure Data Lake Tools för VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Installera Azure Data Lake verktyg

1. Öppna Visual Studio Code.
2. Välj **tillägg** i det vänstra fönstret. Ange **Azure Data Lake verktyg** i sökrutan.
3. Välj **Installera** bredvid **Azure Data Lake verktyg**.

   ![Alternativ för att installera Data Lake verktyg](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Efter några sekunder ändras knappen **Installera** till **Läs igen**.
4. Välj **Läs in igen** för att aktivera **Azure Data Lake verktygs** tillägget.
5. Bekräfta genom att välja **Omladdnings fönster** . Du kan se **Azure Data Lake-verktyg** i fönstret **tillägg** .

## <a name="activate-azure-data-lake-tools"></a>Aktivera Azure Data Lake verktyg

Skapa en. usql-fil eller öppna en befintlig. usql-fil för att aktivera tillägget.

## <a name="work-with-u-sql"></a>Arbeta med U-SQL

Om du vill arbeta med U-SQL måste du öppna antingen en U-SQL-fil eller en mapp.

### <a name="to-open-the-sample-script"></a>Så här öppnar du exempel skriptet

Öppna kommando paletten (Ctrl + Shift + P) och ange **ADL: Open Sample script**. Den öppnar en annan instans av det här exemplet. Du kan också redigera, konfigurera och skicka ett skript på den här instansen.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Så här öppnar du en mapp för ditt U-SQL-projekt

1. Välj **Arkiv** -menyn från Visual Studio Code och välj sedan **Öppna mapp**.
2. Ange en mapp och välj sedan **Välj mapp**.
3. Välj menyn **Arkiv** och välj sedan **ny**. En namnlös-1-fil har lagts till i projektet.
4. Ange följande kod i filen namnlös-1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   UTDATA @departments     till "/Output/departments.csv" med Outputters.Csv ();

    Skriptet skapar en departments.csv-fil med vissa data som ingår i mappen/output.

5. Spara filen som **myUSQL. usql** i den öppna mappen.

### <a name="to-compile-a-u-sql-script"></a>Kompilera ett U-SQL-skript

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: kompilera skript**. Compile-resultaten visas i fönstret **utdata** . Du kan också högerklicka på en skript fil och sedan välja **ADL: kompilera skript** för att kompilera ett U-SQL-jobb. Resultatet av kompileringen visas i fönstret **utdata** .

### <a name="to-submit-a-u-sql-script"></a>Så här skickar du ett U-SQL-skript

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: skicka jobb**. Du kan också högerklicka på en skript fil och sedan välja **ADL: skicka jobb**.

När du har skickat ett U-SQL-jobb visas överförings loggarna i fönstret **utdata** i vs Code. Jobb visningen visas i den högra rutan. Om överföringen lyckas visas även jobb-URL: en. Du kan öppna jobb-URL: en i en webbläsare för att spåra jobb status i real tid.

På fliken **Sammanfattning** i vyn jobb kan du se jobb informationen. Huvud funktionerna omfattar att skicka om ett skript, duplicera ett skript och öppna i portalen. På fliken **data** i vyn jobb kan du referera till indatafiler, utdatafiler och resursfiler. Filer kan laddas ned till den lokala datorn.

![Fliken Sammanfattning i vyn jobb](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Fliken data i vyn jobb](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Ange standard kontexten

Du kan ange standard kontexten för att tillämpa den här inställningen på alla skriptfiler om du inte har angett parametrar för filer individuellt.

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: Ange standard kontext**. Eller högerklicka på skript redigeraren och välj **ADL: Ange standard kontext**.
3. Välj det konto, den databas och det schema som du vill använda. Inställningen sparas i xxx_settings.jsi konfigurations filen.

   ![Konto, databas och schema uppsättning som standard kontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Ange skript parametrar

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: ange skript parametrar**.
3. xxx_settings.jsfilen öppnas med följande egenskaper:

   - **konto**: ett Azure Data Lake Analytics konto i din Azure-prenumeration som behövs för att kompilera och köra U-SQL-jobb. Du måste konfigurera dator kontot innan du kompilerar och kör U-SQL-jobb.
   - **databas**: en databas under ditt konto. Standardvärdet är **Master**.
   - **schema**: ett schema under din databas. Standardvärdet är **dbo**.
   - **optionalSettings**:
        - **prioritet**: prioritets intervallet är från 1 till 1000, med 1 som högsta prioritet. Standardvärdet är **1000**.
        - **degreeOfParallelism**: intervallet för parallellitet är mellan 1 och 150. Standardvärdet är den maximala parallellitet som tillåts i ditt Azure Data Lake Analytics-konto.

   ![Innehåll i JSON-filen](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> När du har sparat konfigurationen visas kontot, databasen och schema informationen i statusfältet i det nedre vänstra hörnet av motsvarande. usql-fil om du inte har en standard kontext uppsättning.

### <a name="to-set-git-ignore"></a>Ange git-ignorera

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: Ange git-ignorera**.

   - Om du inte har en **. gitIgnore** -fil i din vs Code-arbetsmapp skapas en fil med namnet **. gitIgnore** i mappen. Fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **OBJ**) läggs till i filen som standard. Du kan göra fler uppdateringar om det behövs.
   - Om du redan har en **. gitIgnore** -fil i din vs Code-arbetsmapp lägger verktyget till fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **OBJ**) i din **. gitIgnore** -fil om de fyra objekten inte ingår i filen.

   ![Objekt i. gitIgnore-filen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeta med bakomliggande filer: C Sharp, python och R

Azure Data Lake verktyg stöder flera anpassade koder. Anvisningar finns i [utveckla U-SQL med python, R och C Sharp för Azure Data Lake Analytics i vs Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Arbeta med sammansättningar

Information om hur du utvecklar sammansättningar finns i [utveckla U-SQL-sammansättningar för Azure Data Lake Analytics jobb]().

Du kan använda Data Lake verktyg för att registrera anpassade kod sammansättningar i Data Lake Analytics katalogen.

### <a name="to-register-an-assembly"></a>Registrera en sammansättning

Du kan registrera sammansättningen via kommandot **ADL: registrera sammansättning** eller **ADL: registrera sammansättning (avancerat)** .

### <a name="to-register-through-the-adl-register-assembly-command"></a>Registrera via kommandot ADL: registrera sammansättning

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: registrera sammansättning**.
3. Ange den lokala sammansättnings Sök vägen.
4. Välj ett Data Lake Analytics konto.
5. Välj en databas.

Portalen öppnas i en webbläsare och visar sammansättnings registrerings processen.  

Ett bekvämare sätt att utlösa kommandot **ADL: registrera sammansättning** är att högerklicka på DLL-filen i Utforskaren.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Registrera dig via kommandot ADL: registrera sammansättning (avancerat)

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: registrera sammansättning (avancerat)**.
3. Ange den lokala sammansättnings Sök vägen.
4. JSON-filen visas. Granska och redigera sammansättnings beroenden och resurs parametrarna, om det behövs. Instruktioner visas i fönstret **utdata** . Spara (Ctrl + S) JSON-filen om du vill fortsätta med sammansättnings registreringen.

   ![JSON-fil med sammansättnings beroenden och resurs parametrar](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake-verktyg identifierar automatiskt om DLL-filen har några sammansättnings beroenden. Beroendena visas i JSON-filen när de har identifierats.
>- Du kan ladda upp dina DLL-resurser (till exempel. txt,. png och. csv) som en del av sammansättnings registreringen.

Ett annat sätt att utlösa kommandot **ADL: registrera sammansättning (avancerat)** är att högerklicka på DLL-filen i Utforskaren.

Följande U-SQL-kod visar hur du anropar en sammansättning. I exemplet är sammansättnings namnet *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Använd U-SQL lokal körning och lokal fel sökning för Windows-användare

U-SQL lokal körning testar dina lokala data och validerar ditt skript lokalt innan koden publiceras till Data Lake Analytics. Du kan använda den lokala fel söknings funktionen för att utföra följande uppgifter innan din kod skickas till Data Lake Analytics:

- Felsök din C#-kod – bakom.
- Gå igenom koden.
- Validera ditt skript lokalt.

Den lokala funktionen för körning och lokal fel sökning fungerar bara i Windows-miljöer och stöds inte på macOS-och Linux-baserade operativ system.

Anvisningar om lokal körning och lokal fel sökning finns i [U-SQL lokal körning och lokal fel sökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan kompilera och köra U-SQL-skript i Data Lake Analytics måste du ansluta till ditt Azure-konto.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Ansluta till Azure med hjälp av ett kommando

1. Välj Ctrl + Shift + P för att öppna kommando paletten.

2. Ange **ADL: login**. Inloggnings informationen visas längst ned till höger.

   ![Ange inloggnings kommandot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Meddelande om inloggning och autentisering](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Välj **kopiera & öppna** för att öppna [inloggnings webb sidan](https://aka.ms/devicelogin). Klistra in koden i rutan och välj sedan **Fortsätt**.

    ![Inloggnings webb sida](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Följ instruktionerna för att logga in från webb sidan. När du är ansluten visas namnet på ditt Azure-konto i statusfältet i det nedre vänstra hörnet i VS Code-fönstret.

> [!NOTE]
>
> - Data Lake verktyg loggar automatiskt in dig nästa gången om du inte loggar ut.
> - Om ditt konto har två faktorer aktiverade, rekommenderar vi att du använder autentisering i stället för att använda en PIN-kod.

Logga ut genom att ange kommandot **ADL: Logga** ut.

### <a name="to-connect-to-azure-from-the-explorer"></a>Ansluta till Azure från Utforskaren

Expandera **Azure DATALAKE**, Välj **Logga in på Azure** och följ sedan steg 3 och steg 4 i [för att ansluta till Azure med hjälp av ett kommando](#sign-in-by-command).

![Val av "logga in på Azure" i Utforskaren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Du kan inte logga ut från Utforskaren. För att logga ut, se [för att ansluta till Azure med hjälp av ett kommando](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Skapa ett extraherings skript

Du kan skapa ett extraherings skript för CSV-, TSV-och. txt-filer med hjälp av kommandot **ADL: Create Extract script** eller from the Azure Data Lake Explorer.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Skapa ett extraherings skript med hjälp av ett kommando

1. Välj Ctrl + Shift + P för att öppna kommando-paletten och ange **ADL: Create Extract script**.
2. Ange den fullständiga sökvägen till en Azure Storage-fil och välj retur nyckeln.
3. Välj ett konto.
4. För en txt-fil väljer du en avgränsare för att extrahera filen.

![Process för att skapa ett extraherings skript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Extraherings skriptet genereras baserat på dina poster. För ett skript som inte kan identifiera kolumnerna väljer du ett från de två alternativen. Om inte, kommer bara ett skript att genereras.

![Resultat av att skapa ett extraherings skript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Skapa ett extraherings skript från Utforskaren

Ett annat sätt att skapa extraherings skriptet är via snabb menyn (snabb menyn) i CSV-, TSV-eller txt-filen i Azure Data Lake Store-eller Azure Blob Storage.

![Kommandot "skapa EXTRAHERINGs skript" från snabb menyn](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrera med Azure Data Lake Analytics via ett kommando

Du kan komma åt Azure Data Lake Analytics resurser för att lista konton, komma åt metadata och Visa analys jobb.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Visa en lista över Azure Data Lake Analytics konton under din Azure-prenumeration

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: lista konton**. Kontona visas i fönstret **utdata** .

### <a name="to-access-azure-data-lake-analytics-metadata"></a>För att komma åt Azure Data Lake Analytics metadata

1. Välj Ctrl + Shift + P och ange sedan **ADL: list tabeller**.
2. Välj ett av Data Lake Analyticss kontona.
3. Välj en av de Data Lake Analytics databaserna.
4. Välj ett schema. Du kan se listan över tabeller.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Visa Azure Data Lake Analytics jobb

1. Öppna kommando paletten (Ctrl + Shift + P) och välj **ADL: Visa jobb**.
2. Välj ett Data Lake Analytics eller ett lokalt konto.
3. Vänta tills jobb listan visas för kontot.
4. Välj ett jobb från jobb listan. Data Lake verktyg öppnar vyn jobb i den högra rutan och visar viss information i VS-kodens utdata.

    ![Jobb lista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrera med Azure Data Lake Store via ett kommando

Du kan använda Azure Data Lake Store-relaterade kommandon för att:

- [Bläddra bland Azure Data Lake Store resurser](#list-the-storage-path)
- [Förhandsgranska Azure Data Lake Store-filen](#preview-the-storage-file)
- Överför filen direkt till Azure Data Lake Store i VS-kod
- Hämta filen direkt från Azure Data Lake Store i VS-kod

### <a name="list-the-storage-path"></a>Lista lagrings Sök vägen

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Visa lagrings Sök vägen via kommando-paletten

1. Högerklicka på skript redigeraren och välj **ADL: list Sök väg**.
2. Välj mappen i listan eller Välj **Ange en sökväg** eller **Bläddra till rot Sök vägen**. (Vi använder **Ange en sökväg** som exempel.)
3. Välj ditt Data Lake Analytics-konto.
4. Bläddra till eller ange sökvägen till lagringsmappen (till exempel/output/).  

På paletten kommando listas Sök vägs informationen baserat på dina poster.

![Resultat för lagrings Sök väg](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Ett bekvämare sätt att ange den relativa sökvägen är via snabb menyn.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Visa lagrings Sök vägen via snabb menyn

Högerklicka på Sök vägs strängen och välj **list Sök väg**.

!["List Sök väg" på snabb menyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Förhandsgranska lagrings filen

1. Högerklicka på skript redigeraren och välj **ADL: förhands gransknings fil**.
2. Välj ditt Data Lake Analytics-konto.
3. Ange en Azure Storage fil Sök väg (till exempel/output/SearchLog.txt).

Filen öppnas i VS Code.

![Steg och resultat för för hands Grans kar lagrings filen](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Ett annat sätt att förhandsgranska filen är via snabb menyn på filens fullständiga sökväg eller filens relativa sökväg i skript redigeraren.

### <a name="upload-a-file-or-folder"></a>Ladda upp en fil eller mapp

1. Högerklicka på skript redigeraren och välj **Ladda upp fil** eller **Ladda upp mapp**.
2. Välj en fil eller flera filer om du har valt **överför fil**, eller Välj hela mappen om du valde **Ladda upp mapp**. Välj sedan **Ladda upp**.
3. Välj lagringsmappen i listan eller Välj **Ange en sökväg** eller **Bläddra till rot Sök vägen**. (Vi använder **Ange en sökväg** som exempel.)
4. Välj ditt Data Lake Analytics-konto.
5. Bläddra till eller ange sökvägen till lagringsmappen (till exempel/output/).
6. Välj **Välj aktuell mapp** för att ange ditt uppladdnings mål.

![Steg och resultat för att ladda upp en fil eller mapp](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Ett annat sätt att ladda upp filer till lagringen är via snabb menyn på filens fullständiga sökväg eller filens relativa sökväg i skript redigeraren.

Du kan [övervaka överförings statusen](#check-storage-tasks-status).

### <a name="download-a-file"></a>Ladda ned en fil

Du kan hämta en fil med hjälp av kommandot **ADL: Download File** eller **ADL: Download File (avancerat)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Hämta en fil via kommandot ADL: Download File (avancerat)

1. Högerklicka på skript redigeraren och välj sedan **Hämta fil (avancerat)**.
2. VS Code visar en JSON-fil. Du kan ange fil Sök vägar och hämta flera filer samtidigt. Instruktioner visas i fönstret **utdata** . Spara (Ctrl + S) JSON-filen om du vill fortsätta att ladda ned filen eller filerna.

    ![JSON-fil med sökvägar för fil hämtning](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

I fönstret **utdata** visas fil hämtnings statusen.

![Fönstret utdata med hämtnings status](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Du kan [övervaka nedladdnings statusen](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Hämta en fil via kommandot ADL: Download File

1. Högerklicka på skript redigeraren, Välj **Hämta fil** och välj sedan målmappen i dialog rutan **Välj mapp** .

1. Välj mappen i listan eller Välj **Ange en sökväg** eller **Bläddra till rot Sök vägen**. (Vi använder **Ange en sökväg** som exempel.)

1. Välj ditt Data Lake Analytics-konto.

1. Bläddra till eller ange sökvägen till lagringsmappen (till exempel/output/) och välj sedan en fil som ska laddas ned.

![Steg och resultat för att ladda ned en fil](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Ett annat sätt att ladda ned Storage-filer är via snabb menyn på filens fullständiga sökväg eller filens relativa sökväg i skript redigeraren.

Du kan [övervaka nedladdnings statusen](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrol lera status för lagrings aktiviteter

Status för uppladdning och hämtning visas i statusfältet. Välj statusfältet och sedan visas statusen på fliken **utdata** .

![Statusfält och information om utdata](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrera med Azure Data Lake Analytics från Utforskaren

När du har loggat in visas alla prenumerationer för ditt Azure-konto i den vänstra rutan under **Azure-DATALAKE**.

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics navigering för metadata

Expandera din Azure-prenumeration. Under noden **U-SQL-databaser** kan du bläddra igenom din U-SQL-databas och Visa mappar som **scheman**, **autentiseringsuppgifter**, **sammansättningar**, **tabeller** och **index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics entitets hantering av metadata

Expandera **U-SQL-databaser**. Du kan skapa en databas, schema, tabell, tabell typ, index eller statistik genom att högerklicka på motsvarande nod och sedan välja **skript som ska skapas** på snabb menyn. På sidan öppna skript redigerar du skriptet efter dina behov. Skicka sedan jobbet genom att högerklicka på det och välja **ADL: skicka jobb**.

När du har skapat objektet högerklickar du på noden och väljer sedan **Uppdatera** för att visa objektet. Du kan också ta bort objektet genom att högerklicka på det och sedan välja **ta bort**.

![Kommandot "skript att skapa" på snabb menyn i Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skript sida för det nya objektet](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics sammansättnings registrering

Du kan registrera en sammansättning i motsvarande databas genom att högerklicka på noden **sammansättningar** och sedan välja **Registrera sammansättning**.

![Kommandot "Registrera sammansättning" på snabb menyn för noden sammansättningar](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrera med Azure Data Lake Store från Utforskaren

Bläddra till **data Lake Store**:

- Du kan högerklicka på noden mapp och sedan använda kommandona **Uppdatera**, **ta bort**, **Ladda upp**, **överför,** **Kopiera relativ sökväg** och **Kopiera fullständig sökväg** på snabb menyn.

   ![Snabb Meny kommandon för en mapp-nod i Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Du kan högerklicka på filnoden och sedan använda kommandona för för **hands version**, **Hämta**, **ta bort**, **skapa extraherings skript** (endast tillgängligt för CSV-, TSV-och txt-filer), **Kopiera relativ sökväg** och **Kopiera fullständiga sökvägar** på snabb menyn.

   ![Snabb Meny kommandon för en filnod i Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrera med Azure Blob Storage från Utforskaren

Bläddra till Blob Storage:

- Du kan högerklicka på noden BLOB container och sedan använda kommandona **Uppdatera**, **ta bort BLOB container** och **Ladda upp BLOB** på snabb menyn.

   ![Snabb Meny kommandon för en BLOB container-nod under Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Du kan högerklicka på noden mapp och sedan använda kommandona **Uppdatera** och **Ladda upp BLOB** på snabb menyn.

   ![Snabb Meny kommandon för en mapp-nod under Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Du kan högerklicka på filnoden och sedan använda kommandona **Preview/redigera**, **Hämta**, **ta bort**, **skapa extraherings skript** (endast tillgängligt för CSV-, TSV-och txt-filer), **Kopiera relativ sökväg** och **Kopiera fullständiga sökvägar** på snabb menyn.

    ![Snabb Meny kommandon för en filnod under Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Öppna Data Lake Explorer i portalen

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **Öppna webb Azure Storage Explorer** eller högerklicka på en relativ sökväg eller den fullständiga sökvägen i skript redigeraren och välj sedan **Öppna webb Azure Storage Explorer**.
3. Välj ett Data Lake Analytics konto.

Data Lake-verktyg öppnar Azure Storage sökväg i Azure Portal. Du kan hitta sökvägen och förhandsgranska filen från webben.

## <a name="additional-features"></a>Ytterligare funktioner

Data Lake verktyg för VS Code stöder följande funktioner:

- **IntelliSense-komplettering**: förslag visas i popup-fönster runt objekt som nyckelord, metoder och variabler. Olika ikoner representerar olika typer av objekt:

  - Data typen Scala
  - Komplex data typ
  - Inbyggda UDTs
  - .NET-samling och klasser
  - C#-uttryck
  - Inbyggda C#-UDF: er, Katalogentiteter och UDAAGs
  - U-SQL-funktioner
  - U-SQL-fönster funktioner

    ![Objekt typer i IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **IntelliSense-komplettering på data Lake Analytics metadata**: data Lake-verktyg hämtar information om data Lake Analytics metadata lokalt. IntelliSense-funktionen fyller automatiskt i objekt från Data Lake Analytics metadata. Dessa objekt omfattar databas, schema, tabell, vy, tabell värdes funktion, procedurer och C#-sammansättningar.

  ![IntelliSense-metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense-fel markör**: data Lake verktyg stryker under redigering av fel för U-SQL och C#.
- **Syntax**: data Lake verktyg använder färger för att särskilja objekt som variabler, nyckelord, data typer och funktioner.

    ![Syntax med olika färger](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Vi rekommenderar att du uppgraderar till Azure Data Lake verktyg för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och är nu föråldrade.  

## <a name="next-steps"></a>Nästa steg

- [Utveckla U-SQL med python, R och C Sharp för Azure Data Lake Analytics i VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL lokal körning och lokal fel sökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudie: kom igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudie: utveckla U-SQL-skript med hjälp av Data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)