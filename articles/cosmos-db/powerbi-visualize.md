---
title: Power BI-självstudiekurs för Azure Cosmos DB-anslutningsapp
description: Använd den här Power BI-självstudiekursen för att importera JSON, skapa insiktsfulla rapporter och visualisera data med Azure Cosmos DB och Power BI-kopplingen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: sngun
ms.openlocfilehash: f6ba66aa37b4a1902f98d2a1fcf5f542fa6476d7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043659"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualisera data med Azure Cosmos DB med hjälp av anslutningsprogrammet för Power BI

[Power BI](https://powerbi.microsoft.com/) är en onlinetjänst där du kan skapa och dela instrumentpaneler och rapporter. Power BI Desktop är ett verktyg som gör att du kan hämta data från olika datakällor för Rapportredigering. Azure Cosmos DB är en av den datakälla som du kan använda med Power BI Desktop. Du kan ansluta Power BI Desktop till Azure Cosmos DB-konto med Azure Cosmos DB-anslutningsprogrammet för Power BI.  När du har importerat Azure Cosmos DB-data till Power BI kan du omvandla dem, skapa rapporter och publicera rapporterna till Power BI.   

Den här artikeln beskriver de steg som krävs för att ansluta Azure Cosmos DB-konto till Power BI Desktop. När du anslutit du navigera till en samling, extrahera data, transformera JSON-data i tabellformat och publicera en rapport till Power BI.

> [!NOTE]
> Anslutningsprogrammet för Power BI för Azure Cosmos DB ansluter till Power BI Desktop. Rapporter som skapats i Power BI Desktop kan publiceras till PowerBI.com. Kan inte utföras direkt extrahering av Azure Cosmos DB-data från PowerBI.com. 

> [!NOTE]
> Ansluta till Azure Cosmos DB med anslutningsprogrammet för Power BI stöds för närvarande för Azure Cosmos DB SQL API och Gremlin-API-konton.

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har åtkomst till följande resurser innan du följer anvisningarna i den här självstudien för Power BI:

* [Hämta den senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Ladda ned den [vulkanen exempeldata](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) från GitHub.

* [Skapa ett Azure Cosmos DB-databaskonto](https://azure.microsoft.com/documentation/articles/create-account/) och importera vulkanen-data med hjälp av den [Migreringsverktyget för Azure Cosmos DB data](import-data.md). När du importerar data, Tänk på följande inställningar för källa och mål i datamigreringsverktyget:

   * **Källparametrar** 

       * **Importera från:** JSON-filerna

   * **Målparametrar** 

      * **Anslutningssträng:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partitionsnyckel:**  /land 

      * **Samlingsdataflödet:** 1000 

Du måste ha ett konto på PowerBI.com om du vill dela dina rapporter på PowerBI.com.  Läs mer om Power BI och Power BI Pro i [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Nu börjar vi
I de här självstudierna Låt oss anta att du är en geologist studerat snö runt om i världen. Vulkanen data lagras i ett Azure Cosmos DB-konto och format för JSON-dokumentet är följande:

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

Du hämtar vulkanen data från Azure Cosmos DB-kontot och visualisera data i en interaktiv Power BI-rapport.

1. Kör Power BI Desktop.

2. Du kan **hämta Data**, se **senast använda källor**, eller **öppna andra rapporter** direkt från välkomstskärmen. Välj ”X” i det övre högra hörnet att stänga av skärmen. Den **rapporten** av Power BI Desktop visas.
   
   ![Power BI Desktop rapportvyn - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Välj den **Start** menyfliksområdet och sedan på **hämta Data**.  Den **hämta Data** fönster visas.

4. Klicka på **Azure**väljer **Azure Cosmos DB (Beta)**, och klicka sedan på **Connect**. 

    ![Power BI Desktop hämta Data - anslutningsprogrammet för Powerbi](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. På den **Förhandsgranska Anslutningsapp** klickar du på **Fortsätt**. Den **Azure Cosmos DB** fönster visas.

6. Ange Azure Cosmos DB-konto slutpunkts-URL du vill hämta data från enligt nedan och klicka sedan på **OK**. Om du vill använda ditt eget konto, du kan hämta URL: en URI som visas i den **nycklar** -bladet i Azure-portalen. Du kan också ange databasnamn samlingsnamnet eller använda Navigatören för att välja den databasen och samlingen att identifiera varifrån data kommer från.
   
7. Om du ansluter till den här slutpunkten för första gången uppmanas du kontonyckeln. För ditt eget konto kan du hämta nyckeln från den **primärnyckel** rutan den **skrivskyddade nycklar** -bladet i Azure-portalen. Ange rätt nyckel och klickar sedan på **Connect**.
   
   Vi rekommenderar att du använder den skrivskyddade nyckeln när du skapar rapporter. Detta förhindrar onödig exponering av huvudnyckeln för potentiella säkerhetsrisker. Skrivskyddade nyckeln är tillgänglig från den **nycklar** -bladet i Azure-portalen. 
    
8. När kontot har anslutit den **Navigator** visas fönstret. Den **Navigator** visar en lista över databaser under kontot.

9. Klicka på och expandera på databasen där data för rapporten kommer från, välja **volcanodb** (databasnamnet kan vara annorlunda).   

10. Nu väljer en samling som innehåller data om du vill hämta väljer **volcano1** (samlingens namn kan vara annorlunda).
    
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

