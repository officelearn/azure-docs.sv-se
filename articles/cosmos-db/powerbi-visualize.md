---
title: Power BI-självstudiekurs för Azure Cosmos DB-anslutning
description: Använd den här Power BI-självstudien för att importera JSON, skapa insiktsfulla rapporter och visualisera data med Azure Cosmos DB- och Power BI-anslutningsappen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616814"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualisera Azure Cosmos DB-data med hjälp av anslutningsprogrammet för Power BI

[Power BI](https://powerbi.microsoft.com/) är en onlinetjänst där du kan skapa och dela instrumentpaneler och rapporter. Power BI Desktop är ett verktyg för rapportredigering som gör att du kan hämta data från olika datakällor. Azure Cosmos DB är en av de datakällor som du kan använda med Power BI Desktop. Du kan ansluta Power BI Desktop till ett Azure Cosmos DB-konto med Azure Cosmos DB-anslutningsprogrammet för Power BI.  När du har importerat Azure Cosmos DB-data till Power BI kan du omvandla dem, skapa rapporter och publicera rapporterna till Power BI.   

Den här artikeln beskriver de steg som krävs för att ansluta ett Azure Cosmos DB-konto till Power BI Desktop. När du har anslutit navigerar du till en samling, extraherar data, omvandlar JSON-data till tabellformat och publicerar en rapport till Power BI.

> [!NOTE]
> Power BI-kontakten för Azure Cosmos DB ansluter till Power BI Desktop. Rapporter som skapats i Power BI Desktop kan publiceras på PowerBI.com. Direkt extrahering av Azure Cosmos DB-data kan inte utföras från PowerBI.com. 

> [!NOTE]
> Ansluta till Azure Cosmos DB med Power BI-anslutningen stöds för närvarande endast för Azure Cosmos DB SQL API- och Gremlin API-konton.

## <a name="prerequisites"></a>Krav
Innan du följer instruktionerna i den här Power BI-självstudien bör du se till att du har tillgång till följande resurser:

* [Ladda ned den senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Ladda ner [exempelvulkansdata](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) från GitHub.

* [Skapa ett Azure Cosmos-databaskonto](https://azure.microsoft.com/documentation/articles/create-account/) och importera vulkandata med hjälp av [Azure Cosmos DB-datamigreringsverktyget](import-data.md). När du importerar data bör du tänka på följande inställningar för källan och destinationerna i datamigreringsverktyget:

   * **Källparametrar** 

       * **Importera från:** JSON-filer

   * **Målparametrar** 

      * **Anslutningssträng:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partitionsnyckel:** /Land 

      * **Inkasso:** 1000 

Om du vill dela dina rapporter i PowerBI.com måste du ha ett konto i PowerBI.com.  Mer information om Power BI och [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)Power BI Pro finns i .

## <a name="lets-get-started"></a>Låt oss komma igång
I den här guiden, låt oss föreställa oss att du är en geolog studerar vulkaner runt om i världen. Vulkandata lagras i ett Azure Cosmos DB-konto och JSON-dokumentformatet är följande:

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

Du hämtar vulkandata från Azure Cosmos DB-kontot och visualiserar data i en interaktiv Power BI-rapport.

1. Kör Power BI-skrivbordet.

2. Du kan **hämta data,** se **Senaste källor**eller **Öppna andra rapporter** direkt från välkomstskärmen. Välj "X" längst upp till höger för att stänga skärmen. **Rapportvyn för** Power BI Desktop visas.
   
   ![Power BI-skrivbordsrapportvy – Power BI-anslutning](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Markera menyfliksområdet **Start** och klicka sedan på **Hämta data**.  Fönstret **Hämta data** ska visas.

4. Klicka på **Azure**, välj **Azure Cosmos DB (Beta)** och klicka sedan på **Anslut**. 

    ![Power BI-skrivbord hämta data – Power BI-anslutning](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Klicka på **Fortsätt**på sidan **Förhandsgranska koppling.** **Azure Cosmos DB-fönstret** visas.

6. Ange url:en för azure cosmos DB-kontoslutpunkt som du vill hämta data från enligt nedan och klicka sedan på **OK**. Om du vill använda ditt eget konto kan du hämta URL:en från URI-rutan i **bladet Nycklar** på Azure-portalen. Du kan också ange databasnamn, samlingsnamn eller använda navigatorn för att välja databas och samling för att identifiera varifrån data kommer.
   
7. Om du ansluter till den här slutpunkten för första gången uppmanas du att ange kontonyckeln. För ditt eget konto hämtar du nyckeln från rutan **Primärnyckel** i bladet **Skrivskyddade nycklar** på Azure-portalen. Ange lämplig nyckel och klicka sedan på **Anslut**.
   
   Vi rekommenderar att du använder skrivskyddad tangent när du skapar rapporter. Detta förhindrar onödig exponering av huvudnyckeln för potentiella säkerhetsrisker. Skrivskyddad nyckel är tillgänglig från **bladet Nycklar** i Azure-portalen. 
    
8. När kontot har anslutits visas **fönstret Navigator.** **Navigator visar** en lista över databaser under kontot.

9. Klicka och expandera i databasen där data för rapporten kommer ifrån, välj **volcanodb** (ditt databasnamn kan vara annorlunda).   

10. Välj nu en samling som innehåller data som ska hämtas, välj **volcano1** (ditt samlingsnamn kan vara annorlunda).
    
    I fönstret Förhandsgranska visas en lista över **postobjekt.**  Ett dokument representeras som en **posttyp** i Power BI. På samma sätt är ett kapslat JSON-block i ett dokument också en **post**.
    
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning - Navigator-fönster](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klicka på **Redigera** om du vill starta Frågeredigeraren i ett nytt fönster för att omvandla data.

## <a name="flattening-and-transforming-json-documents"></a>Förenkla och omvandla JSON-dokument
1. Växla till power bi-frågeredigerarens fönster, där **kolumnen Dokument** i mittenfönstret.
   ![Frågeredigeraren i Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klicka på expandern till höger i **kolumnrubriken Dokument.**  Snabbmenyn med en lista med fält visas.  Välj de fält du behöver för rapporten, till exempel Vulkannamn, Land, Region, Plats, Höjd, Typ, Status och Senaste Know Eruption. Avmarkera rutan **Använd ursprungligt kolumnnamn som prefix** och klicka sedan på **OK**.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – expandera dokument](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. I mittenfönstret visas en förhandsgranskning av resultatet med de markerade fälten.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – Förenkla resultat](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. I vårt exempel är egenskapen Plats ett GeoJSON-block i ett dokument.  Som du kan se representeras platsen som en **posttyp** i Power BI Desktop.  
5. Klicka på expandern till höger i kolumnrubriken Document.Location.  Snabbmenyn med fälten typ och koordinater visas.  Låt oss markera koordinaterfältet, se till **att använd det ursprungliga kolumnnamnet som prefix** inte är markerat och klicka på **OK**.
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – platspost](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. I mittfönstret visas nu en koordinatekolumn av **listtyp.**  Som visas i början av självstudien är GeoJSON-data i den här självstudien av punkttyp med värden för Latitud och Longitud som registrerats i koordinatermatrisen.
   
    Koordinaterna[0] representerar Longitud medan koordinaterna[1] representerar Latitude.
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – koordinaterlista](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Om du vill förenkla koordinatermatrisen skapar du en **anpassad kolumn** som heter LatLong.  Markera menyfliksområdet **Lägg till kolumn** och klicka på Anpassad **kolumn**.  Fönstret **Anpassad kolumn** visas.
8. Ange ett namn på den nya kolumnen, t.ex.
9. Ange sedan den anpassade formeln för den nya kolumnen.  I vårt exempel kommer vi att sammanfoga Latitud- och Longitudvärdena åtskilda `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`av ett kommatecken som visas nedan med hjälp av följande formel: . Klicka på **OK**.
   
    Mer information om DAX-funktioner (Data Analysis Expressions), inklusive DAX-funktioner, finns [i DAX Basics i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – Lägg till anpassad kolumn](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Nu visar mittfönstret de nya LatLong-kolumnerna som fylls med värdena.
    
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – anpassad LatLong-kolumn](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Om du får ett fel i den nya kolumnen kontrollerar du att de tillämpade stegen under Frågeinställningar matchar följande bild:
    
    ![Tillämpade steg bör vara Källa, Navigering, Utökat dokument, Utökat dokument.Plats, Added Custom](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Om stegen är olika tar du bort de extra stegen och försöker lägga till den anpassade kolumnen igen. 

11. Klicka på **Stäng och Använd** om du vill spara datamodellen.
    
    ![Power BI-självstudiekurs för Azure Cosmos DB Power BI-anslutning – Stäng & Använd](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Skapa rapporterna
I Power BI Desktop Report-vyn kan du börja skapa rapporter för att visualisera data.  Du kan skapa rapporter genom att dra och släppa fält i **rapportarbetsytan.**

![Power BI-skrivbordsrapportvy – Power BI-anslutning](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

I rapportvyn bör du hitta:

1. Fönstret **Fält,** det är här du kan se en lista över datamodeller med fält som du kan använda för dina rapporter.
2. Fönstret **Visualiseringar.** En rapport kan innehålla en eller flera visualiseringar.  Välj de visuella typer som passar dina behov i fönstret **Visualiseringar.**
3. **Rapportarbetsytan,** det är här du skapar visuella objekt för rapporten.
4. Sidan **Rapport.** Du kan lägga till flera rapportsidor i Power BI Desktop.

Följande visar de grundläggande stegen för att skapa en enkel interaktiv karta vy rapport.

1. Till vårt exempel kommer vi att skapa en kartvy som visar platsen för varje vulkan.  I fönstret **Visualiseringar** klickar du på den visuella typen Karta som markerad i skärmbilden ovan.  Du bör se den visuella **Report** typen Karta målad på rapportarbetsytan.  Fönstret **Visualisering** bör också visa en uppsättning egenskaper som är relaterade till den visuella typen Karta.
2. Dra och släpp nu fältet LatLong från fönstret **Fält** till egenskapen **Plats** i **Visualiseringar.**
3. Dra och släpp sedan fältet Vulkannamn till egenskapen **Legend.**  
4. Dra och släpp sedan fältet Höjd till egenskapen **Storlek.**  
5. Du bör nu se kartan visuella visar en uppsättning bubblor som anger platsen för varje vulkan med storleken på bubblan korrelerar till höjden av vulkanen.
6. Du har nu skapat en grundläggande rapport.  Du kan anpassa rapporten ytterligare genom att lägga till fler visualiseringar.  I vårt fall har vi lagt till en Volcano Type utsnitt för att göra rapporten interaktiv.  
   
7. Klicka på **Spara** och spara filen som PowerBITutorial.pbix på Arkiv-menyn.

## <a name="publish-and-share-your-report"></a>Publicera och dela din rapport
Om du vill dela rapporten måste du ha ett konto i PowerBI.com.

1. Klicka på **menyfliksområdet Start** i Power BI-skrivbordet.
2. Klicka på **Publicera**.  Du uppmanas att ange användarnamn och lösenord för ditt PowerBI.com konto.
3. När autentiseringsuppgifterna har autentiserats publiceras rapporten till den destination du har valt.
4. Klicka på **Öppna PowerBITutorial.pbix i Power BI** för att se och dela rapporten på PowerBI.com.
   
    ![Publicering till Power BI Framgång! Öppna självstudiekurs i Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Skapa en instrumentpanel i PowerBI.com
Nu när du har en rapport kan du dela den på PowerBI.com

När du publicerar rapporten från Power BI Desktop till PowerBI.com genererar den en **rapport** och en **datauppsättning** i din PowerBI.com-klientorganisation. När du till exempel har publicerat en rapport som heter **PowerBITutorial** till PowerBI.com visas PowerBITutorial i både **avsnitten Rapporter** och **datauppsättningar** på PowerBI.com.

   ![Skärmbild av den nya rapporten och datauppsättningen i PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Om du vill skapa en delbar instrumentpanel klickar du på knappen **Fäst livesida** i PowerBI.com rapporten.

   ![Skärmbild av den nya rapporten och datauppsättningen i PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Följ sedan instruktionerna i [Fäst en panel från en rapport](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) för att skapa en ny instrumentpanel. 

Du kan också göra ad hoc-ändringar för att rapportera innan du skapar en instrumentpanel. Vi rekommenderar dock att du använder Power BI Desktop för att utföra ändringarna och publicera om rapporten för att PowerBI.com.

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
* Mer information om Power BI finns i [Komma igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Mer information om Azure Cosmos DB finns på målsidan för [Azure Cosmos DB-dokumentation](https://azure.microsoft.com/documentation/services/cosmos-db/).

