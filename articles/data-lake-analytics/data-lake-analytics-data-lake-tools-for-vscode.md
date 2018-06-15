---
title: Använda Azure Data Lake-verktyg för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio-koden för att skapa, testa och köra U-SQL-skript.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 79cd1a04c99891e5146ad20cfd36b8bd4fe4d893
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261492"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Använda Azure Data Lake-verktyg för Visual Studio Code

I den här artikeln lär du dig hur du kan använda Azure Data Lake-verktyg för Visual Studio-koden (VS) för att skapa, testa och köra U-SQL-skript. Informationen finns också i följande video:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Förutsättningar

Azure Data Lake-verktyg för VS-koden har stöd för Windows, Linux och MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

För MacOS och Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Monoljud 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installera Azure Data Lake-verktyg

När du har installerat förutsättningarna kan du installera Azure Data Lake-verktyg för VS-kod.

**Så här installerar du Azure Data Lake-verktyg**

1. Öppna Visual Studio Code.
2. Välj **tillägg** i den vänstra rutan. Ange **Azure Data Lake-verktyg** i sökrutan.
3. Välj **installera** bredvid **Azure Data Lake-verktyg**. 

   ![Alternativ för att installera Data Lake-verktyg](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Efter några sekunder den **installera** ändras knappen till **ladda**.
4. Välj **ladda** att aktivera den **Azure Data Lake-verktyg** tillägg.
5. Välj **ladda fönstret** att bekräfta. Du kan se **Azure Data Lake-verktyg** i den **tillägg** fönstret.

 
## <a name="activate-azure-data-lake-tools"></a>Aktivera Azure Data Lake-verktyg
Skapa en .usql-fil eller öppna en befintlig .usql-fil för att aktivera tillägget. 


## <a name="work-with-u-sql"></a>Arbeta med U-SQL

Om du vill arbeta med U-SQL, måste du öppna en U-SQL-fil eller mapp.

**Öppna exempelskript**

Öppna paletten kommando (Ctrl + Skift + P) och ange **ADL: öppna exempelskript**. En annan instans av det här exemplet öppnas. Du kan också redigera, konfigurera och skicka ett skript på den här instansen.

**Öppna en mapp för U-SQL-projekt**

1. Visual Studio Code, Välj den **filen** -menyn och välj sedan **öppna mappen**.
2. Ange en mapp och välj sedan **Välj mappen**.
3. Välj den **filen** -menyn och välj sedan **ny**. En namnlös-1-fil har lagts till i projektet.
4. Ange följande kod i filen Namnlös 1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Skriptet skapar en departments.csv-fil med vissa data som ingår i mappen/Output.

5. Spara filen som **myUSQL.usql** i mappen öppnas.

**Att kompilera ett U-SQL-skript**

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: kompilera skriptet**. Kompilera resultatet visas i den **utdata** fönster. Du kan också högerklicka på en skriptfil och sedan välja **ADL: kompilera skriptet** att kompilera ett U-SQL-jobb. Kompilering resultat visas i den **utdata** fönstret.
 
**Att skicka ett U-SQL-skript**

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: skicka jobbet**. Du kan också högerklicka på en skriptfil och sedan välja **ADL: skicka jobbet**. 

När du skickar ett U-SQL-jobb loggar som skickas visas i den **utdata** fönster i VS-kod. Vyn jobb visas i den högra rutan. Om överföring lyckas visas för URL: en för jobbet. Du kan öppna URL: en för jobb i en webbläsare för att spåra realtid jobbstatus. 

I vyn jobb **sammanfattning** fliken finns i Jobbinformationen. Huvudsakliga funktionerna inkluderar att skicka ett skript, duplicera ett skript och öppna i portalen. I vyn jobb **DATA** kan du referera till inkommande filer, filer och resursfiler. Filer kan laddas ned till den lokala datorn.

![Fliken Sammanfattning i vyn jobb](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Datafliken i vyn jobb](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Ange standardkontexten**

Du kan ange standardkontexten ska gälla alla skriptfiler för den här inställningen om du inte har angett parametrarna för filer individuellt.

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: Ange standardkontexten**. Högerklicka på Skriptredigeraren och välja **ADL: Set Default Context**.
3. Välj kontot, databas och schema som du vill. Inställningen sparas xxx_settings.json konfigurationsfilen.

   ![Kontot, databas och schema som standardkontexten](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Att ange Skriptparametrar**

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: Ange Skriptparametrar**.
3. Xxx_settings.json filen har öppnats med följande egenskaper:

   - **kontot**: ett Azure Data Lake Analytics-konto under din Azure-prenumeration som behövs för att kompilera och köra U-SQL-jobb. Du måste konfigurera kontot innan du kompilera och köra U-SQL-jobb.
   - **databasen**: en databas med ditt konto. Standardvärdet är **master**.
   - **schemat**: ett schema under din databas. Standardvärdet är **dbo**.
   - **optionalSettings**:
        - **prioritet**: prioritetsintervall är från 1 till 1 000 där 1 är den högsta prioriteten. Standardvärdet är **1000**.
        - **degreeOfParallelism**: parallellitet intervall är från 1 till 150. Standardvärdet är maximalt parallellitet tillåts i ditt Azure Data Lake Analytics-konto. 

   ![Innehållet i JSON-fil](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> När du har sparat konfigurationen visas kontot, databasen och schemainformation i statusfältet i det nedre vänstra hörnet på motsvarande .usql fil om du inte har en standardkontexten ställa in.

**Ange Git Ignorera**

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: Ange Git Ignorera**.

   - Om du inte har en **.gitIgnore** filer i arbetsmappen VS-kod på en fil med namnet **.gitIgnore** skapas i mappen. Fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) läggs till i filen som standard. Du kan göra flera uppdateringar om det behövs.
   - Om du redan har en **.gitIgnore** filer i arbetsmappen VS kod verktyget lägger till fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) i din **.gitIgnore** filen, om de fyra objekten inte ingick i filen.

   ![Objekt i .gitIgnore-fil](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeta med bakomliggande kod filer: C skarpa, Python och R

Azure Data Lake-verktygen stöder flera anpassade koder. Instruktioner finns i [utveckla U-SQL med Python, R och C skarpa för Azure Data Lake Analytics i VS kod](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Arbeta med sammansättningar

Information om hur du utvecklar sammansättningar finns [utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-assemblies.md).

Du kan använda Data Lake-verktyg för att registrera anpassad kodsammansättningar i katalogen Data Lake Analytics.

**Att registrera en sammansättning**

Du kan registrera sammansättningen med hjälp av den **ADL: registrera sammansättningen** eller **ADL: registrera sammansättningen (Avancerat)** kommando.

**Att registrera genom ADL: registrera sammansättningen kommando**
1.  Välj Ctrl + Skift + P för att öppna paletten kommando.
2.  Ange **ADL: registrera sammansättningen**. 
3.  Ange sökväg för lokala sammansättning. 
4.  Välj ett Data Lake Analytics-konto.
5.  Välj en databas.

Portalen öppnas i en webbläsare och visar sammansättningen registreringsprocessen.  

Det enklaste sättet att utlösa den **ADL: registrera sammansättningen** kommandot är Högerklicka på .dll-filen i Utforskaren. 

**Att registrera genom ADL: kommandot för att registrera sammansättningen (Avancerat)**
1.  Välj Ctrl + Skift + P för att öppna paletten kommando.
2.  Ange **ADL: registrera sammansättningen (Avancerat)**. 
3.  Ange sökväg för lokala sammansättning. 
4.  JSON-filen visas. Granska och redigera paketberoenden och Resursparametrar, om det behövs. Instruktioner visas i den **utdata** fönster. Spara (Ctrl + S) JSON-filen om du vill fortsätta till registreringen av sammansättningen.

    ![JSON-fil med paketberoenden och Resursparametrar](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake-verktyg autodetects om DLL-filen har alla beroenden för sammansättningen. Beroenden visas i JSON-filen när de har identifierats. 
>- Du kan ladda upp dina DLL-resurser (exempelvis txt, .png och CSV) som en del av registreringen av sammansättningen. 

Ett annat sätt att utlösa den **ADL: registrera sammansättningen (Avancerat)** kommandot är Högerklicka på .dll-filen i Utforskaren. 

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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Använd lokal U-SQL kör och lokala debug för Windows-användare
Lokal U-SQL kör testar dina lokala data och verifierar ditt skript lokalt innan koden publiceras till Data Lake Analytics. Du kan använda lokala debug-funktionen för att slutföra följande uppgifter innan koden skickas till Data Lake Analytics: 
- Felsöka ditt C# bakomliggande kod. 
- Gå igenom koden. 
- Validera skriptet lokalt.

Anvisningar för lokal körning och lokala debug finns [lokal U-SQL kör och lokala debug med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan kompilera och köra U-SQL-skript i Data Lake Analytics, måste du ansluta till ditt Azure-konto.

<b id="sign-in-by-command">Att ansluta till Azure med hjälp av ett kommando</b>

1.  Välj Ctrl + Skift + P för att öppna paletten kommando. 
2.  Ange **ADL: inloggning**. Logga in informationen visas längst ned till höger.

    ![Ange kommandot inloggning](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Meddelanden om inloggning och autentisering](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Välj **Kopiera & Öppna** att öppna den [inloggningen webbsidan](https://aka.ms/devicelogin). Klistra in koden i rutan och välj sedan **Fortsätt**.

    ![Logga in webbsidan](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Följ instruktionerna för att logga in från webbsidan. När du är ansluten, visas namnet på ditt Azure i statusfältet i det nedre vänstra hörnet i fönstret VS-kod. 

> [!NOTE] 
>- Data Lake-verktyg loggar automatiskt du in nästa gång om du inte loggar.
>- Om ditt konto har två faktorer är aktiverat, rekommenderar vi att du använder phone autentisering i stället för en PIN-kod.


Ange kommandot för att logga ut **ADL: Logga ut**.

**Att ansluta till Azure från Utforskaren**

Expandera **AZURE DATALAKE**väljer **logga in på Azure**, och följ sedan steg 3 och 4 för [att ansluta till Azure med hjälp av kommandot](#sign-in-by-command).

![”Logga in på Azure” val i Utforskaren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Du kan inte logga från Utforskaren. Om du vill logga ut finns [att ansluta till Azure med hjälp av kommandot](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Skapa ett skript för extrahering 
Du kan skapa ett skript för extrahering för CSV-TSV och .txt-filer med hjälp av kommandot **ADL: skapa EXTRAHERA skriptet** eller i Azure Data Lake-Utforskaren.

**Skapa ett extrahering skript med hjälp av ett kommando**

1. Välj Ctrl + Skift + P öppna paletten kommando och ange **ADL: skapa EXTRAHERA skriptet**.
2. Ange den fullständiga sökvägen för ett Azure Storage-filen och välj på Enter.
3. Välj ett konto.
4. Välj en avgränsare för att extrahera filen för en txt-fil. 

![Processen för att skapa ett skript för extrahering](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skriptet extrahering genereras baserat på dina uppgifter. Välj ett av de två alternativen för ett skript som inte kan identifiera kolumnerna. Om inte, bara en skript kommer att skapas.

![Resultatet av att skapa ett skript för extrahering](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Skapa ett skript för extrahering från Utforskaren**

Ett annat sätt att skapa skript för extrahering är via snabbmenyn (genväg) på CSV, TSV eller txt-fil i Azure Data Lake Store eller Azure Blob storage.

![”Skapa EXTRAHERA skript” kommando från snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrera med Azure Data Lake Analytics via ett kommando

Du kan komma åt Azure Data Lake Analytics-resurser för att lista över konton, åtkomst metadata och visa analytics-jobb. 

**Att visa en lista med Azure Data Lake Analytics-konton under din Azure-prenumeration**

1. Välj Ctrl + Skift + P för att öppna paletten kommando.
2. Ange **ADL: visa en lista över konton**. Konton som visas i den **utdata** fönstret.

**Åtkomst till Azure Data Lake Analytics-metadata**

1.  Välj Ctrl + Skift + P och ange **ADL: lista tabeller**.
2.  Välj en av Data Lake Analytics-konton.
3.  Välj en av Data Lake Analytics-databaser.
4.  Välj någon av scheman. Du kan se listan över tabeller.

**Visa Azure Data Lake Analytics-jobb**
1.  Öppna paletten kommando (Ctrl + Skift + P) och välj **ADL: Visa jobb**. 
2.  Välj en Data Lake Analytics eller lokalt konto. 
3.  Vänta tills listan över jobb ska visas för kontot.
4.  Välj ett jobb från jobblistan över. Data Lake-verktyg öppnar vyn jobb i den högra rutan och visar viss information i koden VS-utdata.

    ![Jobblistan](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrera med Azure Data Lake Store via ett kommando

Du kan använda Azure Data Lake Store-relaterade kommandon:
 - [Bläddra igenom Azure Data Lake Store-resurser](#list-the-storage-path) 
 - [Förhandsgranska Azure Data Lake Store-fil](#preview-the-storage-file) 
 - [Ladda upp filen direkt till Azure Data Lake Store i VS-kod](#upload-file-or-folder)
 - [Hämta filen direkt från Azure Data Lake Store i VS-kod](#download-file)

### <a name="list-the-storage-path"></a>Sökvägen för lagring 

**Listan lagringssökväg via paletten kommando**

1. Högerklicka på Skriptredigeraren och välj **ADL: listan sökvägen**.
2. Välj mappen i listan eller välj **ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **ange en sökväg** kan t.ex.) 
3. Välj ditt Data Lake Analytics-konto.
4. Bläddra till eller ange sökvägen till lagring (till exempel utdata /).  

Paletten kommando visar sökvägsinformationen baserat på dina uppgifter.

![Lagring sökväg resultat](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Det enklaste sättet att visa den relativa sökvägen är via snabbmenyn.

**Listan lagringssökväg via snabbmenyn**

Högerklicka på sökvägssträngen och välj **lista sökvägen**.

![”Visa en lista med sökvägen” på snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Förhandsgranska lagringsfilen

1. Högerklicka på Skriptredigeraren och välj **ADL: förhandsgranskningsfil**.
2. Välj ditt Data Lake Analytics-konto. 
3. Ange en filsökväg för Azure Storage (till exempel /output/SearchLog.txt). 

Filen öppnas i VS-kod.

![Steg och resultat för att förhandsgranska lagringsfilen](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Det är ett annat sätt att förhandsgranska filen via snabbmenyn som visas på den fullständiga sökvägen eller den relativa sökvägen i Skriptredigeraren. 

### <a name="upload-a-file-or-folder"></a>Överför en fil eller mapp

1. Högerklicka på Skriptredigeraren och välj **överför filen** eller **överför mappen**.
2. Välj en eller flera filer om du har valt **överför filen**, eller välj hela mappen om du har valt **överför mappen**. Välj sedan **överför**. 
3. Välj mappen i listan eller välj **ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **ange en sökväg** kan t.ex.) 
4. Välj ditt Data Lake Analytics-konto. 
5. Bläddra till eller ange sökvägen till lagring (till exempel utdata /). 
6. Välj **Välj aktuella mappen** att ange din målplatsen.

![Steg och resultat för att överföra en fil eller mapp](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Ett annat sätt att överföra filer till lagring är via snabbmenyn som visas på den fullständiga sökvägen eller den relativa sökvägen i Skriptredigeraren.

Du kan [övervaka Överföringsstatusen](#check-storage-tasks-status).


### <a name="download-a-file"></a>Hämta en fil 
Du kan hämta en fil med hjälp av kommandot **ADL: hämta filen** eller **ADL: hämta filen (Avancerat)**.

**Att hämta en fil med ADL: kommandot hämta filen (Avancerat)**
1. Högerklicka på Skriptredigeraren och välj sedan **hämta filen (Avancerat)**.
2. VS-kod visar en JSON-fil. Du kan ange sökvägar och hämta flera filer samtidigt. Instruktioner visas i den **utdata** fönster. Om du vill fortsätta att ladda ned filen eller filerna spara (Ctrl + S) JSON-filen.

    ![JSON-fil med sökvägar för filhämtning](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

Den **utdata** hämta filstatus visas i fönstret.

![Utdatafönstret med download status](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Du kan [övervaka hämtningsstatus](#check-storage-tasks-status).

**Att hämta en fil med ADL: hämta filen kommando**

1. Högerklicka på Skriptredigeraren, Välj **hämta filen**, och välj sedan målmappen från den **Välj mapp** dialogrutan.
2. Välj mappen i listan eller välj **ange en sökväg** eller **Bläddra från rotsökvägen**. (Vi använder **ange en sökväg** kan t.ex.) 
3. Välj ditt Data Lake Analytics-konto. 
4. Bläddra till eller ange sökvägen till lagring (till exempel utdata-) och sedan välja en fil att ladda ned.

![Steg och resultat för att ladda ned en fil](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Ett annat sätt att hämta lagringsfilerna är via snabbmenyn som visas på den fullständiga sökvägen eller den relativa sökvägen i Skriptredigeraren.

Du kan [övervaka hämtningsstatus](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrollera status för lagringsuppgifter
Överföring och hämtning status visas i statusfältet. Välj statusfältet och sedan status visas på den **utdata** fliken.

![Statusfältet och utdata information](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrera med Azure Data Lake Analytics från Utforskaren

När du har loggat in visas alla prenumerationer för ditt Azure-konto i den vänstra rutan under **AZURE DATALAKE**. 

![Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metadatanavigering

Expandera din Azure-prenumeration. Under den **U-SQL-databaser** , du kan bläddra igenom U-SQL-databas och visa mappar som **scheman**, **autentiseringsuppgifter**, **sammansättningar**, **Tabeller**, och **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Hantering av data Lake Analytics metadata entitet

Expandera **U-SQL-databaser**. Du kan skapa en databas, schemat, tabell, tabelltyp, index eller statistik genom att högerklicka på den motsvarande noden och sedan välja **skript för att skapa** på snabbmenyn. På sidan öppna skriptet redigera skriptet efter dina behov. Skicka jobbet genom att högerklicka på filen och välja **ADL: skicka jobbet**. 

När du har skapat objektet, högerklicka på noden och välj sedan **uppdatera** att visa objektet. Du kan också ta bort objektet genom att högerklicka på den och sedan välja **ta bort**.

![”Skript för att skapa” kommandot på snabbmenyn i Utforskaren Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skriptet sidan för det nya objektet](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics regasm.exe för registrering

Du kan registrera en sammansättning i motsvarande databas genom att högerklicka på den **sammansättningar** noden och sedan välja **registrera sammansättningen**.

![”Registrera sammansättningen” kommandot på snabbmenyn för noden sammansättningar](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrera med Azure Data Lake Store från Utforskaren

Bläddra till **Datasjölager**:

- Du kan högerklicka på noden mapp och sedan använda den **uppdatera**, **ta bort**, **överför**, **överför mappen**, **kopia Relativ sökväg**, och **Kopiera fullständig sökväg** kommandon på snabbmenyn.

   ![Kommandon på snabbmenyn för en mapp-nod i Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Du kan högerklicka på noden fil och sedan använda den **Preview**, **hämta**, **ta bort**, **skapa EXTRAHERA skriptet** (endast tillgängligt för CSV TVS, och TXT-filer), **kopiera relativa sökväg**, och **Kopiera fullständig sökväg** kommandon på snabbmenyn.

   ![Kommandon på snabbmenyn för en filnod i Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrera med Azure Blob storage från Utforskaren

Bläddra till Blob storage:

- Du kan högerklicka på noden för blob-behållaren och sedan använda den **uppdatera**, **ta bort Blob-behållaren**, och **överför Blob** kommandon på snabbmenyn.

   ![Kommandon på snabbmenyn för en blob-behållarnod under Blob storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Du kan högerklicka på noden mapp och sedan använda den **uppdatera** och **överför Blob** kommandon på snabbmenyn.

   ![Kommandon på snabbmenyn för en nod för mappen under Blob storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Du kan högerklicka på noden fil och sedan använda den **förhandsgranskning och redigera**, **hämta**, **ta bort**, **skapa EXTRAHERA skriptet** (tillgänglig endast för CSV, TVS och TXT-filer), **kopiera relativa sökväg**, och **Kopiera fullständig sökväg** kommandon på snabbmenyn.

    ![Kommandon på snabbmenyn för en filnod under Blob storage](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Öppna Utforskaren Data Lake i portalen
1. Välj Ctrl + Skift + P för att öppna paletten kommando.
2. Ange **öppna Web Azure Lagringsutforskaren** eller högerklicka på en relativ sökväg eller den fullständiga sökvägen i Skriptredigeraren och välj sedan **öppna Web Azure Lagringsutforskaren**.
3. Välj ett Data Lake Analytics-konto.

Data Lake-verktyg öppnar Azure Storage-sökvägen i Azure-portalen. Du kan hitta sökvägen och förhandsgranska filen från webben.

## <a name="additional-features"></a>Ytterligare funktioner

Data Lake-verktyg för VS-koden har stöd för följande funktioner:

-   **IntelliSense autocomplete**: förslag visas i popup-fönster runt objekt som nyckelord, metoder och variabler. Olika ikoner representerar olika typer av objekt:

    - Scala-datatyp
    - Komplex datatyp
    - Inbyggda UDT-typer
    - .NET-samling och klasser
    - C#-uttryck
    - Inbyggda C# UDF: er och UDO UDAAGs 
    - U-SQL-funktioner
    - U-SQL fönsterhantering-funktioner
 
    ![IntelliSense objekttyper](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **IntelliSense autocomplete på Data Lake Analytics metadata**: Data Lake-verktyg hämtar Data Lake Analytics metadatainformationen lokalt. Funktionen IntelliSense fyller automatiskt objekt från Data Lake Analytics-metadata. Dessa objekt innefattar databasen, schemat, tabell, visa, tabellvärdesfunktion, procedurer och C#-sammansättningar.
 
    ![IntelliSense metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense fel markör**: Data Lake-verktyg understryker redigera fel för U-SQL och C#. 
-   **Syntax visar**: färger används i Data Lake-verktyg för att skilja mellan saker som variabler, nyckelord, datatyper och funktioner. 

    ![Syntax för olika färger](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Vi rekommenderar att du uppgraderar till Azure Data Lake-verktyg för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och nu är föråldrade.  
   
## <a name="next-steps"></a>Nästa steg
- [Utveckla U-SQL med Python, R och C skarpa för Azure Data Lake Analytics i VS-kod](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokal U-SQL kör och lokala debug med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudier: Kom igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudier: Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
