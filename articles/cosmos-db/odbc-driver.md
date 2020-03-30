---
title: Ansluta till Azure Cosmos DB med bi-analysverktyg
description: Lär dig hur du använder Azure Cosmos DB ODBC-drivrutinen för att skapa tabeller och vyer så att normaliserade data kan visas i BI- och dataanalysprogramvara.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721089"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ansluta till Azure Cosmos DB med hjälp av BI-analysverktyg med ODBC-drivrutinen

Med Azure Cosmos DB ODBC-drivrutinen kan du ansluta till Azure Cosmos DB med hjälp av BI-analysverktyg som SQL Server Integration Services, Power BI Desktop och Tableau så att du kan analysera och skapa visualiseringar av dina Azure Cosmos DB-data i dessa lösningar.

Azure Cosmos DB ODBC-drivrutinen är kompatibel med ODBC 3.8 och stöder ANSI SQL-92-syntax. Drivrutinen har omfattande funktioner som hjälper dig renormalisera data i Azure Cosmos DB. Med drivrutinen kan du representera data i Azure Cosmos DB som tabeller och vyer. Du kan utföra SQL-åtgärder mot tabeller och vyer, t.ex. gruppera efter frågor, infogningar, uppdateringar och borttagningar.

> [!NOTE]
> Ansluta till Azure Cosmos DB med ODBC-drivrutinen stöds för närvarande endast för Azure Cosmos DB SQL API-konton.

## <a name="why-do-i-need-to-normalize-my-data"></a>Varför måste jag normalisera mina data?
Azure Cosmos DB är en schemalös databas som möjliggör snabb programutveckling och möjlighet att iterera på datamodeller utan att begränsas till ett strikt schema. En enda Azure Cosmos-databas kan innehålla JSON-dokument av olika strukturer. Detta är bra för snabb programutveckling, men när du vill analysera och skapa rapporter om dina data med hjälp av dataanalys och BI-verktyg, behöver data ofta förenklas och följa ett visst schema.

Det är här ODBC-drivrutinen kommer in. Genom att använda ODBC-drivrutinen kan du nu renormalize data i Azure Cosmos DB i tabeller och vyer som passar dina dataanalys- och rapporteringsbehov. De renormalized scheman har ingen inverkan på de underliggande data och inte begränsa utvecklare att följa dem. De gör det snarare möjligt för dig att utnyttja ODBC-kompatibla verktyg för att komma åt data. Så nu din Azure Cosmos-databas kommer inte bara att vara en favorit för ditt utvecklingsteam, men dina dataanalytiker kommer att älska det också.

Låt oss komma igång med ODBC-drivrutinen.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Steg 1: Installera Azure Cosmos DB ODBC-drivrutinen

1. Ladda ner drivrutinerna för din miljö:

    | Installer | Operativsystem som stöds| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bitars.msi](https://aka.ms/cosmos-odbc-64x64) för 64-bitars Windows| 64-bitarsversioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) för 32-bitars i 64-bitars Windows| 64-bitarsversioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 och Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bitars.msi](https://aka.ms/cosmos-odbc-32x32) för 32-bitars Windows|32-bitarsversioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows XP och Windows Vista.|

    Kör msi-filen lokalt, som startar **installationsguiden för Microsoft Azure Cosmos DB ODBC Driver**. 

1. Slutför installationsguiden med standardindata för att installera ODBC-drivrutinen.

1. Öppna **odbc-datakälladministratörsappen** på datorn. Du kan göra detta genom att skriva **ODBC-datakällor** i sökrutan i Windows. 
    Du kan bekräfta att drivrutinen installerades genom att klicka på fliken **Drivrutiner** och se till att **Microsoft Azure Cosmos DB ODBC Driver** visas.

    ![Administratör för Azure Cosmos DB ODBC-datakälla](./media/odbc-driver/odbc-driver.png)

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Steg 2: Ansluta till din Azure Cosmos-databas

1. När [du har installerat Azure Cosmos DB ODBC-drivrutinen](#install)klickar du på Lägg **till**i fönstret **ODBC-datakällaadministratör.** Du kan skapa en användare eller system-DSN. I det här exemplet skapar du ett användar-DSN.

1. I fönstret **Skapa ny datakälla** väljer du **Microsoft Azure Cosmos DB ODBC Driver**och klickar sedan på **Slutför**.

1. Fyll i följande information i **installationsfönstret för Azure Cosmos DB ODBC Driver SDN:** 

    ![Installationsfönstret för Azure Cosmos DB ODBC Driver DSN](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Namn på datakälla:** Ditt eget eget namn för ODBC DSN. Det här namnet är unikt för ditt Azure Cosmos DB-konto, så ge det ett lämpligt namn om du har flera konton.
    - **Beskrivning**: En kort beskrivning av datakällan.
    - **Värd:** URI för ditt Azure Cosmos DB-konto. Du kan hämta detta från sidan Azure Cosmos DB Keys i Azure-portalen, vilket visas i följande skärmbild. 
    - **Åtkomstnyckel:** Den primära eller sekundära, skrivskyddade eller skrivskyddade nyckeln från sidan Azure Cosmos DB Keys i Azure-portalen som visas i följande skärmbild. Vi rekommenderar att du använder skrivskyddad nyckel om DSN används för skrivskyddad databehandling och rapportering.
    ![Sidan Azure Cosmos DB Keys](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Kryptera åtkomstnyckel för:** Välj det bästa valet baserat på användarna av den här datorn. 
    
1. Klicka på knappen **Testa** för att se till att du kan ansluta till ditt Azure Cosmos DB-konto. 

1.  Klicka på **Avancerade alternativ** och ange följande värden:
    *  **REST API-version:** Välj [REST API-versionen](https://docs.microsoft.com/rest/api/cosmos-db/) för dina åtgärder. Standard 2015-12-16. Om du har behållare med [stora partitionsnycklar](large-partition-keys.md) och kräver REST API version 2018-12-31:
        - Skriv in **2018-12-31** för REST API-version
        - Skriv "regedit" på **Start-menyn** för att hitta och öppna **registereditorprogrammet.**
        - I Registereditorn navigerar du till sökvägen: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC. INI**
        - Skapa en ny undernyckel med samma namn som ditt DSN, t.ex.
        - Navigera till undernyckeln "Contoso Account ODBC DSN".
        - Högerklicka om du vill lägga till ett nytt **strängvärde:**
            - Värdenamn: **IgnoreSessionToken**
            - Värdedata: Inställningar för Registereditorn **1**
            ![](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Frågekonsekvens:** Välj [konsekvensnivå](consistency-levels.md) för dina åtgärder. Standard är Session.
    - **Antal återförsök:** Ange hur många gånger som ska göras om en åtgärd ska göras om den ursprungliga begäran inte slutförs på grund av att servicehastigheten begränsars.
    - **Schemafil:** Du har ett antal alternativ här.
        - Som standard, lämnar den här posten som den är (tom), söker drivrutinen den första sidan med data för alla behållare för att bestämma schemat för varje behållare. Detta kallas behållarmappning. Utan en schemafil definierad måste drivrutinen utföra sökningen för varje drivrutinssession och kan resultera i en högre starttid för ett program med hjälp av DSN. Vi rekommenderar att du alltid associerar en schemafil för ett DSN.
        - Om du redan har en schemafil (eventuellt en som du har skapat med hjälp av schemaredigeraren) kan du klicka på **Bläddra,** navigera till filen, klicka på **Spara**och sedan klicka på **OK**.
        - Om du vill skapa ett nytt schema klickar du på **OK**och sedan **på Schemaredigeraren** i huvudfönstret. Fortsätt sedan till schemaredigerarens information. När du har skapat den nya schemafilen kom ihåg att gå tillbaka till fönstret **Avancerade alternativ** för att inkludera den nyskapade schemafilen.

1. När du har slutfört och stängt fönstret **Azure Cosmos DB ODBC Driver DSN** läggs det nya användar-DSN till på fliken Användar-DSN.

    ![Nya Azure Cosmos DB ODBC DSN på fliken Användare DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Steg 3: Skapa en schemadefinition med hjälp av metoden för behållarmappning

Det finns två typer av provtagningsmetoder som du kan använda: **behållarmappning** eller **tabellavgränsare**. En samplingssession kan använda båda provtagningsmetoderna, men varje behållare kan bara använda en specifik provtagningsmetod. Stegen nedan skapar ett schema för data i en eller flera behållare med hjälp av metoden för behållarmappning. Den här samplingsmetoden hämtar data på sidan i en behållare för att bestämma datastrukturen. Den överför en behållare till ett bord på ODBC-sidan. Denna provtagningsmetod är effektiv och snabb när data i en behållare är homogena. Om en behållare innehåller heterogen typ av data rekommenderar vi att du använder [mappningsmetoden för tabellavgränsare](#table-mapping) eftersom den ger en mer robust samplingsmetod för att bestämma datastrukturerna i behållaren. 

1. När du har slutfört steg 1-4 i [Anslut till din Azure Cosmos-databas](#connect)klickar du på **Schemaredigeraren** i installationsfönstret för **Azure Cosmos DB ODBC Driver DSN.**

    ![Knappen Schemaredigerare i fönstret Azure Cosmos DB ODBC Driver DSN](./media/odbc-driver/odbc-driver-schema-editor.png)
1. Klicka på **Skapa nytt**i fönstret **Schemaredigerare.**
    Fönstret **Generera schema** visar alla behållare i Azure Cosmos DB-kontot. 

1. Markera en eller flera behållare som ska provas och klicka sedan på **Exempel**. 

1. På fliken **Designvy** representeras databasen, schemat och tabellen. I tabellvyn visar genomsökningen den uppsättning egenskaper som är associerade med kolumnnamnen (SQL-namn, källnamn osv.).
    För varje kolumn kan du ändra kolumnen SQL-namn, SQL-typ, SQL-längd (om tillämpligt), Skala (om tillämpligt), Precision (om tillämpligt) och Nullable.
    - Du kan ange **Dölj kolumn** till **true** om du vill utesluta kolumnen från frågeresultat. Kolumner markerade Dölj kolumn = sant returneras inte för markering och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB-system som krävs egenskaper som börjar med "_".
    - **ID-kolumnen** är det enda fält som inte kan döljas eftersom det används som primärnyckel i det normaliserade schemat. 

1. När du har definierat schemat klickar du på**Spara** **fil,** | navigerar till katalogen för att spara schemat och klickar sedan på **Spara**.

1. Om du vill använda det här schemat med ett DSN öppnar du **installationsfönstret för Azure Cosmos DB ODBC Driver DSN** (via ODBC-datakällaadministratören), klickar på **Avancerade alternativ**och navigerar sedan till det sparade schemat i rutan **Schemafil.** Om du sparar en schemafil i en befintlig DSN ändras DSN-anslutningen till scopet till de data och den struktur som definieras av schemat.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Steg 4: Skapa en schemadefinition med mappningsmetoden tabellavgränsare

Det finns två typer av provtagningsmetoder som du kan använda: **behållarmappning** eller **tabellavgränsare**. En samplingssession kan använda båda provtagningsmetoderna, men varje behållare kan bara använda en specifik provtagningsmetod. 

I följande steg skapas ett schema för data i en eller flera behållare med mappningsmetoden **tabellavgränsare.** Vi rekommenderar att du använder den här samplingsmetoden när behållarna innehåller heterogena typer av data. Du kan använda den här metoden för att begränsa samplingen till en uppsättning attribut och motsvarande värden. Om ett dokument till exempel innehåller egenskapen "Typ" kan du begränsa samplingen till värdena för den här egenskapen. Slutresultatet av samplingen skulle vara en uppsättning tabeller för vart och ett av värdena för Typ som du har angett. Typ = Bil kommer till exempel att producera en biltabell medan Typ = Plan skulle producera en plantabell.

1. När du har slutfört steg 1-4 i [Anslut till din Azure Cosmos-databas](#connect)klickar du på **Schemaredigeraren** i installationsfönstret för Azure Cosmos DB ODBC Driver DSN.

1. Klicka på **Skapa nytt**i fönstret **Schemaredigerare.**
    Fönstret **Generera schema** visar alla behållare i Azure Cosmos DB-kontot. 

1. Markera en behållare på fliken **Exempelvy,** klicka på **Redigera**i kolumnen **Mappningsdefinition** för behållaren . Välj sedan **Tabellavgränsare** i fönstret **Mappningsdefinition.** Gör något av följande:

    a. Skriv namnet på en avgränsare i rutan **Attribut.** Det här är en egenskap i dokumentet som du vill begränsa samplingen till till exempel Ort och tryck på retur. 

    b. Om du bara vill begränsa samplingen till vissa värden för attributet som du angav ovan markerar du attributet i markeringsrutan, anger ett värde i rutan **Värde** (t.ex. Du kan fortsätta att lägga till flera värden för attribut. Se bara till att rätt attribut väljs när du anger värden.

    Om du till exempel inkluderar värdet **attribut** för City och vill begränsa tabellen så att den bara innehåller rader med stadsvärdet New York och Dubai, anger du City i rutan Attribut och New York och sedan Dubai i rutan **Värden.**

1. Klicka på **OK**. 

1. När du har slutfört mappningsdefinitionerna för de behållare som du vill prova klickar du på **Exempel**i fönstret **Schemaredigerare** .
     För varje kolumn kan du ändra kolumnen SQL-namn, SQL-typ, SQL-längd (om tillämpligt), Skala (om tillämpligt), Precision (om tillämpligt) och Nullable.
    - Du kan ange **Dölj kolumn** till **true** om du vill utesluta kolumnen från frågeresultat. Kolumner markerade Dölj kolumn = sant returneras inte för markering och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos `_`DB-system som krävs egenskaper som börjar med .
    - **ID-kolumnen** är det enda fält som inte kan döljas eftersom det används som primärnyckel i det normaliserade schemat. 

1. När du har definierat schemat klickar du på**Spara** **fil,** | navigerar till katalogen för att spara schemat och klickar sedan på **Spara**.

1. Klicka på **Avancerade alternativ**i **fönstret Azure Cosmos DB ODBC Driver DSN.** Navigera sedan till den sparade schemafilen i rutan **Schemafil** och klicka på **OK**. Klicka på **OK** igen om du vill spara DSN. Detta sparar schemat som du skapade i DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Valfritt) Konfigurera länkad serveranslutning

Du kan fråga Azure Cosmos DB från SQL Server Management Studio (SSMS) genom att konfigurera en länkad serveranslutning.

1. Skapa en systemdatakälla enligt beskrivningen i steg `SDS Name` [2](#connect), till exempel med namnet .

1. [Installera SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och anslut till servern. 

1. Skapa ett länkat serverobjekt `DEMOCOSMOS` för datakällan med följande kommandon i SSMS-frågeredigeraren. Ersätt `DEMOCOSMOS` med namnet på den `SDS Name` länkade servern och med namnet på systemdatakällan.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Uppdatera listan Länkade servrar om du vill se det nya länkade servernamnet.

![Länkad server i SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Frågelänkade databas

Om du vill fråga efter den länkade databasen anger du en SSMS-fråga. I det här exemplet väljer frågan från tabellen `customers`i behållaren med namnet :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Kör frågan. Resultatet bör likna detta:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Den länkade Cosmos DB-servern stöder inte namngivning i fyra delar. Ett fel returneras på samma sätt som följande meddelande:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Valfritt) Skapa vyer
Du kan definiera och skapa vyer som en del av samplingsprocessen. Dessa vyer motsvarar SQL-vyer. De är skrivskyddade och är scope val och prognoser för Azure Cosmos DB SQL-frågan definieras. 

Om du vill skapa en vy för dina data klickar du på **Lägg till** på raden i behållaren som ska exempelas i fönstret **Schemaredigeraren.** **View Definitions** 
    ![Skapa en vy med data](./media/odbc-driver/odbc-driver-create-view.png)


Gör sedan följande i fönstret **Visa definitioner:**

1. Klicka på **Ny**, ange ett namn för vyn, till exempel AnställdafrånSeattleView och klicka sedan på **OK**.

1. Ange en Azure Cosmos DB-fråga i **fönstret Redigera** vy. Detta måste vara en [Azure Cosmos DB SQL-fråga](how-to-sql-query.md), till exempel `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, och klicka sedan på **OK**.

    ![Lägga till fråga när du skapar en vy](./media/odbc-driver/odbc-driver-create-view-2.png)


Du kan skapa många vyer som du vill. När du är klar med att definiera vyerna kan du sedan prova data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Steg 5: Visa dina data i BI-verktyg som Power BI Desktop

Du kan använda ditt nya DSN för att ansluta till Azure Cosmos DB med alla ODBC-kompatibla verktyg – det här steget visar bara hur du ansluter till Power BI Desktop och skapar en Power BI-visualisering.

1. Öppna Power BI Desktop.

1. Klicka på **Hämta data**.

    ![Hämta data i Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Klicka på **Annan** | **ODBC** | **Connect**i fönstret Hämta **data** .

    ![Välj ODBC-datakälla i Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. I fönstret **Från ODBC** markerar du det datakällnamn som du skapade och klickar sedan på **OK**. Du kan lämna posterna **Avancerade alternativ** tomma.

    ![Välj Datakällnamn (DSN) i Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. I **fönstret Access en datakälla med hjälp av ett ODBC-drivrutinsfönster** väljer du **Standard eller Anpassad** och klickar sedan på **Anslut**. Du behöver inte inkludera **egenskaperna för anslutningssträngen för autentiseringsuppgifter**.

1. Expandera databasen, schemat i fönstret Navigator i fönstret **Navigatör** och välj sedan tabellen. Resultatfönstret innehåller data med hjälp av schemat som du skapade.

    ![Välj tabell i Power BI Hämta data](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Om du vill visualisera data på Power BI-skrivbordet markerar du rutan framför tabellnamnet och klickar sedan på **Läs in**.

1. Välj fliken Data längst till vänster i Power BI Desktop ![Fliken Data i Power BI-skrivbordet](./media/odbc-driver/odbc-driver-data-tab.png) för att bekräfta att dina data har importerats.

1. Nu kan du skapa visuella objekt med Power ![BI genom att](./media/odbc-driver/odbc-driver-report-tab.png)klicka på fliken Rapport på fliken Rapport i Power BI Desktop, klicka på **Nytt visuellt**objekt och sedan anpassa panelen. Mer information om hur du skapar visualiseringar på Power BI Desktop finns [i Visualiseringstyper i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Felsökning

Om du får följande felmeddelande kontrollerar du att värdena **värd-** och **åtkomstnyckel** som du kopierade Azure-portalen i [steg 2](#connect) är korrekta och försöker sedan igen. Använd kopieringsknapparna till höger om värdena **Värd** och **Åtkomstnyckel** i Azure-portalen för att kopiera värdena felfria.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](introduction.md).
