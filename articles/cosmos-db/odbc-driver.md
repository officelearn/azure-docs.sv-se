---
title: Ansluta till Azure Cosmos DB med BI-verktyg för analys
description: Lär dig hur du använder Azure Cosmos DB ODBC-drivrutinen för att skapa tabeller och vyer så att normaliserade data kan ses i analysprogramvara i BI och data.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: f51ce90d9349d345bb1e77ca5e254315c20c1b1d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034480"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ansluta till Azure Cosmos DB med BI analysverktyg med ODBC-drivrutin

Azure Cosmos DB ODBC-drivrutinen kan du ansluta till Azure Cosmos DB med hjälp av BI analytics-verktyg som SQL Server Integration Services och Power BI Desktop Tableau så att du kan analysera och skapa visualiseringar av dina Azure Cosmos DB-data i dessa lösningar.

Azure Cosmos DB ODBC-drivrutinen är kompatibel med ODBC 3.8 och stöder ANSI SQL-92-syntax. Drivrutinen har omfattande funktioner som hjälper dig renormalisera data i Azure Cosmos DB. Med drivrutinen kan du representera data i Azure Cosmos DB som tabeller och vyer. Du kan utföra SQL-åtgärder mot tabeller och vyer, t.ex. gruppera efter frågor, infogningar, uppdateringar och borttagningar.

> [!NOTE]
> Ansluta till Azure Cosmos DB med ODBC-drivrutin stöds för närvarande för Azure Cosmos DB SQL API-konton.

## <a name="why-do-i-need-to-normalize-my-data"></a>Varför behöver jag normalisera Mina data?
Azure Cosmos DB är en schemalös databas, som möjliggör snabb programutveckling och möjligheten att iterera datamodeller utan att begränsas till ett strikt schema. En enda Azure Cosmos DB-databas kan innehålla JSON-dokument med olika strukturer. Detta är perfekt för snabb programutveckling, men om du vill att analysera och skapa rapporter om dina data med dataanalys och BI-verktyg kan data ska ofta förenklas och följer ett visst schema.

Det här är här ODBC-drivrutinen kommer in. Med hjälp av ODBC-drivrutinen kan renormera du nu data i Azure Cosmos DB i tabeller och vyer som passar din dataanalys och rapportering. Renormalized scheman har ingen inverkan på underliggande data och begränsa inte utvecklare för att följa dem. I stället kan du utnyttja ODBC-kompatibel verktyg för att komma åt data. Så kommer nu Azure Cosmos DB-databas inte bara en favorit för din utvecklingsgrupp, men din dataanalytiker kommer att älska det för.

Låt oss börja med ODBC-drivrutinen.

## <a id="install"></a>Steg 1: Installera Azure Cosmos DB ODBC-drivrutinen

1. Hämta drivrutinerna för din miljö:

    | Installationsprogrammet | Operativsystem som stöds| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) för 64-bitars Windows| 64-bitars versioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/cosmos-odbc-32x64) för 32-bitars på Windows 64-bitars| 64-bitars versioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 och Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) för 32-bitars Windows|32-bitars versioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows XP och Windows Vista.|

    Kör msi-filen lokalt, som startar den **installationsguiden för Microsoft Azure Cosmos DB ODBC-drivrutinen**. 

1. Slutför installationsguiden med att använda standardinställningarna är indata för att installera ODBC-drivrutinen.

1. Öppna den **administratör för ODBC-datakällor** app på datorn. Du kan göra detta genom att skriva **ODBC-datakällor** i Windows-sökrutan. 
    Du kan bekräfta drivrutinen har installerats genom att klicka på den **drivrutiner** fliken och försäkra dig om **ODBC-drivrutinen för Microsoft Azure Cosmos DB** visas.

    ![Azure Cosmos DB ODBC-datakälla](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Steg 2: Ansluta till din Azure Cosmos DB-databas

1. Efter [installerar Azure Cosmos DB ODBC-drivrutinen](#install)i den **ODBC Data Source Administrator** fönstret klickar du på **Lägg till**. Du kan skapa en användare eller System-DSN. I det här exemplet skapar du en användar-DSN.

1. I den **Skapa ny datakälla** väljer **ODBC-drivrutinen för Microsoft Azure Cosmos DB**, och klicka sedan på **Slutför**.

1. I den **SDN installationsprogrammet för Azure Cosmos DB ODBC-drivrutinen** Fyll i följande information: 

    ![Azure Cosmos DB ODBC-drivrutinen DSN konfigurationsfönstret](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Namn på datakälla**: Egna eget namn för ODBC DSN. Det här namnet är unikt för ditt Azure Cosmos DB-konto, så ger den namnet på lämpligt sätt om du har flera konton.
    - **Beskrivning**: En kort beskrivning av datakällan.
    - **Värden**: URI för Azure Cosmos DB-kontot. Du kan hämta det från sidan nycklar för Azure Cosmos DB i Azure-portalen, enligt följande skärmbild. 
    - **Få åtkomst till nyckeln**: Den primära eller sekundära, skrivskyddad eller skrivskyddade nyckeln från sidan nycklar för Azure Cosmos DB i Azure portal enligt följande skärmbild. Vi rekommenderar att du använder den skrivskyddade nyckeln om DSN används för bearbetning av skrivskyddade och rapportering.
    ![Sidan för Azure Cosmos DB-nycklar](./media/odbc-driver/odbc-driver-keys.png)
    - **Kryptera åtkomstnyckel för**: Välj det bästa valet baserat på användare av den här datorn. 
    
1. Klicka på den **Test** knappen för att kontrollera att du kan ansluta till ditt Azure Cosmos DB-konto. 

1. Klicka på **avancerade alternativ** och ange följande värden:
    - **Fråga konsekvens**: Välj den [konsekvensnivå](consistency-levels.md) för din verksamhet. Standardvärdet är Session.
    - **Antal återförsök**: Ange hur många gånger att försöka utföra en åtgärd om den första begäran inte slutförs på grund av begränsningar i tjänsten.
    - **Schemafilen**: Du har ett antal alternativ här.
        - Som standard, lämna den här posten skick (tom), genomsöker drivrutinen den första sidan av data för alla samlingar att fastställa schemat för varje samling. Detta kallas mappning av samlingen. Utan en schemafil som definierats, drivrutinen måste utföra genomsökningen för varje drivrutin-session och kan resultera i en högre starttiden för ett program med hjälp av DSN. Vi rekommenderar att du alltid associera en schemafil för en Datakälla.
        - Om du redan har en schemafil (eventuellt en som du skapat med hjälp av den [Schemaredigerare](#schema-editor)), kan du klicka på **Bläddra**, navigera till filen, klicka på **spara**, och klicka sedan på **OK**.
        - Om du vill skapa ett nytt schema, klickar du på **OK**, och klicka sedan på **Schemaredigerare** i huvudfönstret. Gå sedan vidare till den [Schemaredigerare](#schema-editor) information. När du har skapat den nya schemafilen, Kom ihåg att gå tillbaka till den **avancerade alternativ** fönster för att inkludera den nyligen skapade schemafilen.

1. När du slutför och stänger den **Azure Cosmos DB ODBC-drivrutinen DSN för** kommer den nya användaren DSN har lagts till i fliken användar-DSN.

    ![Nya Azure Cosmos DB ODBC DSN på fliken användar-DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Steg 3: Skapa en schemadefinitionen med metoden samling mappning

Det finns två typer av sampling metoder som du kan använda: **samling mappning** eller **tabell avgränsare**. En session för sampling kan använda båda metoderna för sampling, men varje samling kan bara använda en specifik samplingsmetoden. Stegen nedan skapar ett schema för data i en eller flera samlingar med metoden samling mappning. Den här samplingsmetoden hämtar-data på sidan i en samling att fastställa strukturen för data. Den flyttar kryptografi om en samling till en tabell på ODBC-sida. Den här samplingsmetoden är effektivt och snabbt när data i en samling är homogen. Om en samling innehåller heterogena typ av data, vi rekommenderar att du använder den [tabell avgränsare mappning metoden](#table-mapping) eftersom det ger dig en mer stabil samplingsmetoden för att fastställa datastrukturer i samlingen. 

1. När du har slutfört steg 1-4 i [Anslut till din Azure Cosmos DB-databas](#connect), klickar du på **Schemaredigerare** i den **Azure Cosmos DB ODBC-drivrutinen DSN för** fönster.

    ![Schemat redigerarens knapp i fönstret DSN inställningar för Azure Cosmos DB ODBC-drivrutin](./media/odbc-driver/odbc-driver-schema-editor.png)
1. I den **Schemaredigerare** fönstret klickar du på **Skapa ny**.
    Den **generera schemat** i fönstret visas alla samlingar i Azure Cosmos DB-kontot. 

1. Välj en eller flera samlingar till exempel och klicka sedan på **exempel**. 

1. I den **designvyn** fliken, database, schema och tabellen representeras. I tabellvyn visar genomsökningen uppsättning egenskaper som är associerade med kolumnnamn (SQL-namn, namn på datakälla, osv.).
    För varje kolumn, du kan ändra kolumnnamn för SQL, SQL-typ, SQL-längd (om tillämpligt), skalning (om tillämpligt), Precision (om tillämpligt) och kan ha värdet null.
    - Du kan ange **Dölj kolumn** till **SANT** om du vill utesluta kolumnen från frågeresultat. Kolumner markerats Dölj kolumn = true returneras inte för val av och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB-system som krävs för egenskaper som börjar med ”_”.
    - Den **id** kolumnen är det enda fältet inte kan döljas eftersom den används som primärnyckel i normaliserade schemat. 

1. När du har definierat schemat klickar du på **filen** | **spara**, gå till katalogen för att spara schemat och klicka sedan på **spara**.

    Om du vill i framtiden att använda det här schemat med en DNS, öppna fönstret DSN installationsprogrammet för Azure Cosmos DB ODBC-drivrutinen (via den ODBC-datakälla), klickar du på Avancerade alternativ och navigera till det sparade schemat i rutan schemafilen. Spara en schemafil till en befintlig DSN ändrar DSN-anslutningen till scope till data och struktur som definieras av schemat.

## <a id="table-mapping"></a>Steg 4: Skapa en schemadefinitionen med tabell-avgränsare mappning metod

Det finns två typer av sampling metoder som du kan använda: **samling mappning** eller **tabell avgränsare**. En session för sampling kan använda båda metoderna för sampling, men varje samling kan bara använda en specifik samplingsmetoden. 

Följande steg skapar ett schema för data i en eller flera samlingar med hjälp av den **tabell avgränsare** mappning av metoden. Vi rekommenderar att du använder den här samplingsmetoden när dina samlingar innehåller olika typer av data. Du kan använda den här metoden för att definiera omfattningen av sampling till en uppsättning attribut och dess motsvarande värden. Om ett dokument innehåller en ”Type”-egenskap, till exempel kan du begränsa sampling till värden i den här egenskapen. Slutresultatet av samplingen är en uppsättning tabeller för var och en av värdena för typ som du har angett. Skriv exempelvis = bil, skapas en bil tabell vid typ = plan resulterar i en plan-tabell.

1. När du har slutfört steg 1-4 i [Anslut till din Azure Cosmos DB-databas](#connect), klickar du på **Schemaredigerare** i fönstret DSN installationsprogrammet för Azure Cosmos DB ODBC-drivrutinen.

1. I den **Schemaredigerare** fönstret klickar du på **Skapa ny**.
    Den **generera schemat** i fönstret visas alla samlingar i Azure Cosmos DB-kontot. 

1. Välj en samling på den **exempelvy** fliken den **mappning Definition** kolumnen för insamling, klickar du på **redigera**. I den **mappning Definition** väljer **tabell avgränsare** metod. Gör något av följande:

    a. I den **attribut** skriver du namnet på en egenskap för avgränsare. Det här är en egenskap som du vill begränsa sampling till exempelvis stad och tryck på RETUR. 

    b. Om du bara vill att omfatta sampling till vissa värden för attributet som du angav ovan, Välj attributet i rutan, ange ett värde i den **värdet** box (t.ex. Seattle) och tryck på Ange. Du kan fortsätta att lägga till flera värden för attributen. Se bara till att rätt attributet aktiveras när du har angett värden.

    Exempel: Om du inkluderar en **attribut** värdet för stad, och du vill begränsa din tabell så att endast rader med ett värde för stad i New York och Dubai, anger du stad i rutan attribut, och New York och sedan Dubai i **Värden** box.

1. Klicka på **OK**. 

1. När du har slutfört mappningsdefinitionerna för samlingarna du vill prova i den **Schemaredigerare** fönstret klickar du på **exempel**.
     För varje kolumn, du kan ändra kolumnnamn för SQL, SQL-typ, SQL-längd (om tillämpligt), skalning (om tillämpligt), Precision (om tillämpligt) och kan ha värdet null.
    - Du kan ange **Dölj kolumn** till **SANT** om du vill utesluta kolumnen från frågeresultat. Kolumner markerats Dölj kolumn = true returneras inte för val av och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB systemkrav egenskaper från och med `_`.
    - Den **id** kolumnen är det enda fältet inte kan döljas eftersom den används som primärnyckel i normaliserade schemat. 

1. När du har definierat schemat klickar du på **filen** | **spara**, gå till katalogen för att spara schemat och klicka sedan på **spara**.

1. I den **Azure Cosmos DB ODBC-drivrutinen DSN för** fönstret klickar du på **avancerade alternativ**. I den **schemafilen** , navigera till den sparade schemafilen och klickar på **OK**. Klicka på **OK** igen för att spara DSN. Detta sparar schemat som du skapade till DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Valfritt) Skapa länkad server-anslutning

Du kan fråga Azure Cosmos DB från SQL Server Management Studio (SSMS) genom att konfigurera en länkad server-anslutning.

1. Skapa en systemdatakälla enligt beskrivningen i [steg 2](#connect)med namnet till exempel `SDS Name`.

1. [Installera SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och ansluta till servern. 

1. Skapa en länkad server-objekt i frågeredigeraren SSMS `DEMOCOSMOS` för datakällan med följande kommandon. Ersätt `DEMOCOSMOS` med namnet för den länkade servern, och `SDS Name` med namnet på datakällan system.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Uppdatera listan med länkade servrar om du vill se det nya länkade servernamnet.

![Länkad Server i SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Fråga länkad databas

Ange en fråga med SSMS för att fråga den länkade databasen. I det här exemplet markerar frågan i tabellen i den samling som heter `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Kör frågan. Resultatet bör se ut ungefär så här:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Den länkade Cosmos DB-servern har inte stöd för namngivning av fyra delar. Ett fel returneras liknar följande meddelande:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Valfritt) Skapa vyer
Du kan definiera och skapa vyer som en del av sampling. Dessa vyer är likvärdiga med SQL-vyer. De är skrivskyddade och inom omfånget val och projektioner av Azure Cosmos DB SQL-fråga som definierats. 

Du skapar en vy för dina data i den **Schemaredigerare** fönstret i den **vydefinitioner** kolumnen, klickar du på **Lägg till** på raden i samlingen som exempel. 
    ![Skapa en vy över data](./media/odbc-driver/odbc-driver-create-view.png)


I den **vydefinitioner** fönstret gör du följande:

1. Klicka på **New**, ange ett namn för vyn, till exempel EmployeesfromSeattleView och klicka sedan på **OK**.

1. I den **Redigera vy** fönstret, ange en Azure Cosmos DB-fråga. Det här måste vara en [Azure Cosmos DB SQL-fråga](how-to-sql-query.md), till exempel `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, och klicka sedan på **OK**.

    ![Lägg till fråga när du skapar en vy](./media/odbc-driver/odbc-driver-create-view-2.png)


Du kan skapa flera vyer som du vill. När du är klar Definiera vyer kan du kan sedan ta prov på data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Steg 5: Visa dina data i BI-verktyg som Power BI Desktop

Du kan använda din nya DSN för att ansluta till Azure Cosmos DB med verktyg från någon ODBC-kompatibel – det här steget bara visar hur du ansluter till Power BI Desktop och skapa en Power BI-visualisering.

1. Öppna Power BI Desktop.

1. Klicka på **hämta Data**.

    ![Hämta Data i Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. I den **hämta Data** fönstret klickar du på **andra** | **ODBC** | **Connect**.

    ![Välj ODBC-datakälla i Power BI hämta Data](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. I den **från ODBC** väljer du datakällan servernamnet du skapat och klicka sedan på **OK**. Du kan lämna den **avancerade alternativ** poster som är tom.

    ![Välj namn på datakällan (DSN) i Power BI hämta Data](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. I den **åtkomst till en datakälla med hjälp av en ODBC-drivrutin** väljer **standard eller anpassad** och klicka sedan på **Connect**. Du behöver inte inkludera den **egenskaper för Autenticeringsanslutningssträng**.

1. I den **Navigator** i den vänstra rutan i fönstret expandera databasen, schemat, och välj sedan tabellen. Resultatfönstret omfattar alla data med hjälp av schemat som du skapade.

    ![Välj tabellen i Power BI hämta Data](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Om du vill visualisera data i Power BI desktop, markera kryssrutan framför tabellnamnet och klicka sedan på **belastningen**.

1. I Power BI Desktop, längst till vänster, Välj fliken Data ![Data-fliken i Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) Bekräfta dina data importerades.

1. Nu kan du skapa visualiseringar med Power BI genom att klicka på fliken rapport ![fliken rapport i Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), klicka på **nya Visual**, och sedan anpassa din panel. Mer information om hur du skapar visualiseringar i Power BI Desktop finns i [visualiseringstyper i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Felsökning

Om du får följande fel, se till att den **värden** och **åtkomstnyckeln** värdena som du kopierade Azure-portalen i [steg 2](#connect) är korrekta och försök sedan igen. Använd kopieringsknapparna till höger om den **värden** och **åtkomstnyckel** värdena i Azure portal för att kopiera värdena felfritt.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](introduction.md).
