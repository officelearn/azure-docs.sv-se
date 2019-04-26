---
title: Använda Azure Data Lake Tools för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio Code för att skapa, testa och köra U-SQL-skript.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509348"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Använda Azure Data Lake Tools för Visual Studio Code

I den här artikeln lär du dig hur du kan använda Azure Data Lake Tools för Visual Studio Code (VS Code) för att skapa, testa och köra U-SQL-skript. Informationen finns också i följande video:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Nödvändiga komponenter

Azure Data Lake Tools för VS Code har stöd för Windows, Linux och macOS. U-SQL lokal körning och lokal felsökning fungerar bara i Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

För MacOS och Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installera Azure Data Lake-verktyg

När du har installerat kraven kan du installera Azure Data Lake Tools för VS Code.

**Installera Azure Data Lake-verktyg**

1. Öppna Visual Studio Code.
2. Välj **tillägg** i den vänstra rutan. Ange **Azure Data Lake Tools** i sökrutan.
3. Välj **installera** bredvid **Azure Data Lake Tools**. 

   ![Val för att installera Data Lake-verktyg](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Efter några sekunder i **installera** knappen ändras till **Reload**.
4. Välj **Reload** att aktivera den **Azure Data Lake Tools** tillägget.
5. Välj **Reload Window** att bekräfta. Du kan se **Azure Data Lake Tools** i den **tillägg** fönstret.

 
## <a name="activate-azure-data-lake-tools"></a>Aktivera Azure Data Lake-verktyg
Skapa en .usql-fil eller öppna en befintlig .usql-fil för att aktivera tillägget. 


## <a name="work-with-u-sql"></a>Arbeta med U-SQL

Om du vill arbeta med U-SQL, måste du öppna en U-SQL-fil eller en mapp.

**Öppna exempelskriptet**

Öppna kommandopaletten (Ctrl + Skift + P) och ange **ADL: Öppna exempelskriptet**. En annan instans av det här exemplet öppnas. Du kan också redigera, konfigurera och skicka ett skript på den här instansen.

**Öppna en mapp för ditt U-SQL-projekt**

1. Visual Studio Code, väljer du den **filen** menyn och välj sedan **Öppna mapp**.
2. Ange en mapp och välj sedan **Välj mapp**.
3. Välj den **filen** menyn och välj sedan **New**. En namnlös-1-fil har lagts till i projektet.
4. Ange följande kod i filen Namnlös-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Skriptet skapar en departments.csv-fil med vissa data som ingår i mappen/Output.

5. Spara filen som **myUSQL.usql** i mappen öppnade.

**Att kompilera ett U-SQL-skript**

1. Välj Ctrl + Skift + P för att öppna kommandopaletten. 
2. Ange **ADL: Kompilera skriptet**. Kompilera resultatet visas i den **utdata** fönster. Du kan också högerklicka på en skriptfil och sedan välja **ADL: Kompilera skriptet** att kompilera ett U-SQL-jobb. Kompileringsresultatet visas i den **utdata** fönstret.
 
**Att skicka ett U-SQL-skript**

1. Välj Ctrl + Skift + P för att öppna kommandopaletten. 
2. Ange **ADL: Skicka jobb**. Du kan också högerklicka på en skriptfil och sedan välja **ADL: Skicka jobb**. 

När du skickar in ett U-SQL-jobb, skicka loggarna visas i den **utdata** fönster i VS Code. Jobbvyn visas i den högra rutan. Om överföringen lyckas visas för jobb-URL. Du kan öppna jobb-URL i en webbläsare för att spåra i realtid för jobbets status. 

I jobbvyn **sammanfattning** fliken visas Jobbinformationen. Huvudfunktioner omfattar skicka om ett skript, duplicera ett skript och öppna i portalen. I jobbvyn **DATA** fliken som du kan referera till den indatafiler och utdatafiler resursfiler. Filer kan laddas ned till den lokala datorn.

![Fliken Sammanfattning i jobbvyn](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Data-fliken i jobbvyn](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Så här anger du standardkontexten**

Du kan ange standardkontexten ska gälla alla skriptfiler den här inställningen om du inte har angett parametrarna för filer individuellt.

1. Välj Ctrl + Skift + P för att öppna kommandopaletten. 
2. Ange **ADL: Ange standardkontexten**. Eller högerklicka på Skriptredigeraren och välj **ADL: Ange standardkontexten**.
3. Välj det konto, databas och schema som du vill. Inställningen sparas till konfigurationsfilen xxx_settings.json.

   ![Konto, databas och schema som standardkontexten](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Ange Skriptparametrar**

1. Välj Ctrl + Skift + P för att öppna kommandopaletten. 
2. Ange **ADL: Ange Skriptparametrar**.
3. Xxx_settings.json-filen har öppnats med följande egenskaper:

   - **Kontot**: Ett Azure Data Lake Analytics-konto under din Azure-prenumeration som behövs för att kompilera och köra U-SQL-jobb. Du måste konfigurera kontot innan du kompilera och köra U-SQL-jobb.
   - **Databasen**: En databas med ditt konto. Standardvärdet är **master**.
   - **schema**: Ett schema under din databas. Standardvärdet är **dbo**.
   - **optionalSettings**:
        - **Prioritet**: Prioritet-intervallet är från 1 till 1 000, där 1 är den högsta prioriteten. Standardvärdet är **1000**.
        - **degreeOfParallelism**: Parallellitet-intervallet är från 1 till 150. Standardvärdet är den maximala parallellitet som tillåts i Azure Data Lake Analytics-kontot. 

   ![Innehållet i JSON-fil](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> När du har sparat konfigurationen visas kontot, databasen och schemainformation i statusfältet i nedre vänstra hörnet av filen för motsvarande .usql om du inte har en standardkontext som ställer in.

**Att ställa in Git Ignorera**

1. Välj Ctrl + Skift + P för att öppna kommandopaletten. 
2. Ange **ADL: Ställ in Git Ignorera**.

   - Om du inte har en **.gitIgnore** filen i arbetsmappen VS Code, en fil med namnet **.gitIgnore** har skapats i mappen. Fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) läggs till i filen som standard. Du kan göra fler uppdateringar om det behövs.
   - Om du redan har en **.gitIgnore** filen i arbetsmappen VS Code, verktyget lägger till fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) i din **.gitIgnore** filen, om de fyra objekt som inte fanns med i filen.

   ![Objekt i .gitIgnore-fil](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeta med bakomliggande kod filer: C Sharp, Python och R

Azure Data Lake Tools stöder flera anpassade koder. Anvisningar finns i [utveckla U-SQL med Python, R och C Sharp för Azure Data Lake Analytics i VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Arbeta med sammansättningar

Information om hur du utvecklar sammansättningar finns i [utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-assemblies.md).

Du kan använda Data Lake-verktyg för att registrera sammansättningar av anpassad kod i Data Lake Analytics-katalogen.

**Att registrera en sammansättning**

Du kan registrera sammansättningen med hjälp av den **ADL: Registrera sammansättningen** eller **ADL: Registrera sammansättningen (Avancerat)** kommando.

**Så här registrerar via ADL: Registrera sammansättningen kommando**
1.  Välj Ctrl + Skift + P för att öppna kommandopaletten.
2.  Ange **ADL: Registrera sammansättningen**. 
3.  Ange sökväg för lokala sammansättning. 
4.  Välj ett Data Lake Analytics-konto.
5.  Välj en databas.

Portalen öppnas i en webbläsare och visar sammansättningen registreringsprocessen.  

Det enklaste sättet att utlösa den **ADL: Registrera sammansättningen** kommandot är Högerklicka på .dll-filen i Utforskaren. 

**Så här registrerar via ADL: Registrera kommandot sammansättningen (Avancerat)**
1.  Välj Ctrl + Skift + P för att öppna kommandopaletten.
2.  Ange **ADL: Registrera sammansättningen (Avancerat)**. 
3.  Ange sökväg för lokala sammansättning. 
4.  JSON-filen visas. Granska och redigera paketberoenden och Resursparametrar, om det behövs. Instruktioner visas i den **utdata** fönster. Om du vill gå vidare till den för registreringen, spara (Ctrl + S) JSON-filen.

    ![JSON-fil med paketberoenden och Resursparametrar](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools autodetects om DLL-filen har några beroenden som sammansättningen. Beroenden som visas i JSON-filen när de har upptäckts. 
>- Du kan ladda upp dina DLL-resurser (till exempel .txt, .png och CSV) som en del av den för registreringen. 

Ett annat sätt att utlösa den **ADL: Registrera sammansättningen (Avancerat)** kommandot är Högerklicka på .dll-filen i Utforskaren. 

Följande U-SQL-kod visar hur du anropar en sammansättning. I det här exemplet sammansättningsnamnet är *testa*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Använda U-SQL-lokal körning och lokal felsökning för Windows-användare
Lokal U-SQL kör testar dina lokala data och verifierar ditt skript lokalt innan koden publiceras till Data Lake Analytics. Du kan använda funktionen lokal felsökning för att utföra följande åtgärder innan koden skickas till Data Lake Analytics: 
- Felsök dina C# bakomliggande kod. 
- Gå igenom koden. 
- Verifiera dina skript lokalt.

Lokal körning och lokal felsökning funktionen endast fungerar i Windows-miljöer och stöds inte på macOS och Linux-baserade operativsystem.

Anvisningar för lokal körning och lokal felsökning finns i [U-SQL-lokal körning och lokal felsökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan kompilera och köra U-SQL-skript i Data Lake Analytics, måste du ansluta till ditt Azure-konto.

<b id="sign-in-by-command">Att ansluta till Azure med hjälp av ett kommando</b>

1.  Välj Ctrl + Skift + P för att öppna kommandopaletten. 
2.  Ange **ADL: Logga in**. Inloggningsinformationen visas längst ned till höger.

    ![Att ange kommandot för inloggning](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Meddelande om inloggning och autentisering](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Välj **Kopiera & Öppna** att öppna den [inloggning webbsidan](https://aka.ms/devicelogin). Klistra in koden i rutan och välj sedan **Fortsätt**.

    ![Logga in webbsidan](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Följ anvisningarna för att logga in från webbsidan. När du är ansluten, visas namnet på ditt Azure i statusfältet i det nedre vänstra hörnet i VS Code-fönstret. 

> [!NOTE] 
>- Data Lake-verktyg loggar du in automatiskt nästa gång om du inte loggar.
>- Om ditt konto har två faktorer som är aktiverad, rekommenderar vi att du använder phone autentisering i stället för en PIN-kod.


Ange kommandot för att logga ut **ADL: Logga ut**.

**Att ansluta till Azure från explorer**

Expandera **AZURE-DATALAKE**väljer **logga in på Azure**, och följ steg 3 och 4 av [att ansluta till Azure med hjälp av ett kommando](#sign-in-by-command).

![”Logga in på Azure” val i explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Du kan inte logga från Utforskaren. Om du vill logga ut, se [att ansluta till Azure med hjälp av ett kommando](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Skapa ett skript för extrahering 
Du kan skapa ett skript för extrahering för CSV- och TSV .txt-filer med hjälp av kommandot **ADL: Skapa EXTRAHERA skript** eller från Azure Data Lake explorer.

**Skapa ett skript för extrahering med hjälp av ett kommando**

1. Välj Ctrl + Skift + P för att öppna kommandopaletten och ange **ADL: Skapa skript för extrahering**.
2. Ange den fullständiga sökvägen för en Azure Storage-fil och välj RETUR-tangenten.
3. Välj ett konto.
4. Välj en avgränsare att extrahera filen för en txt-fil. 

![Processen för att skapa ett skript för extrahering](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skriptet extrahering genereras baserat på posterna. Välj ett av de två alternativen för ett skript som inte kan identifiera kolumnerna. Annars kan du bara ett skript som ska skapas.

![Resultatet av att skapa ett skript för extrahering](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Skapa ett skript för extrahering från explorer**

Ett annat sätt att skapa skript för extrahering är via snabbmenyn (genväg) på .csv, TSV eller txt-fil i Azure Data Lake Store eller Azure Blob storage.

![”Skapa EXTRAHERA skriptet” kommando från snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrera med Azure Data Lake Analytics via ett kommando

Du kan komma åt Azure Data Lake Analytics-resurser för att lista över konton, åtkomst till metadata och visa analytics-jobb. 

**Visa en lista över Azure Data Lake Analytics-konton i din Azure-prenumeration**

1. Välj Ctrl + Skift + P för att öppna kommandopaletten.
2. Ange **ADL: Lista över konton**. Kontona visas i den **utdata** fönstret.

**Åtkomst till Azure Data Lake Analytics-metadata**

1.  Välj Ctrl + Skift + P och sedan ange **ADL: Listar tabeller**.
2.  Välj en av Data Lake Analytics-konton.
3.  Välj en av Data Lake Analytics-databaser.
4.  Välj en av scheman. Du kan se listan över tabeller.

**Visa Azure Data Lake Analytics-jobb**
1.  Öppna kommandopaletten (Ctrl + Skift + P) och välj **ADL: Visa jobb**. 
2.  Välj en Data Lake Analytics eller lokalt konto. 
3.  Vänta tills jobblistan ska visas för kontot.
4.  Välj ett jobb från jobblistan över. Data Lake-verktyg öppnas jobbvyn i den högra rutan och visas viss information i VS Code-utdata.

    ![Jobblista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrera med Azure Data Lake Store via ett kommando

Du kan använda Azure Data Lake Store-relaterade kommandon för att:
 - [Bläddra igenom Azure Data Lake Store-resurser](#list-the-storage-path) 
 - [Förhandsgranska Azure Data Lake Store-filen](#preview-the-storage-file) 
 - Ladda upp filen direkt till Azure Data Lake Store i VS Code
 - Ladda ned filen direkt från Azure Data Lake Store i VS Code

### <a name="list-the-storage-path"></a>Sökvägen för lagring 

**Visa en lista över lagringssökväg via kommandopaletten**

1. Högerklicka på Skriptredigeraren och välj **ADL: Lista över sökvägen**.
2. Välj mappen i listan eller välj **ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **ange en sökväg** kan t.ex.) 
3. Välj ditt Data Lake Analytics-konto.
4. Bläddra till eller ange mappsökvägen storage (till exempel/output /).  

Kommandopaletten listar sökvägsinformation baserat på posterna.

![Storage sökväg resultat](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Det enklaste sättet att visa den relativa sökvägen är via snabbmenyn.

**Visa en lista över lagringssökväg via snabbmenyn**

Högerklicka på sökvägssträngen och välj **lista sökvägen**.

![”Ange sökvägen” på snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Förhandsgranska filen storage

1. Högerklicka på Skriptredigeraren och välj **ADL: Förhandsgranska filen**.
2. Välj ditt Data Lake Analytics-konto. 
3. Ange en filsökväg för Azure Storage (till exempel /output/SearchLog.txt). 

Filen öppnas i VS Code.

![Steg och resultatet för att förhandsgranska filen storage](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Ett annat sätt att förhandsgranska filen är via snabbmenyn på filens fullständiga sökväg eller relativa sökvägen i Skriptredigeraren. 

### <a name="upload-a-file-or-folder"></a>Ladda upp en fil eller mapp

1. Högerklicka på Skriptredigeraren och välj **Överför fil** eller **ladda upp mapp**.
2. Välj en eller flera filer om du har valt **Överför fil**, eller välj hela mappen om du har valt **ladda upp mapp**. Välj sedan **överför**. 
3. Välj mappen i listan eller välj **ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **ange en sökväg** kan t.ex.) 
4. Välj ditt Data Lake Analytics-konto. 
5. Bläddra till eller ange mappsökvägen storage (till exempel/output /). 
6. Välj **Välj aktuella mappen** att ange din målplats.

![Steg och resultatet för att ladda upp en fil eller mapp](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Ett annat sätt att överföra filer till storage är via snabbmenyn på filens fullständiga sökväg eller relativa sökvägen i Skriptredigeraren.

Du kan [övervaka Överföringsstatusen](#check-storage-tasks-status).


### <a name="download-a-file"></a>Hämta en fil 
Du kan hämta en fil med hjälp av kommandot **ADL: Ladda ned filen** eller **ADL: Ladda ned filen (Avancerat)**.

**Att hämta en fil med ADL: Hämta fil (Avancerat) (kommando)**
1. Högerklicka på Skriptredigeraren och välj sedan **ladda ned fil (Avancerat)**.
2. VS Code visar en JSON-fil. Du kan ange sökvägar och hämta flera filer samtidigt. Instruktioner visas i den **utdata** fönster. Spara (Ctrl + S) JSON-filen om du vill fortsätta att ladda ned filen eller filerna.

    ![JSON-fil med sökvägar för filhämtning](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

Den **utdata** hämta filstatus visas i fönstret.

![Utdatafönstret med hämtningsstatus](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Du kan [övervaka hämtningsstatus](#check-storage-tasks-status).

**Att hämta en fil med ADL: Hämta fil (kommando)**

1. Högerklicka på Skriptredigeraren, Välj **ladda ned filen**, och Välj målmapp från den **Välj mapp** dialogrutan.
2. Välj mappen i listan eller välj **ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **ange en sökväg** kan t.ex.) 
3. Välj ditt Data Lake Analytics-konto. 
4. Bläddra till eller ange mappsökvägen storage (till exempel/output /) och välj sedan en fil att ladda ned.

![Steg och resultatet för att ladda ned en fil](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Ett annat sätt att hämta lagringsfilerna är via snabbmenyn på filens fullständiga sökväg eller relativa sökvägen i Skriptredigeraren.

Du kan [övervaka hämtningsstatus](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrollera status för storage uppgifter
Status för uppladdning och nedladdning visas i statusfältet. Välj statusfältet och sedan status visas på den **utdata** fliken.

![Statusfältet och matar ut information](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrera med Azure Data Lake Analytics från explorer

När du loggar in alla prenumerationer för ditt Azure-konto visas i den vänstra rutan under **AZURE-DATALAKE**. 

![Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metadatanavigering

Expandera din Azure-prenumeration. Under den **U-SQL-databaser** nod, du kan bläddra igenom dina U-SQL-databas och visa mappar som **scheman**, **autentiseringsuppgifter**, **sammansättningar**, **Tabeller**, och **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Hantering av data Lake Analytics metadata för entitet

Expandera **U-SQL-databaser**. Du kan skapa en databas, schemat, tabell, tabelltyp, index eller statistik genom att högerklicka på den motsvarande noden och sedan välja **skript för att skapa** på snabbmenyn. Redigera skriptet efter dina behov på sidan öppnade skriptet. Skicka jobbet genom att högerklicka på den och välja **ADL: Skicka jobb**. 

När du har skapat objektet, högerklicka på noden och välj sedan **uppdatera** att visa objektet. Du kan också ta bort objektet genom att högerklicka på den och sedan välja **ta bort**.

![”Skapa” skriptkommandot på snabbmenyn i Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skript-sidan för det nya objektet](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics för registrering

Du kan registrera en sammansättning i motsvarande databas genom att högerklicka på den **sammansättningar** noden och sedan välja **registrera sammansättningen**.

![”Registrera sammansättningen” kommando på snabbmenyn för noden sammansättningar](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrera med Azure Data Lake Store från explorer

Bläddra till **Data Lake Store**:

- Du kan högerklicka på mappen noden och sedan använda den **uppdatera**, **ta bort**, **överför**, **ladda upp mapp**, **kopia Relativ sökväg**, och **Kopiera fullständig sökväg** kommandon på snabbmenyn.

   ![Snabbmenykommandon för en nod för mappen i Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Du kan högerklicka på filen noden och sedan använda den **förhandsversion**, **hämta**, **ta bort**, **skapa EXTRAHERA skript** (endast tillgängligt för CSV TVS, och TXT-filer), **kopiera relativa sökväg**, och **Kopiera fullständig sökväg** kommandon på snabbmenyn.

   ![Snabbmenykommandon för en filnod i Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrera med Azure Blob storage från explorer

Bläddra till Blob storage:

- Du kan högerklicka på noden för blob-behållare och sedan använda den **uppdatera**, **ta bort Blobbehållare**, och **ladda upp Blob** kommandon på snabbmenyn.

   ![Snabbmenykommandon för en blob-behållarnod under Blob-lagring](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Du kan högerklicka på mappen noden och sedan använda den **uppdatera** och **ladda upp Blob** kommandon på snabbmenyn.

   ![Snabbmenykommandon för en nod för mappen under Blob-lagring](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Du kan högerklicka på filen noden och sedan använda den **förhandsversion/redigera**, **hämta**, **ta bort**, **skapa EXTRAHERA skript** (tillgänglig endast för CSV, TVS och TXT-filer), **kopiera relativa sökväg**, och **Kopiera fullständig sökväg** kommandon på snabbmenyn.

    ![Snabbmenykommandon för en filnod under Blob-lagring](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Öppna Data Lake explorer i portalen
1. Välj Ctrl + Skift + P för att öppna kommandopaletten.
2. Ange **Open Web Azure Storage Explorer** eller högerklicka på en relativ sökväg eller den fullständiga sökvägen i Skriptredigeraren och välj sedan **Open Web Azure Storage Explorer**.
3. Välj ett Data Lake Analytics-konto.

Data Lake-verktyg öppnas Azure Storage-sökvägen i Azure-portalen. Du kan hitta sökvägen och förhandsgranska filen från webben.

## <a name="additional-features"></a>Ytterligare funktioner

Data Lake Tools för VS Code har stöd för följande funktioner:

-   **IntelliSense automatisk komplettering**: Förslag visas i popup-fönster runt exempelvis nyckelord, metoder och variabler. Olika ikoner representerar olika typer av objekt:

    - Datatypen för Scala
    - Komplex datatyp
    - Inbyggda UDT-typer
    - .NET-samling och klasser
    - C#-uttryck
    - Användardefinierade funktioner inbyggda C#, UDO och UDAAGs 
    - U-SQL-funktioner
    - U-SQL-fönsterfunktioner
 
    ![IntelliSense objekttyper](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **IntelliSense funktionen Komplettera automatiskt i Data Lake Analytics metadata**: Data Lake-verktyg hämtar Data Lake Analytics metadatainformation lokalt. Funktionen IntelliSense fyller automatiskt objekt från Data Lake Analytics-metadata. Dessa objekt innefattar database, schema, tabell, vy, tabellvärdesfunktion, procedurer och C#-sammansättningar.
 
    ![IntelliSense metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense fel markör**: Data Lake-verktyg understryker redigering fel för U-SQL och C#. 
-   **Syntax höjdpunkter**: Data Lake-verktyg använder färger för att skilja objekt som variabler, nyckelord, datatyper och funktioner. 

    ![Syntax med olika färger](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Vi rekommenderar att du uppgraderar till Azure Data Lake Tools för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och är nu föråldrade.  
   
## <a name="next-steps"></a>Nästa steg
- [Utveckla U-SQL med Python, R och C Sharp för Azure Data Lake Analytics i VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL-lokal körning och lokal felsökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudie: Kom igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudie: Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
