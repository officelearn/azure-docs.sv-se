<properties
   pageTitle="Azure Data Catalog – Kom igång med Data Catalog | Microsoft Azure"
   description="Självstudiekurs med en presentation av scenarier och funktioner i Azure Data Catalog."
   documentationCenter=""
   services="data-catalog"
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="05/06/2016"
   ms.author="maroche"/>

# Kom igång med Azure Data Catalog

Den här artikeln är avsedd som en översikt av scenarier och funktioner i **Azure Data Catalog**. När du har registrerat dig för tjänsten följer du dessa steg för att skapa en datakatalog samt registrera, lägga till anteckningar i och identifiera datakällor.

## Förutsättningar för självstudiekursen

Innan du påbörjar den här självstudiekursen måste du ha följande:

- **En Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- **Azure Active Directory** – Azure Data Catalog använder [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för identitets- och åtkomsthantering.
- **Datakällor** – Azure Data Catalog har funktioner som identifierar datakällor. I den här självstudiekursen används exempeldatabasen för Adventure Works, men du kan använda valfri datakälla som stöds om du föredrar att arbeta med data som är bekanta och relevanta för din roll. En lista över datakällor som stöds finns i [Datakällor som stöds](data-catalog-dsr.md).

> [AZURE.NOTE] Mer information om Azure-prenumerationer och Azure Active Directory finns i  [Förutsättningar för Azure Data Catalog](data-catalog-prerequisites.md).

Vi börjar med att installera exempeldatabasen för Adventure Works.

## Övning 1: Installera Adventure Works-exempeldatabasen

I den här övningen installerar du Adventure Works-exemplet för SQL Server Database Engine som används i övningarna som följer.

### Installera Adventure Works 2014 OLTP-databasen

Adventure Works-databasen stöder standardscenarier för webbaserad transaktionsbearbetning för en fiktiv cykeltillverkare (Adventure Works Cycles) som omfattar produkter, försäljning och inköp. I den här självstudiekursen registrerar du information om produkter i **Azure Data Catalog**.

Så här installerar du Adventure Works-exempeldatabasen.

Installera Adventure Works-exempeldatabasen genom att återställa en AdventureWorks2014-säkerhetskopia som finns i [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) på CodePlex. Ett sätt att återställa databasen är att köra ett T-SQL-skript i SQL Server Management Studio.

**Installera Adventure Works-exempeldatabasen med ett T-SQL-skript**

1.  Skapa en mapp med namnet C:\DataCatalog_GetStarted. Om du använder ett annat mappnamn ska du vara noga med att ändra sökvägen i T-SQL-skriptet nedan.
2.  Ladda ned [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661).
3.  Extrahera Adventure Works 2014 Full Database Backup.zip till C:\DataCatalog_GetStarted. Skriptet nedan förutsätter att filen med säkerhetskopian finns på C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak.
4.  Från **SQL Server Management Studio**, i verktygsfältet **Standard**, klickar du på **Ny fråga**.
5.  Kör följande T-SQL-kod i frågefönstret.

**Installera databasen Adventure Works 2014 genom att köra detta skript**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
        -- AdventureWorks2014.bak file location
        FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

        -- AdventureWorks2014.mdf database location
        WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

        -- AdventureWorks2014.ldf log location
        MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Som ett alternativ till att köra ett T-SQL-skript kan du återställa databasen med SQL Server Management Studio. Mer information finns i [Återställa en säkerhetskopia av en databas (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

I denna övning har du installerat Adventure Works-exempeldatabasen som används i övriga övningar. I nästa övning kommer du att registrera datakällor i **Azure Data Catalog** från tabeller i Adventure Works-exempeldatabasen.

## Övning 2: Registrera datakällor

I den här övningen använder du registreringsverktyget i **Azure Data Catalog** för att registrera datakällor för Adventure Works i katalogen. Registrering är processen där du extraherar viktiga strukturella metadata – som namn, typer och platser – från datakällan och dess tillgångar och kopierar dem till katalogen. Datakällorna med innehåll blir kvar där de är, men katalogen använder deras metadata för att göra det lättare att identifiera dem och förstå hur de kan användas.

### Så här registrerar du en datakälla

1.  Öppna https://azure.microsoft.com/services/data-catalog och klicka på **Kom igång**.
2.  Logga in på **Azure Data Catalog**-portalen och klicka på **Publicera data**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.  Klicka på **Starta program**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. På sidan **Välkommen** klickar du på **Logga in** och anger dina inloggningsuppgifter.
5. På sidan **Microsoft Azure Data Catalog** dubbelklickar du på **SQL Server**, eller också klickar du på **SQL Server** och **Nästa**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.  Ange anslutningsegenskaper för SQL Server för AdventureWorks2014 (se exemplet nedan) och klicka på **ANSLUT**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.  På nästa sida registrerar du metadata för datakällan. I det här exemplet registrerar du objekt från **Produktion/Produkt** i AdventureWorks namnområde Produktion. Så här gör du:

    a. I trädet **Serverhierarki** klickar du på **Produktion**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Ctrl-klicka på Produkt, Produktkategori, Produktbeskrivning och Produktfoto.

    c. Klicka på pilen för att flytta markerade objekt (**>**). Då flyttas alla markerade Produkt-objekt till listan **Objekt som ska registreras**.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. Ange beskrivning, foto i **Lägg till taggar**. Då läggs söktaggar till i datatillgångarna. Taggar är ett bra sätt att hjälpa användarna att hitta en registrerad datakälla.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e.  **Valfritt**: Du kan **lägga till en förhandsgranskning** och **en expert på datakällan**.

    f.  Klicka på **REGISTRERA**. De objekt du har markerat registreras i **Azure Data Catalog**. I den här övningen registreras de markerade objekten från Adventure Works.

    g.  Klicka på **Visa portal** om du vill visa registrerade datakällobjekt. I **Azure Data Catalog**-portalen kan du visa datakällobjekt i **paneler** eller i en **lista**.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

I den här övningen registrerade du objekt från exempeldatabasen för Adventure Works så att de enkelt kan identifieras av användare i organisationen.
I nästa övning kommer du att identifiera registrerade datatillgångar.

## Övning 3: Identifiera registrerade datatillgångar

I den här övningen får du identifiera registrerade datatillgångar och visa deras metadata i **Azure Data Catalog**. Du kan identifiera datatillgångar i **Azure Data Catalog** på flera olika sätt, till exempel med enkel nyckelordssökning, interaktiva filter och avancerad söksyntax för avancerade användare.

### Så här identifierar du registrerade datatillgångar

**Azure Data Catalog** har en enkel men kraftfull söksyntax där du enkelt kan skapa frågor som returnerar data användarna behöver. Mer information om sökning i **Azure Data Catalog** finns i [Söksyntax för Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

**Azure Data Catalog** har följande sökalternativ:

- Nyckelordssökning
- Filter
- Avancerad sökning

Du kan även begränsa vilka datatillgångar som ska visas. **Azure Data Catalog** har följande visningsalternativ:

- Visa egenskaper
- Visa kolumner
- Visa förhandsgranskning

I det här exemplet används nyckelordssökning. Det finns flera frågetekniker i **Azure Data Catalog**. I det här exemplet används en **grupperad** sökfråga.

**Frågetekniker**

|Teknik|Användning|Exempel
|---|---|---
|Egenskapsomfång|Returnerar endast datakällor där söktermen ger träffar i den angivna egenskapen|namn:produkt
|Logiska operatorer|Utöka eller begränsa sökningen med booleska åtgärder enligt beskrivningen i avsnittet Booleska operatorer på den här sidan|finans INTE företag
|Gruppera med parenteser|Gruppera delar av frågan med parenteser för logisk isolering, särskilt i kombination med booleska operatorer|namn:produkt OCH (taggar:illustration ELLER taggar:foto)
|Jämförelseoperatorer|Använda andra jämförelser än lika med för egenskaper som innehåller datum och numeriska datatyper|skapades:> 2014-05-11

I det här exemplet gör du en **grupperad** sökning efter datatillgångar där namnet är lika med Produkt och taggarna är lika med illustration eller lika med foto.

1. Öppna https://azure.microsoft.com/services/data-catalog, klicka på **Kom igång** och logga in på **Azure Data Catalog**-portalen.
2. I rutan **Sök i datakatalog** anger du en **grupperad** sökning: (**taggar:beskrivning ELLER taggar:foto**).
3. Klicka på sökikonen eller tryck på Retur. **Azure Data Catalog** visar datatillgångar för sökfrågan.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

I den här övningen har du identifierat och visat datatillgångar för Adventure Works som registrerats i katalogen i **Azure Data Catalog**-portalen.

<a name="annotating"/>
## Övning 4: Kommentera registrerade datakällor

I den här övningen kommenterar du datatillgångar som du tidigare har registrerat i katalogen i **Azure Data Catalog**-portalen. Dina kommentarer kompletterar och förbättrar strukturella metadata som extraherats från datakällan under registreringen och gör datatillgångarna mycket lättare att identifiera och förstå. Eftersom alla **Data Catalog**-användare kan lägga till egna anteckningar är det enkelt för alla användare att dela med sig av sina perspektiv.

### Så här lägger du till kommentarer i datatillgångar

1. Öppna https://azure.microsoft.com/services/data-catalog, klicka på **Kom igång** och logga in på **Azure Data Catalog**-portalen.
2. Klicka på **Upptäck**.
3. Välj en eller flera **datatillgångar**. I det här exemplet markerar du **Produktfoto** och anger ”Produktfoton för marknadsföringsmaterial”.
4. Ange en **beskrivning** som hjälper andra att identifiera och förstå varför och hur du använder den valda datatillgången. Ange till exempel ”Produktbilder”. Du kan också lägga till fler taggar och visningskolumner.
5. Nu kan du testa att söka och filtrera för att identifiera datatillgångar med de beskrivande metadata som du har lagt till i katalogen.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

I den här övningen lade du till beskrivande information till registrerade datatillgångar så att katalogens användare kan identifiera datakällor med hjälp av termer de förstår.

> [AZURE.NOTE] Standard Edition av Data Catalog innehåller en företagsordlista som hjälper katalogadministratörer att fastställa en central klassificering för företaget. Katalogens användare kan sedan lägga till anteckningar i datatillgångar med termer från ordlistan. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md)  

## Övning 5: Gemensamt skapade metadata

I den här övningen arbetar du tillsammans med en annan användare med att lägga till metadata i katalogens datatillgångar. I **Azure Data Catalog** kan alla användare lägga till kommentarer, vilket innebär att alla kan lägga till taggar, beskrivningar och andra metadata så att alla synpunkter om en datatillgång och hur den kan användas kan delas med andra användare.

> [AZURE.NOTE] Det gör inget om du inte har en annan användare att arbeta med under självstudiekursen! Alla användare som har åtkomst till datakatalogen kan lägga till egna perspektiv när de vill. Tack vare metoden med användargenererade metadata kan katalogens innehåll och informationen i katalogens metadata växa med tiden.

### Så här skapar du användargenererade metadata för datatillgångar

Be en kollega upprepa övningen [Lägga till anteckningar till registrerade datakällor](#annotating) ovan. När dina kollegor lägger till beskrivningar i en datatillgång, till exempel produktfoto, ser du flera kommentarer.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

I den här övningen utforskade du användargenererade metadata i **Azure Data Catalog**, där alla kataloganvändare kan lägga till kommentarer i datatillgångarna de identifierar.

## Övning 6: Ansluta till datakällor

I den här övningen ansluter du till en datakälla med Microsoft Excel via **Azure Data Catalog**-portalen.

> [AZURE.NOTE] Det är viktigt att komma ihåg att **Azure Data Catalog** inte ger användarna åtkomst till den faktiska datakällan – det blir bara enklare för användarna att identifiera datakällor och förstå hur de kan användas. När användarna ansluter till en datakälla använder klientprogrammet deras autentiseringsuppgifter för Windows eller också uppmanas de att ange andra autentiseringsuppgifter efter behov. Om användaren inte tidigare har beviljats åtkomst till datakällan kan användaren inte ansluta förrän åtkomst har beviljats.

### Så här ansluter du till en datakälla från Excel

1. Öppna https://azure.microsoft.com/services/data-catalog, klicka på **Kom igång** och logga in på **Azure Data Catalog**-portalen.
2. Klicka på **Upptäck**.
3. Välj en datatillgång. I det här exemplet väljer du Produktkategori.
4. Välj **Öppna i** > **Excel**.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. I fönstret **Säkerhetsmeddelande om Microsoft Excel** klickar du på **Aktivera**.
6. Öppna filen **ProductCategory.odc**.
7. Datakällan öppnas i Excel.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

I den här övningen anslöt du till datakällor som identifierats med hjälp av **Azure Data Catalog**. I **Azure Data Catalog**-portalen kan användarna ansluta direkt med de klientprogram som integrerats i menyn **Öppna i …** så att användarna kan ansluta med valfritt program via anslutningsplatsinformationen i tillgångens metadata.

## Övning 7: Ta bort metadata från datakällan

I den här övningen tar du bort förhandsgranskningsdata från registrerade datatillgångar samt datatillgångar från katalogen i **Azure Data Catalog**-portalen.

> [AZURE.NOTE] Som standard tillåter katalogen att alla användare registrerar valfri datakälla och att alla användare tar bort alla datatillgångar som har registrerats. Hanteringsfunktionerna i **Standard Edition av Azure Data Catalog** erbjuder ytterligare alternativ för ägarskap av tillgångar samt begränsning av vem som kan identifiera tillgångar och ta bort tillgångar.

I **Azure Data Catalog** kan du ta bort enskilda tillgångar eller flera tillgångar.

### Så här tar du bort flera datatillgångar

1. Öppna https://azure.microsoft.com/services/data-catalog, klicka på **Kom igång** och logga in på **Azure Data Catalog**-portalen.
2. Klicka på **Upptäck**.
3. Markera en eller flera datatillgångar.
4. Klicka på **Ta bort**.

I den här övningen tog du bort registrerade datatillgångar från katalogen.

## Övning 8: Hantera registrerade datakällor

I den här övningen övertar du ägarskapet för datatillgångar samt styr vad användarna kan identifiera och hur användarna kan hantera resurserna via hanteringsfunktionerna i **Azure Data Catalog**.

> [AZURE.NOTE] De hanteringsfunktioner som beskrivs i den här övningen är bara tillgängliga i **Standard Edition av Azure Data Catalog** och inte i **Free Edition**.
I **Azure Data Catalog**kan du överta ägarskapet för datatillgångar, lägga till delägare till datatillgångar och ange datatillgångars synlighet.

### Så här övertar du ägarskapet för datatillgångar och begränsar synligheten

1. Öppna https://azure.microsoft.com/services/data-catalog, klicka på **Kom igång** och logga in på **Azure Data Catalog**-portalen.
2. Klicka på **Upptäck**.
3. Markera en eller flera datatillgångar.
4. I panelen**Egenskaper**, avsnittet **Hantering**, klickar du på **Överta ägarskap**.
5. Om du vill begränsa synligheten klickar du på **Ägare och dessa användare**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

I den här övningen du utforskat hanteringsfunktionerna i **Azure Data Catalog** samt begränsad synlighet för valda datatillgångar.

## Sammanfattning

I den här handledningen du arbetat med grundläggande funktioner för **Azure Data Catalog**, till exempel registrering, kommentarer, identifiering och hantering av företagsdatakällor. Nu har du slutfört självstudiekursen, så det är dags att komma igång. Du kan börja i dag genom att registrera de datakällor som du och ditt team använder och bjuda in kollegor att använda katalogen.



<!--HONumber=Jun16_HO2-->


