---
title: Använda Azure Data Lake Tools för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio-kod för att skapa, testa och köra U-SQL-skript.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60509348"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Använda Azure Data Lake Tools för Visual Studio Code

I den här artikeln kan du lära dig hur du kan använda Azure Data Lake Tools for Visual Studio Code (VS Code) för att skapa, testa och köra U-SQL-skript. Informationen behandlas också i följande video:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Krav

Azure Data Lake Tools för VS-kod stöder Windows, Linux och macOS.U-SQL lokal körning och lokal felsökning fungerar bara i Windows.

- [Visual Studio-kod](https://www.visualstudio.com/products/code-vs.aspx)

För MacOS och Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installera Azure Data Lake Tools

När du har installerat förutsättningarna kan du installera Azure Data Lake Tools för VS-kod.

**Så här installerar du Azure Data Lake Tools**

1. Öppna Visual Studio Code.
2. Välj **Tillägg** i den vänstra rutan. Ange **Azure Data Lake Tools** i sökrutan.
3. Välj **Installera** bredvid **Azure Data Lake Tools**. 

   ![Val för installation av DataSjöverktyg](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Efter några sekunder ändras knappen **Installera** till **Reload**.
4. Välj **Ladda om** för att aktivera Azure Data Lake **Tools-tillägget.**
5. Välj **Ladda om fönstret** för att bekräfta. Du kan se **Azure Data Lake Tools** i fönstret **Tillägg.**

 
## <a name="activate-azure-data-lake-tools"></a>Aktivera Azure Data Lake Tools
Skapa en USQL-fil eller öppna en befintlig USQL-fil för att aktivera tillägget. 


## <a name="work-with-u-sql"></a>Arbeta med U-SQL

Om du vill arbeta med U-SQL måste du öppna antingen en U-SQL-fil eller en mapp.

**Så här öppnar du exempelskriptet**

Öppna kommandopaletten (Ctrl+Skift+P) och ange **ADL: Öppna exempelskript**. En annan instans av det här exemplet öppnas. Du kan också redigera, konfigurera och skicka ett skript på den här instansen.

**Så här öppnar du en mapp för ditt U-SQL-projekt**

1. Välj **Arkiv-menyn** på Visual Studio-kod och välj sedan **Öppna mapp**.
2. Ange en mapp och välj sedan **Välj mapp**.
3. Markera **Arkiv-menyn** och välj sedan **Ny**. En Namnlös-1-fil läggs till i projektet.
4. Ange följande kod i filen Untitled-1:

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

    Skriptet skapar en departments.csv-fil med vissa data som ingår i mappen /output.

5. Spara filen som **myUSQL.usql** i den öppnade mappen.

**Så här kompilerar du ett U-SQL-skript**

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten. 
2. Ange **ADL: Kompilera skript**. Kompileringsresultaten visas i **utdatafönstret.** Du kan också högerklicka på en skriptfil och sedan välja **ADL: Kompilera skript** för att kompilera ett U-SQL-jobb. Kompileringsresultatet visas i **fönstret Utdata.**
 
**Så här skickar du ett U-SQL-skript**

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten. 
2. Ange **ADL: Skicka jobb**. Du kan också högerklicka på en skriptfil och sedan välja **ADL: Skicka jobb**. 

När du har skickat in ett U-SQL-jobb visas inlämningsloggarna i **utdatafönstret** i VS-kod. Jobbvyn visas i den högra rutan. Om överföringen lyckas visas också jobb-URL:en. Du kan öppna jobb-URL:en i en webbläsare för att spåra jobbstatusen i realtid. 

På fliken **SAMMANFATTNING** i jobbvyn kan du se jobbinformationen. Huvudfunktionerna är att skicka ett skript igen, duplicera ett skript och öppna i portalen. På fliken DATA **i** jobbvyn kan du referera till indatafiler, utdatafiler och resursfiler. Filer kan hämtas till den lokala datorn.

![Fliken Sammanfattning i jobbvyn](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Fliken Data i jobbvyn](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Så här anger du standardkontexten**

Du kan ange standardkontexten för att tillämpa den här inställningen på alla skriptfiler om du inte har angett parametrar för filer individuellt.

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten. 
2. Ange **ADL: Ange standardkontext**. Eller högerklicka på skriptredigeraren och välj **ADL: Ange standardkontext**.
3. Välj det konto, den databas och det schema du vill använda. Inställningen sparas i konfigurationsfilen xxx_settings.json.

   ![Konto-, databas- och schemauppsättning som standardkontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Så här anger du skriptparametrar**

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten. 
2. Ange **ADL: Ange skriptparametrar**.
3. Filen xxx_settings.json öppnas med följande egenskaper:

   - **konto**: Ett Azure Data Lake Analytics-konto under din Azure-prenumeration som behövs för att kompilera och köra U-SQL-jobb. Du måste konfigurera datorkontot innan du kompilerar och kör U-SQL-jobb.
   - **databas:** En databas under ditt konto. Standard är **huvud .**
   - **schema**: Ett schema under databasen. Standard är **dbo**.
   - **tillvalSinställningar:**
        - **prioritet**: Prioritetsintervallet är från 1 till 1000, med 1 som högsta prioritet. Standardvärdet är **1000**.
        - **gradParallelism**: Parallellismen spänner är från 1 till 150. Standardvärdet är den maximala parallellitet som tillåts i ditt Azure Data Lake Analytics-konto. 

   ![Innehållet i JSON-filen](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> När du har sparat konfigurationen visas konto-, databas- och schemainformationen i statusfältet längst ned till vänster i motsvarande USQL-fil om du inte har konfigurerat en standardkontext.

**Så här anger du Git-ignorera**

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten. 
2. Ange **ADL: Ange Git Ignorera**.

   - Om du inte har en **.gitIgnore-fil** i arbetsmappen VS-kod skapas en fil med namnet **.gitIgnore** i mappen. Fyra objekt **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) läggs till i filen som standard. Du kan göra fler uppdateringar om det behövs.
   - Om du redan har en **.gitIgnore-fil** i arbetsmappen VS-kod lägger verktyget till fyra objekt **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) i filen **.gitIgnore** om de fyra objekten inte ingick i filen.

   ![Objekt i .gitIgnore-filen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeta med bakomkodfiler: C Sharp, Python och R

Azure Data Lake Tools stöder flera anpassade koder. Instruktioner finns i [Utveckla U-SQL med Python, R och C Sharp för Azure Data Lake Analytics i VS-kod](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Arbeta med sammansättningar

Information om hur du utvecklar sammansättningar finns i [Utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-assemblies.md).

Du kan använda DataSjöverktyg för att registrera anpassade kodsammansättningar i DataSjöanalyskatalogen.

**Så här registrerar du en sammansättning**

Du kan registrera sammansättningen via **ADL: Register Assembly** eller **ADL: Register Assembly (Advanced)** kommando.

**Så här registrerar du dig via kommandot ADL: Register Assembly**
1.  Välj Ctrl+Skift+P om du vill öppna kommandopaletten.
2.  Ange **ADL: Registrera montering**. 
3.  Ange den lokala monteringssökvägen. 
4.  Välj ett DataSjöanalyskonto.
5.  Välj en databas.

Portalen öppnas i en webbläsare och visar monteringsregistreringsprocessen.  

Ett bekvämare sätt att utlösa kommandot **ADL: Register Assembly** är att högerklicka på DLL-filen i Utforskaren. 

**Så här registrerar du dig via kommandot ADL: Register Assembly (Advanced)**
1.  Välj Ctrl+Skift+P om du vill öppna kommandopaletten.
2.  Ange **ADL: Registrera montering (avancerat)**. 
3.  Ange den lokala monteringssökvägen. 
4.  JSON-filen visas. Granska och redigera monteringsberoenden och resursparametrar om det behövs. Instruktioner visas i **utdatafönstret.** Om du vill gå vidare till sammansättningsregistreringen sparar du (Ctrl+S) filen JSON.

    ![JSON-fil med monteringsberoenden och resursparametrar](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools kontrollerar automatiskt om DLL-filen har några sammansättningsberoenden. Beroendena visas i JSON-filen när de har identifierats. 
>- Du kan ladda upp DLL-resurser (till exempel .txt, .png och CSV) som en del av monteringsregistreringen. 

Ett annat sätt att utlösa **adl:registrera sammansättningen (Avancerat)** är att högerklicka på DLL-filen i Utforskaren. 

Följande U-SQL-kod visar hur du anropar en sammansättning. I exemplet är sammansättningsnamnet *test*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Använda U-SQL lokal körning och lokal felsökning för Windows-användare
U-SQL lokal körning testar dina lokala data och validerar skriptet lokalt innan koden publiceras i Data Lake Analytics. Du kan använda den lokala felsökningsfunktionen för att slutföra följande uppgifter innan koden skickas till Data Lake Analytics: 
- Felsök din C#-kod bakom. 
- Gå igenom koden. 
- Validera skriptet lokalt.

Den lokala körnings- och den lokala felsökningsfunktionen fungerar bara i Windows-miljöer och stöds inte på macOS- och Linux-baserade operativsystem.

Instruktioner om lokal körning och lokal felsökning finns i [U-SQL lokal körning och lokal felsökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan kompilera och köra U-SQL-skript i Data Lake Analytics måste du ansluta till ditt Azure-konto.

<b id="sign-in-by-command">Så här ansluter du till Azure med hjälp av ett kommando</b>

1.  Välj Ctrl+Skift+P om du vill öppna kommandopaletten. 
2.  Ange **ADL: Logga in**. Inloggningsinformationen visas längst ned till höger.

    ![Ange inloggningskommandot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Meddelande om inloggning och autentisering](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Välj **Kopiera & Öppna** för att öppna [inloggningswebbsidan](https://aka.ms/devicelogin). Klistra in koden i rutan och välj sedan **Fortsätt**.

    ![Inloggningswebbsida](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Följ instruktionerna för att logga in från webbsidan. När du är ansluten visas ditt Azure-kontonamn i statusfältet i det nedre vänstra hörnet av VS-kodfönstret. 

> [!NOTE] 
>- DataSjöverktyg signerar dig automatiskt nästa gång om du inte loggar ut.
>- Om ditt konto har två aktiverade faktorer rekommenderar vi att du använder telefonautentisering i stället för att använda en PIN-kod.


Om du vill logga ut anger du kommandot **ADL: Logout**.

**Så här ansluter du till Azure från utforskaren**

Expandera **AZURE DATALAKE**, välj **Logga in på Azure**och följ sedan steg 3 och steg 4 i För att ansluta till Azure med hjälp av ett [kommando](#sign-in-by-command).

!["Logga in på Azure" i utforskaren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Du kan inte logga ut från utforskaren. Information om hur du loggar ut finns [i Ansluta till Azure med hjälp av ett kommando](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Skapa ett extraheringsskript 
Du kan skapa ett extraheringsskript för CSV-, .tsv- och .txt-filer med kommandot **ADL: Skapa EXTRAHERA skript** eller från Utforskaren i Azure Data Lake.

**Så här skapar du ett extraheringsskript med hjälp av ett kommando**

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten och ange **ADL: Skapa EXTRAHERA skript**.
2. Ange den fullständiga sökvägen för en Azure Storage-fil och välj Retur-tangenten.
3. Välj ett konto.
4. För en TXT-fil väljer du en avgränsare för att extrahera filen. 

![Process för att skapa ett extraheringsskript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Extraheringsskriptet genereras baserat på dina poster. För ett skript som inte kan identifiera kolumnerna väljer du ett bland de två alternativen. Om inte, kommer bara ett skript att genereras.

![Resultatet av att skapa ett extraheringsskript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Så här skapar du ett extraheringsskript från utforskaren**

Ett annat sätt att skapa extraheringsskriptet är via högerklicksmenyn (genväg) på CSV-, .tsv- eller .txt-filen i Azure Data Lake Store eller Azure Blob storage.

![Kommandot "Skapa EXTRACT Script" på snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrera med Azure Data Lake Analytics via ett kommando

Du kan komma åt Azure Data Lake Analytics-resurser för att lista konton, komma åt metadata och visa analysjobb. 

**Så här listar du Azure Data Lake Analytics-konton under din Azure-prenumeration**

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten.
2. Ange **ADL: Listkonton**. Kontona visas i **fönstret Utdata.**

**Så här kommer du åt Azure Data Lake Analytics-metadata**

1.  Välj Ctrl+Skift+P och ange sedan **ADL: Listtabeller**.
2.  Välj ett av DataSjöanalyskontona.
3.  Välj en av DataSjöanalysdatabaserna.
4.  Välj ett av schemana. Du kan se listan över tabeller.

**Så här visar du Azure Data Lake Analytics-jobb**
1.  Öppna kommandopaletten (Ctrl+Skift+P) och välj **ADL: Visa jobb**. 
2.  Välj en DataSjöanalys eller ett lokalt konto. 
3.  Vänta tills jobblistan visas för kontot.
4.  Välj ett jobb i jobblistan. Datasjöverktyg öppnar jobbvyn i den högra rutan och visar viss information i VS-kodutdata.

    ![Jobblista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrera med Azure Data Lake Store via ett kommando

Du kan använda Azure Data Lake Store-relaterade kommandon för att:
 - [Bläddra igenom Azure Data Lake Store-resurserna](#list-the-storage-path) 
 - [Förhandsgranska Azure Data Lake Store-filen](#preview-the-storage-file) 
 - Ladda upp filen direkt till Azure Data Lake Store i VS-kod
 - Hämta filen direkt från Azure Data Lake Store i VS-kod

### <a name="list-the-storage-path"></a>Lista lagringssökvägen 

**Så här listar du lagringssökvägen via kommandopaletten**

1. Högerklicka på skriptredigeraren och välj **ADL: Listsökväg**.
2. Välj mappen i listan eller välj **Ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **Ange en sökväg** som exempel.) 
3. Välj ditt DataSjöanalyskonto.
4. Bläddra till eller ange sökvägen till lagringsmappen (till exempel /output/).  

Kommandopaletten visar sökvägsinformationen baserat på dina poster.

![Resultat av lagringssökväg](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Ett bekvämare sätt att lista den relativa sökvägen är via snabbmenyn.

**Så här listar du lagringssökvägen via snabbmenyn**

Högerklicka på sökvägssträngen och välj **Listbana**.

!["Listsökväg" på snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Förhandsgranska lagringsfilen

1. Högerklicka på skriptredigeraren och välj **ADL: Förhandsgranska fil**.
2. Välj ditt DataSjöanalyskonto. 
3. Ange en Azure Storage-filsökväg (till exempel /output/SearchLog.txt). 

Filen öppnas i VS-kod.

![Steg och resultat för att förhandsgranska lagringsfilen](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Ett annat sätt att förhandsgranska filen är via snabbmenyn på filens fullständiga sökväg eller filens relativa sökväg i skriptredigeraren. 

### <a name="upload-a-file-or-folder"></a>Ladda upp en fil eller mapp

1. Högerklicka på skriptredigeraren och välj **Ladda upp fil** eller ladda upp **mapp**.
2. Välj en eller flera filer om du har valt **Ladda upp fil**eller välj hela mappen om du valde Ladda upp **mapp**. Välj sedan **Ladda upp**. 
3. Välj lagringsmappen i listan eller välj **Ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **Ange en sökväg** som exempel.) 
4. Välj ditt DataSjöanalyskonto. 
5. Bläddra till eller ange sökvägen till lagringsmappen (till exempel /output/). 
6. Välj **Välj Aktuell mapp** för att ange ditt uppladdningsmål.

![Steg och resultat för att ladda upp en fil eller mapp](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Ett annat sätt att ladda upp filer till lagring är via snabbmenyn på filens fullständiga sökväg eller filens relativa sökväg i skriptredigeraren.

Du kan [övervaka uppladdningsstatus](#check-storage-tasks-status).


### <a name="download-a-file"></a>Hämta en fil 
Du kan ladda ner en fil med kommandot **ADL: Download File** eller **ADL: Download File (Advanced)**.

**Så här hämtar du en fil via kommandot ADL: Download File (Advanced)**
1. Högerklicka på skriptredigeraren och välj sedan **Hämta fil (Avancerat).**
2. VS-kod visar en JSON-fil. Du kan ange filsökvägar och hämta flera filer samtidigt. Instruktioner visas i **utdatafönstret.** Om du vill fortsätta hämta filen eller filerna sparar du (Ctrl+S) filen JSON.

    ![JSON-fil med sökvägar för filhämtning](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

I **utdatafönstret** visas filhämtningsstatusen.

![Utdatafönster med hämtningsstatus](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Du kan [övervaka hämtningsstatusen](#check-storage-tasks-status).

**Så här hämtar du en fil via kommandot ADL: Download File**

1. Högerklicka på skriptredigeraren, välj **Hämta fil**och välj sedan målmappen i dialogrutan **Välj mapp.**
2. Välj mappen i listan eller välj **Ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **Ange en sökväg** som exempel.) 
3. Välj ditt DataSjöanalyskonto. 
4. Bläddra till eller ange sökvägen till lagringsmappen (till exempel /output/) och välj sedan en fil att hämta.

![Steg och resultat för hämtning av en fil](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Ett annat sätt att hämta lagringsfiler är via snabbmenyn på filens fullständiga sökväg eller filens relativa sökväg i skriptredigeraren.

Du kan [övervaka hämtningsstatusen](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrollera lagringsuppgifternas status
Uppladdnings- och hämtningsstatusen visas i statusfältet. Markera statusfältet och sedan visas statusen på fliken **UTdata.**

![Statusfält och utdatainformation](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrera med Azure Data Lake Analytics från utforskaren

När du har loggat in visas alla prenumerationer för ditt Azure-konto i den vänstra rutan under **AZURE DATALAKE**. 

![Utforskaren för Datasjö](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigering i metadata för DataSjöanalys

Utöka din Azure-prenumeration. Under noden **U-SQL-databaser** kan du bläddra igenom din U-SQL-databas och visa mappar som **Tables** **scheman,** **autentiseringsuppgifter,** **sammansättningar,** tabeller och **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Hantering av metadata för datasjöanalys

Expandera **U-SQL-databaser**. Du kan skapa en databas, ett schema, en tabell, en tabelltyp, ett index eller en statistik genom att högerklicka på motsvarande nod och sedan välja **Skript som ska skapas** på snabbmenyn. På den öppnade skriptsidan redigerar du skriptet efter dina behov. Skicka sedan jobbet genom att högerklicka på det och välja **ADL: Skicka jobb**. 

När du har skapat objektet högerklickar du på noden och väljer sedan **Uppdatera för** att visa objektet. Du kan också ta bort objektet genom att högerklicka på det och sedan välja **Ta bort**.

![Kommandot "Script to Create" på snabbmenyn i Utforskaren i Datasjö](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skriptsida för det nya objektet](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrering av sammansättningen DataSjöanalys

Du kan registrera en sammansättning i motsvarande databas genom att högerklicka på noden **Sammansättningar** och sedan välja **Registrera montering**.

![Kommandot "Registrera montering" på snabbmenyn för noden Sammansättningar](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrera med Azure Data Lake Store från utforskaren

Bläddra till **Data Lake Store:**

- Du kan högerklicka på mappnoden och sedan använda **kommandona Uppdatera,** **Ta bort,** **Ladda upp,** **Ladda upp mapp,** **Kopiera relativ sökväg**och **Kopiera fullständig sökväg** på snabbmenyn.

   ![Snabbmenykommandon för en mappnod i Utforskaren i Datasjö](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Du kan högerklicka på filnoden och sedan använda **kommandona Förhandsgranska**, **Hämta**, **Ta bort**, Skapa **EXTRAHERA skript** (endast tillgängligt för CSV-, TSV- och TXT-filer), Kopiera relativ **sökväg**och **Kopiera fullständiga sökvägskommandon** på snabbmenyn.

   ![Snabbmenykommandon för en filnod i Utforskaren i Datasjö](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrera med Azure Blob-lagring från utforskaren

Bläddra till Blob-lagring:

- Du kan högerklicka på blob-behållarnoden och sedan använda **kommandona Uppdatera,** **Ta bort blob-behållare**och **Ladda upp blob** på snabbmenyn.

   ![Snabbmenykommandon för en blob-behållarnod under Blob-lagring](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Du kan högerklicka på mappnoden och sedan använda **kommandona Uppdatera** och **ladda upp blob** på snabbmenyn.

   ![Snabbmenykommandon för en mappnod under Blob-lagring](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Du kan högerklicka på filnoden och sedan använda **kommandona Förhandsgranska/Redigera,** **Hämta**, **Skapa** **EXTRAHERA skript** (endast tillgängligt för CSV-, TSV- och TXT-filer), Kopiera **relativ sökväg**och **Kopiera fullständiga sökvägskommandon** på snabbmenyn.

    ![Snabbmenykommandon för en filnod under Blob-lagring](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Öppna utforskaren för Datasjö i portalen
1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten.
2. Ange **Öppna Web Azure Storage Explorer** eller högerklicka på en relativ sökväg eller den fullständiga sökvägen i skriptredigeraren och välj sedan Öppna Web Azure Storage **Explorer**.
3. Välj ett DataSjöanalyskonto.

DataSjöverktyg öppnar Sökvägen till Azure Storage i Azure-portalen. Du hittar sökvägen och förhandsgranskar filen från webben.

## <a name="additional-features"></a>Ytterligare funktioner

DataSjöverktyg för VS-kod stöder följande funktioner:

-   **IntelliSense autocomplete**: Förslag visas i popup-fönster runt objekt som nyckelord, metoder och variabler. Olika ikoner representerar olika typer av objekt:

    - Datatyp för Scala
    - Komplex datatyp
    - Inbyggda UDT:er
    - .NET-samling och klasser
    - C#-uttryck
    - Inbyggda C# UDFs, UDOs och UDAAGs 
    - U-SQL-funktioner
    - U-SQL-fönsterfunktioner
 
    ![Objekttyper för IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **IntelliSense komplettera automatiskt på Data Lake Analytics metadata:** Data Lake Tools hämtar datasjöanalys metadatainformation lokalt. IntelliSense-funktionen fyller automatiskt i objekt från DataSjöanalysmetadata. Dessa objekt omfattar databas-, schema-, tabell-, vy-, tabellvärderad funktion, procedurer och C#-sammansättningar.
 
    ![IntelliSense-metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense-felmarkör:** Datasjöverktyg understryker redigeringsfel för U-SQL och C#. 
-   **Syntaxhöjdpunkter**: Datasjöverktyg använder färger för att skilja objekt som variabler, nyckelord, datatyper och funktioner. 

    ![Syntax med olika färger](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Vi rekommenderar att du uppgraderar till Azure Data Lake Tools för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och är nu föråldrade.  
   
## <a name="next-steps"></a>Nästa steg
- [Utveckla U-SQL med Python, R och C Sharp för Azure Data Lake Analytics i VS-kod](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL lokal körning och lokal felsökning med Visual Studio-kod](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudiekurs: Kom igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudiekurs: Utveckla U-SQL-skript med hjälp av DataSjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
