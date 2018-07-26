---
title: Power BI-självstudiekurs för Azure Cosmos DB-anslutningsapp | Microsoft Docs
description: Använd den här Power BI-självstudiekursen för att importera JSON, skapa insiktsfulla rapporter och visualisera data med Azure Cosmos DB och Power BI-kopplingen.
keywords: Power bi självstudien, visualisera data, power bi-anslutningsapp
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: sngun
ms.openlocfilehash: 3734c30dad0735164d4501d1d63d8ffe301396a3
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249122"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Power BI-självstudiekurs för Azure Cosmos DB: visualisera data med hjälp av anslutningsprogrammet för Power BI
[PowerBI.com](https://powerbi.microsoft.com/) är en onlinetjänst där du kan skapa och dela instrumentpaneler och rapporter med data som är viktiga för dig och din organisation.  Power BI Desktop är en dedikerad rapportredigeringsverktyget där du kan hämta data från olika datakällor, sammanfoga och omvandla data, skapa kraftfulla rapporter och visualiseringar och publicera rapporterna till Power BI.  Med den senaste versionen av Power BI Desktop kan ansluta du nu till ditt Azure Cosmos DB-konto via Azure Cosmos DB-anslutningsprogrammet för Power BI.   

I den här Power BI-självstudien går vi igenom stegen för att ansluta till ett Azure Cosmos DB-konto i Power BI Desktop navigerar du till en samling där vi vill att extrahera data med hjälp av Navigatören, transformera JSON-data i tabellformat med frågeredigeraren för Power BI Desktop , och skapa och publicera en rapport till PowerBI.com.

När du har slutfört den här självstudiekursen i Power BI, kommer du att kunna besvara följande frågor:  

* Hur kan jag skapa rapporter med data från Azure Cosmos DB med Power BI Desktop?
* Hur kan jag ansluta till ett Azure Cosmos DB-konto i Power BI Desktop?
* Hur kan jag hämta data från en samling i Power BI Desktop?
* Hur kan jag omvandla kapslad JSON-data i Power BI Desktop?
* Hur kan jag publicera och dela Mina rapporter på PowerBI.com?

> [!NOTE]
> Anslutningsprogrammet för Power BI för Azure Cosmos DB ansluter till Power BI Desktop för extrahering och transformering av data. Rapporter som skapats i Power BI Desktop kan senare publiceras till PowerBI.com. Direct extrahering och transformering av data i Azure Cosmos DB kan inte utföras på PowerBI.com. 

> [!NOTE]
> Ansluta till Azure Cosmos DB med anslutningsprogrammet för Power BI stöds för närvarande för Azure Cosmos DB SQL- och MongoDB API-konton. För att ansluta Azure Cosmos DB till Power BI med hjälp av MongoDB-API, måste du använda den [Simba MongoDB ODBC-drivrutinen](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har åtkomst till följande resurser innan du följer anvisningarna i den här självstudien för Power BI:

* [Den senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Åtkomst till vår demo-konto eller data i ditt Azure Cosmos DB-konto.
  * Demodata fylls med vulkanen data som visas i den här självstudien. Det här demo-kontot är inte bunden av alla serviceavtal och är avsedd endast i demonstrationssyfte.  Vi förbehåller oss rätten att göra ändringar inklusive den här demon konto men begränsat inte till, avslutar kontot, ändra nyckeln, begränsa åtkomst, ändra, och ta bort data, när som helst utan att i förväg eller orsak.
    * URL: https://analytics.documents.azure.com
    * Skrivskyddade nyckel: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR + YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw ==
  * För att skapa ditt eget konto, se [skapa ett Azure Cosmos DB-databaskonto med Azure portal](https://azure.microsoft.com/documentation/articles/create-account/). Sedan för att hämta exemplet vulkanen data som liknar vad används i den här självstudien (men inte innehåller GeoJSON-block), finns i den [NOAA plats](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) och sedan importera data med den [Migreringsverktyget för Azure Cosmos DB data](import-data.md).

Du måste ha ett konto på PowerBI.com om du vill dela dina rapporter på PowerBI.com.  Läs mer om Power BI för kostnadsfri och Power BI Pro [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Nu börjar vi
I de här självstudierna Låt oss anta att du är en geologist studerat snö runt om i världen.  Vulkanen data lagras i ett Azure Cosmos DB-konto och JSON-dokument ut följande exempeldokument.

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

Du vill hämta vulkanen data från Azure Cosmos DB-kontot och visualisera data i en interaktiv Power BI-rapport som följande rapport.

![Genom att slutföra den här självstudiekursen för Power BI med anslutningsprogrammet för Power BI kommer du att kunna visualisera data med Power BI Desktop vulkanen-rapport](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Är du redo att prova? Nu sätter vi igång.

1. Kör Power BI Desktop på din arbetsstation.
2. När Power BI Desktop startas visas en *Välkommen* skärm som visas.
   
    ![Power BI Desktop välkomstskärmen - anslutningsprogrammet för Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Du kan **hämta Data**, se **senast använda källor**, eller **öppna andra rapporter** direkt från den *Välkommen* skärmen.  Klicka på X i det övre högra hörnet för att stänga av skärmen. Den **rapporten** av Power BI Desktop visas.
   
    ![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Välj den **Start** menyfliksområdet och sedan på **hämta Data**.  Den **hämta Data** fönster visas.
5. Klicka på **Azure**väljer **Azure Cosmos DB (Beta)**, och klicka sedan på **Connect**. 

    ![Power BI Desktop hämta Data - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. På den **Förhandsgranska Anslutningsapp** klickar du på **Fortsätt**. Den **Azure Cosmos DB** fönster visas.
7. Ange Azure Cosmos DB-konto slutpunkts-URL du vill hämta data från enligt nedan och klicka sedan på **OK**. Om du vill använda ditt eget konto, du kan hämta URL: en URI som visas i den **[nycklar](manage-account.md#keys)** -bladet i Azure-portalen. Om du vill använda demodata ange `https://analytics.documents.azure.com` för URL: en. 
   
    Lämna den databasens namn, samlingsnamn och SQL-instruktionen tomt eftersom de här fälten är valfria.  I stället använder vi Navigatören för att markera den databas och samling för att identifiera varifrån data kommer från.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – Desktop ansluta fönster](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Om du ansluter till den här slutpunkten för första gången uppmanas du kontonyckeln. För ditt eget konto kan du hämta nyckeln från den **primärnyckel** rutan den **[skrivskyddade nycklar](manage-account.md#keys)** -bladet i Azure-portalen. För kontot demo nyckeln är `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Ange rätt nyckel och klickar sedan på **Connect**.
   
    Vi rekommenderar att du använder den skrivskyddade nyckeln när du skapar rapporter.  Detta förhindrar onödig exponering av huvudnyckeln för potentiella säkerhetsrisker. Skrivskyddade nyckeln är tillgänglig från den [nycklar](manage-account.md#keys) -bladet i Azure portal eller du kan använda demo-kontoinformation som anges ovan.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – Kontonyckel](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Om du får ett felmeddelande som säger ”den angivna databasen inte hittades”. Se lösningen steg i den här [Power BI problemet](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. När kontot har anslutit den **Navigator** visas fönstret.  Den **Navigator** visar en lista över databaser under kontot.
10. Klicka på och expandera på databasen där data för rapporten som kommer från, om du använder demodata, välja **volcanodb**.   
11. Nu Välj en samling som innehåller data som ska hämtas. Om du använder demodata väljer **volcano1**.
    
    Förhandsgranskningsfönstret visar en lista över **post** objekt.  Ett dokument representeras som en **post** typ i Power BI. På samma sätt kan en kapslad JSON-block i ett dokument är också en **post**.
    
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – fönstret navigatör](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klicka på **redigera** att starta frågeredigeraren i ett nytt fönster för att transformera data.

## <a name="flattening-and-transforming-json-documents"></a>Förenkla och transformera JSON-dokument
1. Växla till Power BI Query Editor-fönstret där den **dokumentet** kolumnen i den mittersta rutan.
   ![Frågeredigeraren för Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klicka på expander på höger sida av den **dokumentet** kolumnrubriken.  Snabbmeny med en lista med fält visas.  Välj de fält som behövs för rapporten, till exempel vulkanen namn, land, Region, plats, utökade privilegier, typ, Status och senaste vet fall. Avmarkera den **Använd ursprungligt kolumnnamn som prefix** rutan och klicka sedan på **OK**.
   
    ![Power BI-självstudiekursen för Azure Cosmos DB Power BI-anslutningen – Expandera dokument](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Den mittersta rutan visas en förhandsgranskning av resultatet med de fält som valts.
   
    ![Power BI-självstudiekursen för Azure Cosmos DB Power BI-anslutningen – platta ut resultat](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. I vårt exempel är egenskapen Location ett GeoJSON-block i ett dokument.  Som du ser plats representeras som en **post** typ i Power BI Desktop.  
5. Klicka på expander på höger sida av Document.Location kolumnrubriken.  Snabbmenyn med typ och koordinaterna fält visas.  Vi väljer du fältet koordinater, se till att **Använd ursprungligt kolumnnamn som prefix** inte är markerat och klicka på **OK**.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – posten](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Den mittersta rutan visar nu en koordinater kolumn med **lista** typen.  Visas i början av självstudien är GeoJSON-data i den här självstudien av typen punkt med latitud och longitud värden i matrisen koordinater.
   
    Elementet koordinaterna [0] representerar longitud medan koordinaterna [1] representerar latitud.
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – koordinater lista](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. För att platta ut koordinater matris, skapa en **anpassad kolumn** kallas LatLong.  Välj den **Lägg till kolumn** menyfliksområdet och klicka på **anpassad kolumn**.  Den **anpassad kolumn** fönster visas.
8. Ange ett namn för den nya kolumnen, t.ex. LatLong.
9. Ange anpassade formeln för den nya kolumnen.  I vårt exempel ska vi sammanfoga latitud och longitud värden avgränsade med semikolon, enligt nedan med hjälp av följande formel: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Klicka på **OK**.
   
    Mer information på Dataanalysuttryck (DAX), inklusive DAX-funktioner på [grundläggande DAX i Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – Lägg till anpassad kolumn](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Den mittersta rutan visar nu, de nya LatLong kolumner fylls i med värden.
    
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – anpassad LatLong kolumn](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Om du får ett fel i den nya kolumnen, se till att de tillämpade steg under frågeinställningar matchar följande bild:
    
    ![Tillämpade steg ska vara källan, navigering, expanderad dokument, expanderad Document.Location, Lägg till egen](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Om din stegen är olika, ta bort extra steg och försök lägga till anpassad kolumn igen. 

11. Klicka på **Stäng och tillämpa** att spara datamodellen.
    
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutningsapp – Stäng & tillämpa](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Skapa rapporter
Power BI Desktop rapportvyn är där du kan börja skapa rapporter för att visualisera data.  Du kan skapa rapporter genom att dra och släppa fält i den **rapporten** arbetsytor.

![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

I rapportvyn, bör du hitta:

1. Den **fält** fönstret det här är där du kan se en lista över datamodeller med fält som du kan använda för dina rapporter.
2. Den **visualiseringar** fönstret. En rapport kan innehålla en eller flera visualiseringar.  Välj visuella typer passa dina behov från den **visualiseringar** fönstret.
3. Den **rapporten** arbetsytor, det är där du skapar visuella objekt för rapporten.
4. Den **rapporten** sidan. Du kan lägga till flera rapportsidor i Power BI Desktop.

Nedan visas de grundläggande stegen för att skapa en enkel interaktiva kartan Visa rapport.

1. I vårt exempel ska vi skapa en karta vy som visar platsen för varje vulkanen.  I den **visualiseringar** klickar du på på kartan visualiseringstyp som markerats i skärmbilden ovan.  Du bör se den visual schematyp ritats på den **rapporten** arbetsytor.  Den **visualisering** fönstret visas också en uppsättning egenskaper som rör visual schematyp.
2. Nu kan dra och släpp LatLong fält från den **fält** fönstret för att den **plats** -egenskapen i **visualiseringar** fönstret.
3. Sedan dra och släpp vulkanen namnfältet för den **förklaring** egenskapen.  
4. Dra och släpp fältet höjning för att sedan den **storlek** egenskapen.  
5. Du bör nu se kartan visual som visar en uppsättning bubblor som anger platsen för varje vulkanen med storleken på bubblan korrelera till vulkanen utökas.
6. Du har nu skapat en grundläggande rapport.  Du kan ytterligare anpassa rapporten genom att lägga till fler visualiseringar.  I vårt fall vi har lagt till ett vulkanen typ utsnitt för att göra rapporten interaktiva.  
   
    ![Skärmbild av slutgiltiga Power BI Desktop vid slutförandet av Power BI-självstudiekursen för Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. Klicka på Arkiv-menyn **spara** och spara filen som PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publicera och dela din rapport
Om du vill dela din rapport, måste du ha ett konto på PowerBI.com.

1. I Power BI Desktop klickar du på den **Start** menyfliksområdet.
2. Klicka på **Publicera**.  Du uppmanas att ange användarnamn och lösenord för ditt konto på PowerBI.com.
3. När autentiseringsuppgifterna har autentiserats, publiceras rapporten till ditt mål som du har valt.
4. Klicka på **öppna 'PowerBITutorial.pbix' i Power BI** att visa och dela din rapport på PowerBI.com.
   
    ![Publicera till Power BI lyckades! Öppna självstudiekursen i Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Skapa en instrumentpanel i PowerBI.com
Nu när du har en rapport kan du dela den på PowerBI.com

När du publicerar rapporten från Power BI Desktop till PowerBI.com genereras en **rapporten** och en **datauppsättning** i PowerBI.com-klienten. Till exempel när du har publicerat en rapport som heter **PowerBITutorial** på PowerBI.com, visas PowerBITutorial i både den **rapporter** och **datauppsättningar** avsnitten på PowerBI.com.

   ![Skärmbild av den nya rapporten och datauppsättningen i PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Om du vill skapa en delbara instrumentpanel, klickar du på den **Fäst Live-sida** knappen på PowerBI.com rapporten.

   ![Skärmbild av den nya rapporten och datauppsättningen i PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Följ sedan instruktionerna i [fästa en panel från en rapport](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) att skapa en ny instrumentpanel. 

Du kan också göra ad hoc-ändringar i rapporten innan du skapar en instrumentpanel. Det rekommenderas dock att du använder Power BI Desktop för att utföra ändringar och publicera rapporten till PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Nästa steg
* Läs mer om Power BI i [Kom igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Läs mer om Azure Cosmos DB i den [dokumentations-landningssidan för Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

