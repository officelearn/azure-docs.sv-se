---
title: "Azure Data Lake-verktyg: Använd Azure Data Lake-verktyg för Visual Studio Code | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio-koden för att skapa, testa och köra U-SQL-skript. "
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: jejiang
ms.openlocfilehash: 7e1e2c0a5481a81e9267bcf87076076b377a1496
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Använda Azure Data Lake-verktyg för Visual Studio Code

Läs Azure Data Lake-verktyg för Visual Studio-koden (VS) att skapa, testa och köra U-SQL-skript. Informationen finns också i följande video:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Förutsättningar

Azure Data Lake-verktyg för VSCode stöder Windows, Linux och MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

För MacOS och Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Monoljud 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Installera Data Lake-verktyg

När du har installerat förutsättningarna kan du installera Data Lake-verktyg för VS-kod.

**Så här installerar du Data Lake-verktyg**

1. Öppna Visual Studio-koden.
2. Klicka på **tillägg** i den vänstra rutan. Ange **Azure Data Lake** i sökrutan.
3. Klicka på **installera** bredvid **Azure Data Lake-verktyg**. Efter några sekunder den **installera** knappen ändras till **ladda**.
4. Klicka på **ladda** att aktivera den **Azure Data Lake-verktyg** tillägg.
5. Klicka på **ladda fönstret** att bekräfta. Du kan se **Azure Data Lake-verktyg** i fönstret tillägg.

    ![Data Lake-verktyg för Visual Studio Code-tillägg](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Aktivera Azure Data Lake-verktyg
Skapa en ny .usql fil eller öppna en befintlig .usql-fil för att aktivera tillägget. 

## <a name="open-the-sample-script"></a>Öppna exempelskript
Öppna paletten kommando (Ctrl + Skift + P) och ange **ADL: öppna exempelskript**. En annan instans av det här exemplet öppnas. Du kan också redigera, konfigurera och skicka skript på den här instansen.

## <a name="work-with-u-sql"></a>Arbeta med U-SQL

Du måste öppna ett U-SQL-fil eller mapp att arbeta med U-SQL.

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
2. Ange **ADL: skicka jobbet**.  Du kan också högerklicka på en skriptfil och sedan välja **ADL: skicka jobbet**. 

 När du skickar ett U-SQL-jobb loggar som skickas visas i den **utdata** fönster i VS-kod. Visa jobb visas den högra rutan. Om överföring lyckas jobbet URL visas också. Du kan öppna URL: en för jobb i en webbläsare för att spåra jobbstatusen standarddatakällan i realtid. Du kan se Jobbinformationen för jobb visa sammanfattning på fliken. Huvudfunktionerna ta skicka skript, duplicera skript, öppna i portalen. Visa jobbdata på fliken kan du referera till indata-filer, filer, resurs. Filer kan laddas ned till den lokala datorn.

   ![Data Lake-verktyg för Visual Studio Code konfigurationsfil](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![Data Lake-verktyg för Visual Studio Code konfigurationsfil](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Ange standardkontext**

 Du kan ange standardkontexten ska gälla alla skriptfiler för den här inställningen om du inte har angett parametrarna för filen respektive.

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: Ange standardkontexten**.
3. Eller högerklicka på Skriptredigeraren och välj **ADL: Set Default Context**, Välj kontot, databas och schema som du vill använda. Inställningen sparas xxx_settings.json konfigurationsfilen.

    ![Data Lake-verktyg för Visual Studio Code konfigurationsfil](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Ange Skriptparametrar**

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: Ange Skriptparametrar**.
3. xxx_settings.JSON filen har öppnats med följande egenskaper:

  - Konto: Ett Data Lake Analytics-konto under din Azure-prenumeration som behövs för att kompilera och köra U-SQL-jobb, så du måste konfigurera kontot innan du kompilera och köra U-SQL-jobb.
    - Databas: En databas med ditt konto. Standardvärdet är **master**.
    - Schema: Ett schema under din databas. Standardvärdet är **dbo**.
    - Valfria inställningar:
        - Prioritet: Prioritet intervallet är från 1 till 1000 med 1 är den högsta prioriteten. Standardvärdet är **1000**.
        - Parallellitet: Parallellitet-intervallet är från 1 till 150. Standardvärdet är maximalt parallellitet tillåts i ditt Azure Data Lake Analytics-konto. 

        ![Data Lake-verktyg för Visual Studio Code konfigurationsfil](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > När konfigurationen har sparats visas kontot, databasen och schemainformation i statusfältet i nedre vänstra hörnet av motsvarande .usql-fil om du inte har standardkontexten ställa in.

**Ange Git Ignorera**

1. Välj Ctrl + Skift + P för att öppna paletten kommando. 
2. Ange **ADL: Ange Git Ignorera**.

    - Om du inte har en **.gitIgnore** filer i arbetsmappen VSCode på en fil med namnet **.gitIgnor** skapas i mappen. Fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) läggs till i filen som standard. Ytterligare kan du göra uppdateringar om det behövs.
    - Om du redan har en **.gitIgnore** filer i arbetsmappen VSCode verktyget lägger till fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) till din **.gitIgnore** filen, om de fyra objekten inte ingick i filen.

    ![Data Lake-verktyg för Visual Studio Code konfigurationsfil](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Använda Python, R och CSharp bakomliggande kod-fil
Azure Data Lake-verktyget stöder flera anpassade koder finns i anvisningarna [utveckla U-SQL med Python, R och CSharp för Azure Data Lake Analytics i VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Använd sammansättningar

Information om hur du utvecklar sammansättningar finns [utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-assemblies.md).

Du kan använda Data Lake-verktyg för att registrera anpassad kodsammansättningar i katalogen Data Lake Analytics.

**Att registrera en sammansättning**

Du kan registrera sammansättningen med hjälp av den **ADL: registrera sammansättningen** eller **ADL: registrera sammansättningen (Avancerat)** kommandon.

**Att registrera genom ADL: registrera sammansättningen kommando**
1.  Välj Ctrl + Skift + P för att öppna paletten kommando.
2.  Ange **ADL: registrera sammansättningen**. 
3.  Ange sökväg för lokala sammansättning. 
4.  Välj ett Data Lake Analytics-konto.
5.  Välj en databas.

Resultat: Portalen öppnas i en webbläsare och visar sammansättningen registreringsprocessen.  

Ett annat praktiskt sätt att utlösa den **ADL: registrera sammansättningen** kommandot är Högerklicka på .dll-filen i Utforskaren. 

**Att registrera om ADL: registrera sammansättningen (Avancerat)**
1.  Välj Ctrl + Skift + P för att öppna paletten kommando.
2.  Ange **ADL: registrera sammansättningen (Avancerat)**. 
3.  Ange sökväg för lokala sammansättning. 
4.  JSON-filen visas. Granska och redigera paketberoenden och Resursparametrar, om det behövs. Instruktioner visas i den **utdata** fönster. Spara (Ctrl + S) JSON-filen om du vill fortsätta till registreringen av sammansättningen.

    ![Data Lake-verktyg för Visual Studio Code bakomliggande kod](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- Beroenden för sammansättningen: Azure Data Lake-verktyg autodetects om DLL-filen har några beroenden. Beroenden visas i JSON-filen när de identifieras. 
   >- Resurser: Du kan överföra dina DLL-resurser (exempelvis txt, .png och CSV) som en del av registreringen av sammansättningen. 

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


## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan kompilera och köra U-SQL-skript i Data Lake Analytics, måste du ansluta till ditt Azure-konto.

**Att ansluta till Azure**

1.  Välj Ctrl + Skift + P för att öppna paletten kommando. 
2.  Ange **ADL: inloggning**. Inloggningsinformation visas på ytan.

    ![Data Lake-verktyg för Visual Studio Code kommandot paletten](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Data Lake-verktyg för Visual Studio Code enhetsinformation för inloggning](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Klicka på **Kopiera & Öppna** att öppna webbsidan inloggningen med URL: https://aka.ms/devicelogin. Klistra in koden **G567LX42V** i textrutan och välj sedan **Fortsätt**.

   ![Data Lake-verktyg för Visual Studio Code inloggningen klistra in koden](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Följ instruktionerna för att logga in från webbsidan. När du är ansluten Azure kontonamnet visas i statusfältet i det nedre vänstra hörnet i det **VS kod** fönster. 

    > [!NOTE] 
    >- Data Lake-verktyg loggar automatiskt in nästa gång om du har loggat in innan, men du inte har loggat ut ännu.
    >- Om ditt konto har två faktorer är aktiverat, rekommenderar vi att du använder phone autentisering i stället för en PIN-kod.


Ange kommandot för att logga ut **ADL: Logga ut**.

## <a name="list-your-data-lake-analytics-accounts"></a>Visa en lista över dina Data Lake Analytics-konton

Hämta en lista över dina Data Lake Analytics-konton för att testa anslutningen.

**Att visa Data Lake Analytics-konton under din Azure-prenumeration**

1. Välj Ctrl + Skift + P för att öppna paletten kommando.
2. Ange **ADL: visa en lista över konton**. Konton som visas i den **utdata** fönstret.


## <a name="access-the-data-lake-analytics-catalog"></a>Få tillgång till Data Lake Analytics-katalogen

Du kan använda följande steg för att komma åt U-SQL-katalogen när du har anslutit till Azure.

**Åtkomst till Azure Data Lake Analytics-metadata**

1.  Välj Ctrl + Skift + P och ange **ADL: lista tabeller**.
2.  Välj en av Data Lake Analytics-konton.
3.  Välj en av Data Lake Analytics-databaser.
4.  Välj någon av scheman. Du kan se listan över tabeller.

## <a name="view-data-lake-analytics-jobs"></a>Visa Data Lake Analytics-jobb

**Visa Data Lake Analytics-jobb**
1.  Öppna paletten kommando (Ctrl + Skift + P) och välj **ADL: Visa jobb**. 
2.  Välj en Data Lake Analytics eller lokalt konto. 
3.  Vänta tills listan jobb för kontot som ska visas.
4.  Välj ett jobb jobblistan, Data Lake-verktyg öppnar vyn jobb i den högra rutan och visar viss information i VS kod **utdata**.

    ![Data Lake-verktyg för Visual Studio Code IntelliSense objekttyper](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake Lagringsintegrering

Du kan använda lagring av Azure Data Lake-relaterade kommandon:
 - Bläddra igenom Azure Data Lake-lagringsresurser. [Storage-sökvägen](#list-the-storage-path). 
 - Förhandsgranska Azure Data Lake Storage-fil. [Förhandsgranska lagringsfilen](#preview-the-storage-file). 
 - Ladda upp filen direkt till Azure Data Lake-lagring i VS-kod. [Ladda upp filen eller mappen](#upload-file-or-folder).
 - Hämta filen direkt från Azure Data Lake Storage i VS-kod. [Hämta filen](#download-file).

### <a name="list-the-storage-path"></a>Sökvägen för lagring 

**Listan lagringssökväg via paletten kommando**

Högerklicka på Skriptredigeraren och välj **ADL: listan sökvägen**.

Välj mappen i listan eller klicka på **ange sökvägen** eller **Bläddra från roten** (använder ange en sökväg som exempel). Välj -> din **ADLA konto**. Analysera -> eller ange sökvägen till lagring (till exempel: / utdata /). -> Kommandot paletten listorna sökvägsinformationen baserat på dina uppgifter.

![Data Lake-verktyg för Visual Studio Code lista lagring sökväg resultat](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Det enklaste sättet att visa den relativa sökvägen är via snabbmenyn.

**Listan lagringssökväg genom att högerklicka på**

Högerklicka på sökvägssträngen för att välja **lista sökvägen** att fortsätta.

![Data Lake-verktyg för Visual Studio Code högerklickar du på snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Förhandsgranska lagringsfilen

Högerklicka på Skriptredigeraren och välj **ADL: förhandsgranskningsfil**.

Välj din **ADLA konto**. Ange en filsökväg (till exempel /output/SearchLog.txt) för Azure storage ->. Result ->: filen öppnas i VSCode.

   ![Data Lake-verktyg för Visual Studio Code förhandsgranska filen resultat](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Det är ett annat sätt att förhandsgranskningsfilen via snabbmenyn på den fullständiga sökvägen eller den relativa sökvägen i Skriptredigeraren. 

### <a name="upload-file-or-folder"></a>Ladda upp filen eller mappen

1. Högerklicka på Skriptredigeraren och välj **överför filen** eller **överför mappen**.

2. Välj en eller flera filer om du väljer överför filen eller välj hela mappen om du väljer Skicka mapp sedan klickar du på **överför**. -> Välj lagringsmappen i listan, eller klicka på **ange sökvägen** eller **Bläddra från roten** (använder ange en sökväg som exempel). Välj -> din **ADLA konto**. Analysera -> eller ange sökvägen till lagring (till exempel: / utdata /). -> Klicka **Välj aktuella mappen** att ange din målplatsen.

   ![Data Lake-verktyg för Visual Studio Code överför status](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Ett annat sätt att ladda upp filer till lagring är via snabbmenyn på den fullständiga sökvägen eller den relativa sökvägen i Skriptredigeraren.

På samma gång, kan du övervaka den [Överföringsstatus](#check-storage-tasks-status).


### <a name="download-file"></a>Hämta fil 
Du kan hämta filer genom att ange kommandona **ADL: hämta filen** eller **ADL: hämta filen (Avancerat)**.

**Hämta filer även om ADL: hämta filen (Avancerat)**
1. Högerklicka på Skriptredigeraren och välj sedan **hämta filen (Avancerat)**.
2. VS-kod visar en JSON-fil. Du kan ange sökvägar och hämta flera filer samtidigt. Instruktioner visas i den **utdata** fönster. Om du vill fortsätta att hämta filen, spara (Ctrl + S) JSON-filen.

    ![Data Lake-verktyg för Visual Studio-koden och hämta filer med config](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Resultat: Den **utdata** Överföringsstatusen filen visas i fönstret.

    ![Data Lake-verktyg för Visual Studio-koden och hämta flera filer resultat](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

På samma gång, kan du övervaka den [hämtar status](#check-storage-tasks-status).

**Hämta filer även om ADL: hämta filen**

1. Högerklicka på Skriptredigeraren, Välj **hämta filen**, och välj sedan målmappen från den **Välj mapp** dialogrutan.

2. Välj mappen i listan eller klicka på **ange sökvägen** eller **Bläddra från roten** (använder ange en sökväg som exempel). Välj -> din **ADLA konto**. Analysera -> eller ange sökvägen till lagring (till exempel: / utdata) -> Välj en fil att ladda ned.

   ![Hämta status för data Lake-verktyg för Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Ett annat sätt för att hämta lagringsfilerna är via snabbmenyn på den fullständiga sökvägen eller den relativa sökvägen i Skriptredigeraren.

På samma gång, kan du övervaka den [hämtar status](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrollera status för lagringsuppgifter
Statusen visas längst ned i statusfältet när du är klar överföra och ladda ned.
1. Klicka på länkarna nedan statusfältet och sedan hämtning och ladda upp status visas i **utdata** panelen.

   ![Data Lake-verktyg för Visual Studio Code Kontrollera lagring status](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>VSCode Explorer integrering med Azure Data Lake

**Azure Integration** 

- Innan inloggningen till Azure kan du alltid Expandera **DATALAKE EXPLORER**, klicka på **logga in på Azur** att logga in på Azure. Efter inloggningen, visas alla prenumerationer i ditt Azure-konto visas i den vänstra panelen i den **DATALAKE EXPLORER**. 

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**ADLA metadatanavigering** 

- Expandera din Azure-prenumeration kan du navigera U-SQL-databasen, visa den **scheman**, **autentiseringsuppgifter**, **sammansättningar**, **tabell**, **Index**och så vidare under noden U-SQL-databaser.

**Hantering av ADLA Metadata entitet**

- Expandera **U-SQL-databaser**, kan du skapa en ny databas, schemat, tabell, tabelltyper, index, statistik genom att högerklicka på den **skript för att skapa** snabbmenyn under noden motsvarande. På sidan öppna skriptet skript efter dina behov och sedan skicka jobbet genom att högerklicka på snabbmenyn **ADL: skicka jobbet**. När du slutför skapandet av det, klickar du på snabbmenyn **uppdatera** att visa den nya objekt skapas. Du kan också ta bort objektet genom att högerklicka på snabbmenyn **ta bort**.

   ![DataLake skapas nya objekt-menyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![DataLake explorer skapar nytt objektskript](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**ADLA regasm.exe för registrering**

 - Du kan **registrera sammansättningen** till motsvarande databasen genom att högerklicka på den **sammansättningar** nod.

    ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**ADLS-integrering** 

 - Gå till **Lagringskonto**, kan du **Preview**, **hämta**, **ta bort**, **kopiera relativa sökväg**, **Kopiera fullständig sökväg** av snabbmenyn på noden fil. Du kan **uppdatera**, **överför**, **överför mappen**, **ta bort** genom att högerklicka på snabbmenyn på noden mapp.

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Öppna Lagringsutforskaren för ADL i portalen
1. Välj Ctrl + Skift + P för att öppna paletten kommando.
2. Ange **öppna Web Azure Lagringsutforskaren** eller högerklicka på en relativ sökväg eller den fullständiga sökvägen i Skriptredigeraren och välj **öppna Web Azure Lagringsutforskaren**.
3. Välj ett Data Lake Analytics-konto.

Data Lake-verktyg öppnar Azure storage-sökvägen i Azure-portalen. Du kan hitta sökvägen och förhandsgranska filen från webben.

## <a name="local-run-and-local-debug-for-windows-users"></a>Lokal körning och lokala debug för Windows användare
Lokal U-SQL kör testar dina lokala data och verifierar ditt skript lokalt innan koden publiceras till Data Lake Analytics. Lokala debug-funktionen kan du utföra följande åtgärder innan koden skickas till Data Lake Analytics: 
- Felsöka ditt C# bakomliggande kod. 
- Gå igenom koden. 
- Validera skriptet lokalt.

Anvisningar för lokal körning och lokala debug finns [lokal U-SQL kör och lokala debug med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Ytterligare funktioner

Data Lake-verktyg för VS-koden har stöd för följande funktioner:

-   IntelliSense automatisk komplettering: förslag visas i popup-fönster runt objekt, till exempel nyckelord, metoder och variabler. Olika ikoner representerar olika typer av objekt:

    - Scala-datatyp
    - Komplex datatyp
    - Inbyggda UDT-typer
    - .NET-samling och klasser
    - C#-uttryck
    - Inbyggda C# UDF: er och UDO UDAAGs 
    - U-SQL-funktioner
    - U-SQL-fönsterfunktion
 
    ![Data Lake-verktyg för Visual Studio Code IntelliSense objekttyper](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   IntelliSense automatisk komplettering på Data Lake Analytics metadata: Data Lake-verktyg hämtar Data Lake Analytics metadatainformationen lokalt. Funktionen IntelliSense fyller automatiskt objekt, inklusive databasen, schemat, tabell, visa, tabellvärdesfunktion, procedurer och C#-sammansättningar, från Data Lake Analytics-metadata.
 
    ![Data Lake-verktyg för Visual Studio Code IntelliSense metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense fel markör: Data Lake-verktyg understryker redigera felen för U-SQL och C#. 
-   Visar syntax: Data Lake-verktyg använder olika färger för att skilja objekt, till exempel variabler, nyckelord, datatyp och funktioner. 

    ![Data Lake-verktyg för Visual Studio Code syntax visar](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Nästa steg
- [Utveckla U-SQL med Python, R och CSharp för Azure Data Lake Analytics i VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokal U-SQL kör och lokala debug med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudier: Kom igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudier: Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-assemblies.md)




