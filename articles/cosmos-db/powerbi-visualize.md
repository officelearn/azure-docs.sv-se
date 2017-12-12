---
title: "Power BI självstudiekurs för Azure Cosmos DB connector | Microsoft Docs"
description: "Använd den här Power BI självstudiekursen för att importera JSON, skapa insiktsfulla rapporter och visualisera data med hjälp av Azure Cosmos DB och Power BI-anslutningen."
keywords: Power bi kursen, visualisera data, power bi-anslutningsprogrammet
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: mimig
ms.openlocfilehash: 6414cdc942c43f6eb13ca8f050d6503bdd3e0b42
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Power BI-självstudiekurs för Azure Cosmos DB: visualisera data med Power BI-anslutningsprogrammet
[PowerBI.com](https://powerbi.microsoft.com/) är en onlinetjänst där du kan skapa och dela instrumentpaneler och rapporter med data som är viktiga för dig och din organisation.  Power BI Desktop är en dedikerad rapport redigeringsverktyg som gör att du kan hämta data från olika datakällor, slå samman och transformera data, skapa kraftfulla rapporter och visualiseringar och publicera rapporterna till Power BI.  Med den senaste versionen av Power BI Desktop kan du nu ansluta till ditt Cosmos-DB-konto via Cosmos-DB-koppling för Power BI.   

I självstudierna Power BI vi att gå igenom stegen för att ansluta till en Cosmos-DB-konto i Power BI Desktop navigerar du till en samling där vi vill extrahera data med Navigatören, transformera JSON-data i tabellformat med Power BI Desktop Query Editor , skapa och publicera en rapport till PowerBI.com.

Den här kursen Power BI, kommer du att kunna besvara följande frågor:  

* Hur kan jag skapa rapporter med data från Cosmos-databasen med hjälp av Power BI Desktop?
* Hur kan ansluta till en Cosmos-DB-konto i Power BI Desktop?
* Hur kan jag hämta data från en samling i Power BI Desktop
* Hur kan jag omvandla kapslade JSON-data i Power BI Desktop?
* Hur kan jag för att publicera och dela Mina rapporter på PowerBI.com?

> [!NOTE]
> Power BI-anslutningsprogrammet för Azure Cosmos DB ansluter till Power BI Desktop för extrahering och omvandling av data. Rapporter som skapas i Power BI Desktop kan sedan publiceras till PowerBI.com. Direkt extrahering och omvandling av Azure Cosmos DB data kan inte utföras på PowerBI.com. 

> [!NOTE]
> För att ansluta Azure Cosmos DB till Power BI med MongoDB-API, måste du använda den [Simba MongoDB ODBC-drivrutinen](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Krav
Innan du följer anvisningarna i den här självstudiekursen för Power BI, kontrollera att du har åtkomst till följande resurser:

* [Den senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Åtkomst till vår demo-konto eller data i ditt Cosmos-DB-konto.
  * Demo-konto fylls med vulkanen data visas i den här kursen. Det här demo-kontot inte är bunden av alla serviceavtal och är avsedd endast i demonstrationssyfte.  Vi förbehåller sig rätten att göra ändringar i den här demon konto som men begränsat inte till, avslutar kontot, ändra nyckeln, begränsa åtkomst, ändra, och ta bort data, när som helst utan varsel eller orsak.
    * URL: https://analytics.documents.azure.com
    * Skrivskyddad sekundärnyckel: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR + YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw ==
  * För att skapa ditt eget konto, se [skapa ett Azure DB som Cosmos-databaskonto med Azure-portalen](https://azure.microsoft.com/documentation/articles/create-account/). Sedan för att hämta exempel vulkanen data som liknar vad används i den här kursen (men inte innehåller GeoJSON-block), finns det [NOAA plats](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) och sedan importera data med hjälp av den [Azure Cosmos DB datamigreringsverktyg](import-data.md).

Du måste ha ett konto på PowerBI.com om du vill dela dina rapporter på PowerBI.com.  Läs mer om Power BI för kostnadsfria och Power BI Pro [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Nu börjar vi
I den här kursen ska vi anta att du en geologist studerar snö runtom i världen.  Vulkanen data lagras i ett Cosmos-DB-konto och JSON-dokument som ser ut som följande exempeldokument.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Vill du hämta vulkanen data från Cosmos-DB-kontot och visualisera data i en interaktiv Power BI-rapport som följande rapport.

![Den här kursen Power BI med anslutningsprogrammet för Power BI, kommer du att kunna visualisera data med Power BI Desktop vulkanen rapporten](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Vill du prova? Nu sätter vi igång.

1. Kör Power BI Desktop på din arbetsstation.
2. När Power BI Desktop startas en *Välkommen* visas.
   
    ![Power BI Desktop välkomstskärmen - anslutningsprogrammet för Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Du kan **hämta Data**, se **de senaste källorna**, eller **öppna andra rapporter** direkt från den *Välkommen* skärmen.  Klicka på X i det övre högra hörnet för att stänga skärmen. Den **rapporten** av Power BI Desktop visas.
   
    ![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Välj den **Start** band, och klicka sedan på **hämta Data**.  Den **hämta Data** fönster visas.
5. Klicka på **Azure**väljer **Microsoft Azure DocumentDB (Beta)**, och klicka sedan på **Anslut**. 

    ![Power BI Desktop hämta Data - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. På den **Preview Connector** klickar du på **Fortsätt**. Den **Microsoft Azure DocumentDB Connect** visas.
7. Ange Cosmos DB konto slutpunkts-URL du vill hämta data från enligt nedan och klickar sedan på **OK**. Om du vill använda ditt eget konto kan du hämta URL: en från rutan URI i den  **[nycklar](manage-account.md#keys)**  bladet i Azure-portalen. Om du vill använda demo-kontot måste ange `https://analytics.documents.azure.com` för URL. 
   
    Lämna databasnamnet, samlingsnamn och SQL-instruktionen tomt eftersom dessa fält är valfria.  I stället använder vi Navigatören för att markera den databas och samling för att identifiera där data hämtas från.
   
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - Desktop ansluta fönster](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Om du ansluter till den här slutpunkten för första gången uppmanas för nyckeln konto. För ditt eget konto kan du hämta nyckeln från den **primärnyckel** rutan den  **[skrivskyddade nycklar](manage-account.md#keys)**  bladet i Azure-portalen. För kontot demo nyckeln är `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Ange rätt nyckel och klickar sedan på **Anslut**.
   
    Vi rekommenderar att du använder den skrivskyddade nyckeln när du skapar rapporter.  Detta förhindrar onödig exponering av huvudnyckeln för potentiella säkerhetsrisker. Skrivskyddad nyckeln är tillgänglig från den [nycklar](manage-account.md#keys) bladet för Azure-portalen eller du kan använda demo-kontoinformation som anges ovan.
   
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - Kontonyckel](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Om du får ett felmeddelande som säger ”den angivna databasen inte kunde hittas”. Se lösningen steg i den här [Power BI problemet](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. När kontot har anslutit den **Navigator** visas.  Den **Navigator** visar en lista över databaser under kontot.
10. Klicka på och expandera på databasen där data i rapporten hämtas från, om du använder kontot demo väljer **volcanodb**.   
11. Nu Välj en samling som du ska hämta data från. Om du använder kontot demo väljer **volcano1**.
    
    Förhandsgranskning Visar en lista över **post** objekt.  Ett dokument återges som en **post** typ i Power BI. På samma sätt kan en kapslad JSON-block i ett dokument är också en **post**.
    
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - Navigator-fönstret](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klicka på **redigera** att starta frågeredigeraren i ett nytt fönster för att omvandla data.

## <a name="flattening-and-transforming-json-documents"></a>Förenkla och omvandla JSON-dokument
1. Växla till Power BI Query Editor-fönstret där den **dokument** kolumnen i den mittersta rutan.
   ![Power BI Desktop frågeredigeraren](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klicka på expander på höger sida av den **dokument** kolumnrubriken.  Snabbmenyn med en lista med fält visas.  Välj fält som du behöver för rapporten, till exempel, vulkanen namn, land, Region, plats, höjning, typ, Status och senaste vet fall, och klicka sedan på **OK**.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - Expandera dokument](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Den mittersta rutan visas en förhandsgranskning av resultatet med fält som har valts.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - platta ut resultat](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. I vårt exempel är Platsegenskapen ett GeoJSON-block i ett dokument.  Som du ser plats representeras som en **post** typ i Power BI Desktop.  
5. Klicka på expander på höger sida av kolumnrubriken plats.  Snabbmenyn med typ och koordinater fält visas.  Vi väljer koordinater fältet och klickar på **OK**.
   
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - posten](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Den mittersta rutan visas nu en kolumn för koordinater i **lista** typen.  Visas i början av kursen är GeoJSON data i den här kursen av typen punkt med latitud och longitud värden i matrisen koordinater.
   
    Elementet koordinater [0] representerar longitud medan koordinater [1] representerar latitud.
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - koordinater lista](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Om du vill förenkla koordinater matrisen ska vi skapa en **anpassad kolumnen** kallas LatLong.  Välj den **Add Column** band och klicka på **Lägg till anpassad kolumn**.  Den **Lägg till anpassad kolumn** fönster visas.
8. Ange ett namn för den nya kolumnen, t.ex. LatLong.
9. Ange sedan den anpassade formeln för den nya kolumnen.  I vårt exempel kommer vi sammanfoga latitud och longitud värden åtskilda med kommatecken enligt nedan med hjälp av följande formel: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Klicka på **OK**.
   
    Mer information om Data Analysis uttryck (DAX) inklusive DAX-funktioner finns [DAX grundläggande i Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - Lägg till anpassad kolumn](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. Nu visas den mittersta rutan kolumnen nytt LatLong fylls med värdena latitud och longitud avgränsade med kommatecken.
    
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - kolumnen för anpassade LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Om du får ett fel i den nya kolumnen, se till att tillämpade stegen under frågeinställningar matchar följande bild:
    
    ![Tillämpade steg ska vara källan, navigering, expanderas dokumentet, expanderas Document.Location, lägga till anpassad](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Om stegen är olika, ta bort extra steg och försök lägga till anpassade kolumnen igen. 
11. Vi har nu slutfört förenkla data i tabellformat.  Du kan utnyttja alla funktioner som är tillgängliga i frågeredigeraren till form och transformera data i Cosmos-databasen.  Om du använder exemplet ändra datatypen för höjning till **heltal** genom att ändra den **datatyp** på den **Start** menyfliksområdet.
    
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - ändra kolumntypen](./media/powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Klicka på **stängs och** spara datamodellen.
    
    ![Power BI självstudiekurs för Azure Cosmos DB Power BI-anslutningsprogrammet - Stäng & tillämpa](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Skapa rapporter
Power BI Desktop rapportvyn är där du kan börja skapa rapporter för att visualisera data.  Du kan skapa rapporter genom att dra och släppa fält i den **rapporten** arbetsyta.

![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

I rapportvyn bör du hitta:

1. Den **fält** rutan detta är där du kan se en lista över datamodeller med fält som du kan använda för dina rapporter.
2. Den **visualiseringar** fönstret. En rapport kan innehålla en eller flera visualiseringar.  Välj vilka visual passa dina behov från den **visualiseringar** fönstret.
3. Den **rapporten** arbetsyta, det är där du skapar den visuella informationen för rapporten.
4. Den **rapporten** sidan. Du kan lägga till flera rapportsidor i Power BI Desktop.

Nedan visas de grundläggande stegen för att skapa en enkel interaktiv karta Visa rapport.

1. I vårt exempel skapar vi en karta vy som visar platsen för varje vulkanen.  I den **visualiseringar** klickar du på kartan visual typ som är markerade i skärmbilden ovan.  Du bör se den visuella karttyp återges i den **rapporten** arbetsyta.  Den **visualiseringen** rutan bör också visa en uppsättning egenskaper som rör visual schematyp.
2. Nu dra och släpp fältet LatLong från den **fält** att den **plats** egenskap i **visualiseringar** fönstret.
3. Därefter dra och släpp fältet vulkanen namn som den **förklaring** egenskapen.  
4. Dra och släpp höjning fältet till den **storlek** egenskapen.  
5. Du bör nu se kartan visual visar en uppsättning bubblor som anger platsen för varje vulkanen med storleken på bubblan korrelerar till höjning av vulkanen.
6. Du har nu skapat en enkel rapport.  Ytterligare kan du anpassa rapporten genom att lägga till fler.  I vårt fall vi har lagt till ett utsnitt vulkanen typ så att rapporten interaktiva.  
   
    ![Skärmbild av den slutgiltiga rapporten i Power BI Desktop vid slutförandet av Power BI-självstudiekurs för Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Publicera och dela din rapport
Om du vill dela din rapport, måste du ha ett konto på PowerBI.com.

1. I Power BI Desktop klickar du på den **Start** menyfliksområdet.
2. Klicka på **Publicera**.  Du uppmanas att ange användarnamn och lösenord för ditt konto på PowerBI.com.
3. När autentiseringsuppgifterna har autentiserats publiceras rapporten till ditt mål som du har valt.
4. Klicka på **öppna 'PowerBITutorial.pbix' i Power BI** finns och att dela din rapport på PowerBI.com.
   
    ![Publicering till att Power BI lyckades! Öppna kursen i Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Skapa en instrumentpanel i PowerBI.com
Nu när du har en rapport kan du dela den på PowerBI.com

När du publicerar din rapport från Power BI Desktop till PowerBI.com genererar en **rapporten** och en **Dataset** i PowerBI.com-klienten. Till exempel när du har publicerat en rapport som kallas **PowerBITutorial** till PowerBI.com, visas PowerBITutorial i både den **rapporter** och **datauppsättningar** avsnitt på PowerBI.com.

   ![Skärmbild av den nya rapporten och Dataset på PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Klicka för att skapa en instrumentpanel som delas av **PIN-kod Live sidan** knappen på PowerBI.com rapporten.

   ![Skärmbild av den nya rapporten och Dataset på PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Följ instruktionerna i [fästa en panel från en rapport](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) att skapa en ny instrumentpanel. 

Du kan också göra tillfälliga ändringar i rapporten innan du skapar en instrumentpanel. Det rekommenderas dock att du använder Power BI Desktop utför ändringar och publicera om rapporten till PowerBI.com.

## <a name="refresh-data-in-powerbicom"></a>Uppdatera data på PowerBI.com
Det finns två sätt att uppdatera data, ad hoc- och schemalagda.

För en ad hoc-uppdatering, klicka på eclipses (...) av den **Dataset**, t.ex. PowerBITutorial. Du bör se en lista med åtgärder, inklusive **Uppdatera nu**. Klicka på **Uppdatera nu** datauppdatering.

![Skärmbild av Uppdatera nu på PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

Gör följande för en schemalagd uppdatering.

1. Klicka på **Uppdatera schema** i listan åtgärd. 

    ![Skärmbild av schemauppdatering på PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. I den **inställningar** expanderar **datakällans autentiseringsuppgifter**. 
3. Klicka på **redigera autentiseringsuppgifter**. 
   
    Konfigurera popup-fönster visas. 
4. Ange nyckel för att ansluta till Cosmos-DB-konto för uppsättningen data och klicka sedan på **logga in**. 
5. Expandera **Uppdatera schema** och ställa in det schema som du vill uppdatera datauppsättningen. 
6. Klicka på **tillämpa** och du är klar ställer in schemalagd uppdatering.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Power BI i [Kom igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Mer information om Cosmos DB finns på [Azure Cosmos DB dokumentations-landningssidan](https://azure.microsoft.com/documentation/services/cosmos-db/).

