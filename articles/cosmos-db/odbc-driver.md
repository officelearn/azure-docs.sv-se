---
title: "Ansluta till Azure Cosmos-databasen med verktyg för BI analytics | Microsoft Docs"
description: "Lär dig hur du använder Azure Cosmos DB ODBC-drivrutinen för att skapa tabeller och vyer så att normaliserade data kan visas i BI och data analytics-programvara."
keywords: ODBC, odbc-drivrutinen
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 01/16/2018
ms.author: mimig
ms.openlocfilehash: 3892f698ec2b0b45f71dc38491687897559821ba
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ansluta till Azure Cosmos-databasen med verktyg för analys av BI med ODBC-drivrutin

Azure Cosmos DB ODBC-drivrutinen kan du ansluta till Azure Cosmos-databasen med BI analytics-verktyg som SQL Server Integration Services och Power BI Desktop Tableau så att du kan analysera och skapa visualiseringar av dina Azure Cosmos DB-data i dessa lösningar.

Azure Cosmos DB ODBC-drivrutinen är ODBC 3.8 kompatibel och stöder ANSI SQL-92-syntax. Drivrutinen erbjuder omfattande funktioner som hjälper dig att renormalize data i Azure Cosmos DB. Du kan använder drivrutinen för att representera data i Azure Cosmos DB som tabeller och vyer. Drivrutinen kan du utföra SQL-åtgärder mot de tabeller och vyer, inklusive frågor, infogar, uppdateringar, och tar bort.

## <a name="why-do-i-need-to-normalize-my-data"></a>Varför måste normalisera Mina data?
Azure Cosmos-DB är en schemalös databas så att den möjliggör snabb utveckling av appar genom att aktivera program för att iterera sina datamodellen direkt och inte begränsa dem till ett strikt schema. En enda Azure Cosmos-DB-databas kan innehålla olika strukturer JSON-dokument. Det är bra för snabb utveckling, men när du vill analysera och skapa rapporter för dina data med hjälp av dataanalys och BI-verktyg data behöver ofta förenklas och följa ett visst schema.

Det är där ODBC-drivrutinen kommer in. Du kan nu renormalized data i Azure Cosmos DB i tabeller och vyer montering dina databehov för analys och rapportering med hjälp av ODBC-drivrutinen. Renormalized scheman har ingen inverkan på underliggande data och inte begränsa utvecklare att följa dem, de bara kan du utnyttja ODBC-kompatibel verktyg för att komma åt data. Nu Azure Cosmos-DB-databas kommer inte bara vara en favorit för Utvecklingsteamet, men data analytikerna kommer gillar det för.

Nu kan komma igång med ODBC-drivrutinen.

## <a id="install"></a>Steg 1: Installera Azure Cosmos DB ODBC-drivrutinen

1. Hämta drivrutiner för din miljö:

    | Installer | Operativsystem som stöds| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) för 64-bitars Windows| 64-bitars versioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/documentdb-odbc-32x64) för 32-bitars på Windows 64-bitars| 64-bitars versioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 och Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) för 32-bitars Windows|32-bitars versioner av Windows 8.1 eller senare, Windows 8, Windows 7, Windows XP och Windows Vista.|

    Kör msi-filen lokalt, som startar den **installationsguiden för Microsoft Azure Cosmos DB ODBC-drivrutinen**. 
2. Slutför guiden med standardinställningarna som indata för att installera ODBC-drivrutinen.
3. Öppna den **ODBC Data source Administrator** appen på din dator och du kan göra detta genom att skriva **ODBC-datakällor** i Windows-sökrutan. 
    Du kan bekräfta drivrutinen har installerats genom att klicka på den **drivrutiner** flik och se till att **ODBC-drivrutinen för Microsoft Azure Cosmos DB** visas.

    ![Azure Cosmos DB ODBC Data Source Administrator](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Steg 2: Anslut till din Azure Cosmos-DB-databas

1. Efter [installera Azure Cosmos DB ODBC-drivrutinen](#install)i den **ODBC Data Source Administrator** -fönstret klickar du på **Lägg till**. Du kan skapa en användare eller System-DSN. I det här exemplet skapar vi ett användar-DSN.
2. I den **Skapa ny datakälla** väljer **ODBC-drivrutinen för Microsoft Azure Cosmos DB**, och klicka sedan på **Slutför**.
3. I den **SDN installationsprogram för Azure Cosmos DB ODBC-drivrutinen** Fyll i följande: 

    ![Azure Cosmos DB ODBC-drivrutinen DSN inställningsfönstret](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Namn på datakälla**: egna eget namn för ODBC DSN. Det här namnet är unikt för ditt Azure DB som Cosmos-konto, så namnet på lämpligt sätt om du har flera konton.
    - **Beskrivning**: en kort beskrivning av datakällan.
    - **Värden**: URI för Azure DB som Cosmos-konto. Du kan hämta det från sidan Azure Cosmos DB nycklar i Azure-portalen som visas i följande skärmbild. 
    - **Snabbtangent**: den primära eller sekundära, skrivskyddad eller skrivskyddad nyckeln från Azure Cosmos DB nycklar sidan i Azure-portalen som visas i följande skärmbild. Vi rekommenderar att du använder den skrivskyddade nyckeln om DSN används för skrivskyddade databehandling och rapportering.
    ![Azure DB-nycklar för Cosmos-sida](./media/odbc-driver/odbc-driver-keys.png)
    - **Kryptera åtkomstnyckeln för**: Välj det bästa valet baserat på användare för den här datorn. 
4. Klicka på den **Test** för att kontrollera att du kan ansluta till ditt konto i Azure Cosmos DB. 
5. Klicka på **avancerade alternativ** och ange följande värden:
    - **Fråga konsekvenskontroll**: Välj den [konsekvensnivå](consistency-levels.md) för din verksamhet. Standardvärdet är Session.
    - **Antal återförsök**: Ange hur många gånger ska försöka utföra en åtgärd om den ursprungliga begäranden inte slutföras på grund av begränsning av tjänsten.
    - **Schemafilen**: du har ett antal alternativ här.
        - Som standard, lämnar den här posten är (tom) söker drivrutinen första sidan data för alla samlingar att fastställa schemat för varje samling. Detta kallas samlingen mappning. Drivrutinen har att utföra sökningen för varje drivrutin session utan en schemafilen som definierats och kan resultera i en högre starttiden för ett program med hjälp av DSN. Vi rekommenderar att du alltid associerar en schemafil för en Datakälla.
        - Om du redan har en schemafilen (eventuellt en som du skapat med hjälp av [schemat redigeraren](#schema-editor)), kan du klicka på **Bläddra**, navigera till filen, klicka på **spara**, och klicka sedan på **OK**.
        - Om du vill skapa ett nytt schema, klickar du på **OK**, och klicka sedan på **schemat Editor** i huvudfönstret. Gå sedan vidare till den [schemat Editor](#schema-editor) information. När du skapar den nya schemafilen, Kom ihåg att gå tillbaka till den **avancerade alternativ** fönstret ska omfatta schemafilen nyligen skapade.

6. När du slutför och stänger den **Azure Cosmos DB ODBC-drivrutinen DSN för** och den nya användaren DSN har lagts till i fliken användar-DSN.

    ![Nya Azure Cosmos DB ODBC DSN på fliken användar-DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Steg 3: Skapa schemadefinition med metoden samling mappning

Det finns två typer av provtagningsmetoder som du kan använda: **samling mappning** eller **tabell avgränsare**. En provtagning session kan använda båda provtagningsmetoderna, men varje samling kan bara använda en specifik urvalsmetoden. Stegen nedan för att skapa ett schema för data i en eller flera samlingar med metoden samling mappning. Den här urvalsmetoden hämtar-data på sidan i en samling för att fastställa strukturen för data. Den transposes en samling till en tabell på ODBC-sida. Den här urvalsmetoden är effektivt och snabbt när data i en samling är homogen. Om en samling innehåller heterogena typ av data, rekommenderar vi du använder den [tabell avgränsare mappning metoden](#table-mapping) eftersom det ger en mer robusta urvalsmetoden för att fastställa datastrukturer i samlingen. 

1. När du har slutfört steg 1 – 4 i [Anslut till Azure Cosmos DB databasen](#connect), klickar du på **schemat Editor** i den **Azure Cosmos DB ODBC-drivrutinen DSN för** fönster.

    ![Schemat editor-knapp i fönstret DSN inställningar för Azure Cosmos DB ODBC-drivrutin](./media/odbc-driver/odbc-driver-schema-editor.png)
2. I den **schemat Editor** -fönstret klickar du på **Skapa nytt**.
    Den **generera schemat** fönstret visas alla samlingar i Azure DB som Cosmos-konto. 
3. Välj en eller flera samlingar till exempel och klicka sedan på **exempel**. 
4. I den **designvyn** fliken, databas, schema och tabellen representeras. I tabellvyn visar genomsökningen uppsättning egenskaper som är associerade med kolumnnamn (SQL-namn, namnet på datakällan, osv.).
    För varje kolumn, kan du ändra kolumnnamnet för SQL, SQL-typ, SQL-längd (om tillämpligt), skala (om tillämpligt), Precision (om tillämpligt) och kan ha värdet null.
    - Du kan ange **Dölj kolumn** till **SANT** om du vill utesluta kolumnen från frågeresultat. Kolumner markerad Dölj kolumn = true returneras inte för val och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB krävs systemegenskaperna börjar med ”_”.
    - Den **id** kolumn är det enda fält inte får vara dolda eftersom den används som primärnyckel i normaliserade schemat. 
5. När du har definierat schemat klickar du på **filen** | **spara**, gå till katalogen för att spara schemat och klicka sedan på **spara**.

    Om du i framtiden vill använda det här schemat med en Datakälla öppna fönstret Azure Cosmos DB ODBC-drivrutinen DSN för (via ODBC Data Source Administrator), klicka på Avancerat och navigera till sparade schemat i rutan schemafilen. En schemafil sparas till en befintlig DSN ändrar DSN-anslutningen till scope till data och struktur som definieras av schemat.

## <a id="table-mapping"></a>Steg 4: Skapa en schemadefinition med tabell-avgränsare mappning metod

Det finns två typer av provtagningsmetoder som du kan använda: **samling mappning** eller **tabell avgränsare**. En provtagning session kan använda båda provtagningsmetoderna, men varje samling kan bara använda en specifik urvalsmetoden. 

Följande steg att skapa ett schema för data i en eller flera samlingar med hjälp av den **tabell avgränsare** mappning av metoden. Vi rekommenderar att du använder den här provtagningsmetoden när samlingar innehåller heterogena typ av data. Du kan använda den här metoden för att begränsa beräkningarna till en uppsättning attribut och dess motsvarande värden. Om ett dokument innehåller en egenskap för ”typ”, kan du definiera beräkningarna till värden i den här egenskapen. Slutresultatet för provtagning är en uppsättning tabeller för alla värden för typ som du har angett. Skriv till exempel = bil genererar en bil tabell vid typ = plan skulle skapa en plan tabell.

1. När du har slutfört steg 1 – 4 i [Anslut till Azure Cosmos DB databasen](#connect), klickar du på **schemat redigeraren** i fönstret DSN installationsprogram för Azure Cosmos DB ODBC-drivrutinen.
2. I den **schemat Editor** -fönstret klickar du på **Skapa nytt**.
    Den **generera schemat** fönstret visas alla samlingar i Azure DB som Cosmos-konto. 
3. Markera en samling på den **Exempelvyn** fliken den **definitionen mappning** för samlingen, klickar du på **redigera**. I den **definitionen mappning** väljer **tabell avgränsare** metod. Gör något av följande:

    a. I den **attribut** skriver namnet på en egenskap som avgränsare. Detta är en egenskap i dokumentet som du vill omfång provtagning till exempelvis ort och tryck på RETUR. 

    b. Välj attributet i rutan om du bara vill omfång provtagning till vissa värden för attributet som du angav och ange ett värde i den **värdet** rutan, till exempel Seattle och tryck på RETUR. Du kan fortsätta att lägga till flera värden för attributen. Se bara till att rätt attributet aktiveras när du anger värden.

    Om du inkluderar exempelvis en **attribut** värdet av ort, och du vill begränsa din tabell om du vill inkludera endast rader med ett värde för ort i New York och Dubai, anger du stad i rutan attribut, och New York och sedan Dubai i den **värden** rutan.
4. Klicka på **OK**. 
5. När du har slutfört mappning definitioner för samlingarna du vill prova i den **schemat Editor** -fönstret klickar du på **exempel**.
     För varje kolumn, kan du ändra kolumnnamnet för SQL, SQL-typ, SQL-längd (om tillämpligt), skala (om tillämpligt), Precision (om tillämpligt) och kan ha värdet null.
    - Du kan ange **Dölj kolumn** till **SANT** om du vill utesluta kolumnen från frågeresultat. Kolumner markerad Dölj kolumn = true returneras inte för val och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB krävs systemegenskaperna börjar med ”_”.
    - Den **id** kolumn är det enda fält inte får vara dolda eftersom den används som primärnyckel i normaliserade schemat. 
6. När du har definierat schemat klickar du på **filen** | **spara**, gå till katalogen för att spara schemat och klicka sedan på **spara**.
7. I den **Azure Cosmos DB ODBC-drivrutinen DSN för** -fönstret klickar du på ** Avancerade alternativ **. I den **schemafilen** , navigera till den sparade schemafilen och klicka **OK**. Klicka på **OK** igen för att spara det DSN-namnet. Detta sparar det schema som du skapade det DSN-namnet. 

## <a name="optional-creating-views"></a>(Valfritt) Skapa vyer
Du kan definiera och skapa vyer som en del av processen provtagning. Dessa vyer är likvärdiga med SQL-vyer. De är skrivskyddade och scope valen och projektioner av Azure Cosmos-Databasens SQL definieras. 

Du skapar en vy för dina data i den **schemat Editor** fönster i den **vydefinitioner** kolumnen, klickar du på **Lägg till** på raden i samlingen till exempel. I den **vydefinitioner** fönster, gör du följande:
1. Klicka på **ny**, ange ett namn för vyn, till exempel EmployeesfromSeattleView och klicka sedan på **OK**.
2. I den **Redigera vy** och ange en Azure Cosmos DB-fråga. Detta måste vara en Azure Cosmos-Databasens SQL-fråga, till exempel`SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`, och klicka sedan på **OK**.

Du kan skapa flera vyer som du vill. När du är klar definierar vyerna som du kan sedan ta prov data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Steg 5: Visa dina data i BI-verktyg som Power BI Desktop

Du kan använda din nya DSN för att ansluta DocumentADB med verktyg som ODBC-kompatibel – det här steget bara visar hur du ansluter till Power BI Desktop och skapa en Power BI-visualisering.

1. Öppna Power BI Desktop.
2. Klicka på **hämta Data**.
3. I den **hämta Data** -fönstret klickar du på **andra** | **ODBC** | **Anslut**.
4. I den **från ODBC** väljer du datakällan servernamnet du skapat och klicka sedan på **OK**. Du kan lämna den **avancerade alternativ** poster som är tom.
5. I den **åtkomst till en datakälla med hjälp av en ODBC-drivrutin** väljer **standard eller anpassad** och klicka sedan på **Anslut**. Du behöver inte inkludera den **autentiseringsuppgifter anslutningssträngsegenskaper**.
6. I den **Navigator** i den vänstra rutan i fönstret expanderar du databasen, schemat, och välj tabellen. Resultatfönstret innehåller data med det schema som du skapade.
7. Om du vill visualisera data i Power BI desktop kryssrutan framför tabellnamnet och klicka sedan på **belastningen**.
8. I Power BI Desktop längst till vänster, klicka på fliken Data ![Datafliken i Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) Bekräfta dina data importerades.
9. Du kan nu skapa grafer med Power BI genom att klicka på fliken rapport ![fliken rapport i Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), klicka på **nya Visual**, och anpassa din panel. Mer information om hur du skapar visualiseringar i Power BI Desktop finns [visualiseringstyper i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Felsökning

Om du får följande fel, se till att den **värden** och **åtkomstnyckeln** värden som du kopierade i Azure portal [steg 2](#connect) är korrekta och försök sedan igen. Använd knapparna kopiera till höger om den **värden** och **åtkomstnyckeln** värden i Azure portal för att kopiera värdena felfritt.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](introduction.md).
