---
title: Använd Azure Cosmos DB-ändringsflödet att visualisera analyser av realtidsdata
description: Den här artikeln beskrivs hur ändringsfeed kan användas av en retail-företag att förstå användarmönster, utföra dataanalys i realtid och visualisering.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: 379c7913f803c599865df080524da5c3fb1d0e52
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526358"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Använd Azure Cosmos DB-ändringsflödet att visualisera analyser av realtidsdata

Azure Cosmos DB-ändringsflödet är en mekanism för att hämta en kontinuerlig och inkrementella feed med poster från en Azure Cosmos DB-behållare som de posterna som ska skapas eller ändras. Ändra feed support fungerar genom att lyssna på behållare efter ändringar. Sedan returnerar den sorterade listan över dokument som har ändrats i den ordning som de har ändrats. Läs mer om ändringsfeed i [arbeta med ändringsflödet](change-feed.md) artikeln. 

Den här artikeln beskrivs hur ändringsfeed kan användas av en e-handel företag att förstå användarmönster, utföra dataanalys i realtid och visualisering. Du kan analysera händelser, t.ex en användare visa ett objekt, att lägga till ett objekt i deras kundvagn eller köpa ett objekt. När något av dessa händelser inträffar, en ny post skapas och ändringsfeed loggar som registrerar. Ändringsfeed sedan utlösare en serie steg, vilket resulterar i visualisering av mått som analyserar företagets prestanda och aktivitet. Som du kan visualisera måttexempel intäkter, unika besökare, mest populära objekt och genomsnittspriset för de objekt som visas och lagts till i kundvagnen och har köpt. De här måtten exempel kan ett e-handel företag utvärdera dess plats popularitet, utveckla dess reklam och priser strategier och fatta beslut rörande vilka lager ni ska investera i.

Intresserad av att titta på en video om lösningen innan du börjar, se följande video:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Lösningskomponenter
Följande diagram representerar dataflöde och komponenter som ingår i lösningen:

![Projektet visuellt objekt](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Generering av data:** Datasimulatorn används för att generera detaljhandelsdata som representerar händelser, t.ex en användare visa ett objekt, att lägga till ett objekt i deras kundvagn och köpa ett objekt. Du kan generera stora uppsättning exempeldata med hjälp av datageneratorn. Genererade exempeldata innehåller dokument i följande format:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Genererade data är butiker i en Azure Cosmos DB-samling.  

3. **Ändringsfeed:** Ändringsflöde att lyssna efter ändringar i Azure Cosmos DB-samlingen. Varje gång ett nytt dokument har lagts till i samlingen (som när en händelse inträffar, till exempel en användare som visar ett objekt när en artikel läggs till i sina kundvagn eller köpa ett objekt), kommer ändringsfeed utlösa en [Azure Function](../azure-functions/functions-overview.md).  

4. **Azure Function:** Azure Function bearbetar nya data och skickar den till en [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Händelsehubb:** Azure Event Hub lagrar dessa händelser och skickar dem till [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) att utföra ytterligare analyser.  

6. **Azure Stream Analytics:** Azure Stream Analytics definierar frågor för att bearbeta händelserna och utföra dataanalys i realtid. Dessa data skickas sedan till [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Powerbi används för att visualisera data som skickas av Azure Stream Analytics. Du kan skapa en instrumentpanel om du vill se hur mått som ändras i realtid.  

## <a name="prerequisites"></a>Förutsättningar

* Microsoft .NET Framework 4.7.1 eller högre

* Microsoft .NET Core 2.1 (eller senare)

* Visual Studio med Universal Windows Platform-utveckling, .NET-skrivbordsutveckling och ASP.NET och utvecklingsarbetsbelastning

* Microsoft Azure-prenumeration

* Microsoft Power BI-konto

* Ladda ned den [labb för Azure Cosmos DB-ändringsfeed](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) från GitHub. 

## <a name="create-azure-resources"></a>Skapa Azure-resurser 

Skapa Azure-resurser – Azure Cosmos DB, Storage-kontot Event Hub, Stream Analytics som krävs av lösningen. Du distribuerar dessa resurser via en Azure Resource Manager-mall. Använd följande steg för att distribuera dessa resurser: 

1. Ange körningsprincip för Windows PowerShell **Unrestricted**. Du gör detta genom att öppna **Windows PowerShell som administratör** och kör följande kommandon:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Från GitHub-lagringsplats som du hämtade i föregående steg, gå till den **Azure Resource Manager** och öppna filen heter **parameters.json** fil.  

3. Ange värden för cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parametrar som anges i **parameters.json** fil. Du måste använda de namn som du ger till var och en av dina resurser senare.  

4. Från **Windows PowerShell**, navigera till den **Azure Resource Manager** mappen och kör sedan följande kommando:

   ```powershell
   .\deploy.ps1
   ```
5. När du uppmanas, anger du din Azure **prenumerations-ID**, **changefeedlab** för resursgruppens namn och **run1** för distributionens namn. När resurserna börjar distribuera, kan det ta upp till 10 minuter att slutföra.

## <a name="create-a-database-and-the-collection"></a>Skapa en databas och samlingen

Nu ska du skapa en samling för att lagra e-handel site-händelser. När en användare visar ett objekt, lägger till ett objekt i deras kundvagn eller Köp ett objekt, samlingen får en post som innehåller åtgärden (”Visa”, ”har lagts till” eller ”köpt”), namnet på objektet som ingår, priset för objektet som är inblandade och användaren kundvagn i ID-numret nvolved.

1. Gå till [Azure-portalen](https://portal.azure.com/) och hitta den **Azure Cosmos DB-konto** som skapas av malldistributionen.  

2. Från den **Datautforskaren** väljer **ny samling** och Fyll i formuläret med följande uppgifter:  

   * För den **databas-id** väljer **Skapa nytt**, ange sedan **changefeedlabdatabase**. Lämna den **etablera databasen dataflöde** rutan avmarkerad.  
   * För den **samling** id Anger **changefeedlabcollection**.  
   * För den **partitionsnyckel** anger **/punkt**. Detta är skiftlägeskänsligt, så se till att du anger den korrekt.  
   * För den **dataflöde** anger **10000**.  
   * Välj knappen **OK**.  

3. Därefter skapar en annan samling som heter **lån** för ändringsfeed bearbetning. Lånsamlingen samordnar bearbetning ändringsflöden över flera arbetare. En separat samling används som omfattar lånen med ett lån per partition.  

4. Gå tillbaka till den **Datautforskaren** rutan och välj **ny samling** och Fyll i formuläret med följande uppgifter:

   * För den **databas-id** väljer **Använd befintlig**, ange sedan **changefeedlabdatabase**.  
   * För den **samlings-id** anger **lån**.  
   * För **lagringskapacitet**väljer **fast**.  
   * Lämna den **dataflöde** fältet inställt på standardvärdet.  
   * Välj knappen **OK**.

## <a name="get-the-connection-string-and-keys"></a>Hämta anslutningssträngen och nycklar

### <a name="get-the-azure-cosmos-db-connection-string"></a>Hämta Azure Cosmos DB-anslutningssträng

1. Gå till [Azure-portalen](https://portal.azure.com/) och hitta den **Azure Cosmos DB-konto** som skapas av malldistributionen.  

2. Navigera till den **nycklar** fönstret Kopiera primär ANSLUTNINGSSTRÄNG och kopiera den till anteckningar eller ett annat dokument att du har åtkomst till i labbet. Du bör märker den **Cosmos DB-anslutningssträng**. Du måste kopiera strängen i din kod senare, så Observera och Kom ihåg var du lagrar den.

### <a name="get-the-storage-account-key-and-connection-string"></a>Hämta storage-konto nyckel och anslutningssträngen

Azure Storage-konton kan du lagra data. I den här övningen ska du använda ett storage-konto som lagrar data som används av Azure-funktion. Azure-funktion utlöses när ändringar görs i samlingen.

1. Gå tillbaka till din resursgrupp och öppna det lagringskonto som du skapade tidigare  

2. Välj **åtkomstnycklar** på menyn till vänster.  

3. Kopiera värdena under **nyckel 1** anteckningar eller ett annat dokument att du har åtkomst till i labbet. Du bör märka den **nyckel** som **Lagringsnyckeln** och **anslutningssträngen** som **Lagringsanslutningssträng**. Du måste kopiera dessa strängar i din kod senare, så Observera och Kom ihåg var du lagrar dem.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Hämta anslutningssträngen för event hub-namnområde

En Azure-Händelsehubb tar emot händelsedata, butiker, processer och vidarebefordrar data. I den här övningen, Azure Event Hub får ett dokument varje gång en ny händelse sker (dvs. ett objekt visas av en användare, läggs till en användares kundvagn, eller som en användare) och sedan vidarebefordrar det dokumentet till Azure Stream Analytics.

1. Gå tillbaka till din resursgrupp och öppna den **Event Hub Namespace** som du skapade och med namnet i prelab.  

2. Välj **principer för delad åtkomst** på menyn till vänster.  

3. Välj **RootManageSharedAccessKey**. Kopiera den **anslutningssträng – primär nyckel** anteckningar eller ett annat dokument att du har åtkomst till i labbet. Du bör märker den **Event Hub Namespace** anslutningssträngen. Du måste kopiera strängen i din kod senare, så Observera och Kom ihåg var du lagrar den.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Konfigurera Azure Function för att läsa ändringsflöde

När ett nytt dokument har skapats, eller ett aktuella dokument ändras i en Cosmos DB-samling, ändringsflödet automatiskt lägger till det ändrade dokumentet dess historik över samlingsändringar. Du kan nu skapa och kör en Azure-funktion som bearbetar den ändringsflödet. När ett dokument skapas eller ändras i samlingen som du skapade, utlöses Azure Function ändringen feed. Azure Function skickar sedan det ändrade dokumentet till Händelsehubben.

1. Gå tillbaka till den lagringsplats du klonade på din enhet.  

2. Högerklicka på filen som heter **ChangeFeedLabSolution.sln** och välj **öppna med Visual Studio**.  

3. Gå till **local.settings.json** i Visual Studio. Sedan Använd de värden du antecknade tidigare för att fylla i tomma värden.  

4. Gå till **ChangeFeedProcessor.cs**. I parametrar för den **kör** fungera, utföra följande åtgärder:  

   * Ersätt texten **din SAMLING namn här** med namnet på din samling. Om du följt tidigare anvisningar, är namnet på din samling changefeedlabcollection.  
   * Ersätt texten **YOUR lån SAMLINGEN namnet här** med namnet på din lånsamlingen. Om du följt tidigare anvisningar, namnet på din lånsamlingen är **lån**.  
   * Kontrollera att att Startprojekt rutan till vänster på den gröna pilen står överst i Visual Studio, **ChangeFeedFunction**.  
   * Välj **starta** överst på sidan för att köra programmet  
   * Du kan bekräfta att funktionen körs när konsolappen säger ”jobbet värden startade”.

## <a name="insert-data-into-azure-cosmos-db"></a>Infoga data i Azure Cosmos DB 

Se hur ändringsfeed bearbetar nya åtgärder på en e-handelswebbplats, har du simulerar data som representerar användare visa objekt från produktkatalogen, att lägga till objekten i sina kundvagnar och köpa objekten i sina kundvagnar. Dessa data är valfri och i syfte att replikera vilka data på ett e-handel plats skulle se ut.

1. Gå tillbaka till lagringsplatsen i Utforskaren och högerklicka på **ChangeFeedFunction.sln** att öppna den i ett nytt Visual Studio-fönster.  

2. Navigera till den **App.config** fil. I den `<appSettings>` blockera, lägga till slutpunkten och unikt **PRIMÄRNYCKEL** som Azure Cosmos DB-kontot som du hämtade tidigare.  

3. Lägg till i den **samling** och **databasen** namn. (Dessa namn bör vara **changefeedlabcollection** och **changefeedlabdatabase** om du inte väljer att namnge din på olika sätt.)

   ![Uppdatera anslutningssträngar](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Spara ändringarna på alla filer som redigeras.  

5. Högst upp i Visual Studio, se till att den **Startprojekt** står i rutan till vänster på den gröna pilen **DataGenerator**. Välj sedan **starta** överst på sidan för att köra programmet.  
 
6. Vänta tills programmet som ska köras. Stjärnorna betyder att data kommer! Kör du programmet – det är viktigt att stora mängder data som samlas in.  

7. Om du navigerar till [Azure-portalen](https://portal.azure.com/) , sedan till Cosmos DB-konto i resursgruppen, sedan till **Datautforskaren**, visas slumpmässig importerade data i din  **changefeedlabcollection** .
 
   ![Data som genereras i portalen](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Konfigurera ett stream analytics-jobb

Azure Stream Analytics är en fullständigt hanterad molntjänst för bearbetning i realtid för strömmande data. I den här övningen kommer du använder stream analytics för att bearbeta nya händelser från Event Hub (d.v.s. när ett objekt visas, lagts till i en kundvagn eller köpt), införliva dessa händelser i dataanalys i realtid och skicka dem till Power BI för visualisering.

1. Från den [Azure-portalen](https://portal.azure.com/), navigera till resursgruppen, sedan till **streamjob1** (stream analytics-jobbet som du skapade i prelab).  

2. Välj **indata** som visas nedan.  

   ![Skapa indata](./media/changefeed-ecommerce-solution/create-input.png)

3. Välj **+ Lägg till strömindata**. Välj sedan **Event Hub** från den nedrullningsbara menyn.  

4. Fyll i nya inkommande formuläret med följande information:

   * I den **inkommande** alias, ange **inkommande**.  
   * Välj alternativet för **Välj Event Hub från dina prenumerationer**.  
   * Ange den **prenumeration** fältet till din prenumeration.  
   * I den **händelsehubbsnamnområdet** fältet, anger du namnet på din Event Hub-Namespace som du skapade under prelab.  
   * I den **händelsehubbnamn** väljer du alternativet för **Använd befintlig** och välj **händelse hub1** från den nedrullningsbara menyn.  
   * Lämna **Event Hub-principen** namnfältet inställd på standardvärdet.  
   * Lämna **händelseserialiseringsformat** som **JSON**.  
   * Lämna **fältet avkodning** inställd **UTF-8**.  
   * Lämna **komprimeringstyp för händelsen** fältet inställt på **ingen**.  
   * Välj knappen **Spara**.

5. Gå tillbaka till sidan för stream analytics-jobb och välj **utdata**.  

6. Välj **+ Lägg till**. Välj sedan **Power BI** från den nedrullningsbara menyn.  

7. För att skapa en ny Power BI-utdata om du vill visualisera genomsnittspriset, utför du följande åtgärder:

   * I den **utdataaliaset** anger **averagePriceOutput**.  
   * Lämna den **grupparbetsyta** fältet inställt på **auktorisera anslutningen att läsa in arbetsytorna**.  
   * I den **Datamängdsnamn** anger **averagePrice**.  
   * I den **tabellnamn** anger **averagePrice**.  
   * Välj den **auktorisera** knappen och följ anvisningarna för att auktorisera anslutningen till Power BI.  
   * Välj knappen **Spara**.  

8. Gå sedan tillbaka till **streamjob1** och välj **redigera frågan**.

   ![Redigera frågan](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Klistra in följande fråga i frågefönstret. Den **GENOMSNITTSPRISET** frågan beräknar genomsnittspriset för alla objekt som visas av användare, det genomsnittliga priset för alla objekt som läggs till användarnas kundvagnar och genomsnittspriset för alla objekt som köps av användare. Det här måttet kan hjälpa företag att e-handel bestämma vilka priser att sälja objekt på och vad inventering för att investera i. Till exempel om det genomsnittliga priset för objekt som visas är mycket högre än det genomsnittliga priset för objekt som har köpt, kan sedan ett företag välja att lägga till billigare objekt i dess inventering.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Välj sedan **spara** i det övre vänstra hörnet.  

11. Gå nu tillbaka till **streamjob1** och välj den **starta** längst upp på sidan. Azure Stream Analytics kan ta några minuter att starta, men till slut visas den ändras från ”från” till ”körs”.

## <a name="connect-to-power-bi"></a>Anslut till Powerbi

Powerbi är en uppsättning verktyg för Företagsanalys att analysera data och dela insikter. Det är ett bra exempel på hur du strategiskt visualisera analyserade data.

1. Logga in på Power BI och gå till **Min arbetsyta** genom att öppna menyn till vänster på sidan.  

2. Välj **+ skapa** i övre högra hörnet och välj sedan **instrumentpanelen** du skapar en instrumentpanel.  

3. Välj **+ Lägg till panel** i det övre högra hörnet.  

4. Välj **anpassade strömmande Data**och välj sedan den **nästa** knappen.  
 
5. Välj **averagePrice** från **YOUR DATAUPPSÄTTNINGAR**och välj sedan **nästa**.  

6. I den **Visualiseringstyp** , Välj **klustrade stapeldiagram** från den nedrullningsbara menyn. Under **axel**, Lägg till åtgärd. Hoppa över **förklaring** utan att lägga till något. Sedan, under nästa avsnitt anropas **värdet**, lägga till **genomsnittlig**. Välj **nästa**rubrik diagrammet och välj **tillämpa**. Du bör se ett nytt diagram på instrumentpanelen!  

7. Nu, om du vill att visualisera mått, går du tillbaka till **streamjob1** och skapa tre mer utdata i följande fält.

   a. **Utdataalias:** incomingRevenueOutput, namn på datauppsättning: incomingRevenue tabellnamn: incomingRevenue  
   b. **Utdataalias:** top5Output, namn på datauppsättning: top5 tabellnamn: top5  
   c. **Utdataalias:** uniqueVisitorCountOutput, namn på datauppsättning: uniqueVisitorCount tabellnamn: uniqueVisitorCount

   Välj sedan **redigera frågan** och klistra in följande frågor **ovan** som du redan har skapat.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   5 FRÄMSTA frågan beräknar de översta 5 objekten, rankade efter antal gånger som de har köpts. Det här måttet kan e-handel företag utvärdera vilka objekt som är mest populära och de kan påverka företagets reklam, prissättning, och inventera beslut.

   INTÄKTER frågan omsättningen genom att addera priserna för alla objekt som har köpt varje minut. Det här måttet kan hjälpa företag att e-handel utvärdera dess resultat och också förstå vad tidpunkter på dagen bidra till de flesta intäkter. Detta kan påverka övergripande företagets strategi, i synnerhet marknadsföring.

   UNIKA BESÖKARE frågan beräknar hur många unika besökare finns på webbplatsen var femte sekund genom att upptäcka unika kundvagn-ID: n Det här måttet kan hjälpa företag att e-handel utvärdera deras webbplatsaktivitet och utarbeta en strategi för hur du skaffa fler kunder.

8. Du kan nu lägga till paneler för dessa datauppsättningar samt.

   * För 5 främsta, skulle det vara klokt att göra en grupperat stående stapeldiagram med objekt som axel och antal som värde.  
   * För intäkter, skulle det vara klokt att göra ett linjediagram med tiden som axeln och summan av priserna som värde. Tidsfönster att visa bör vara det största möjligt för att kunna leverera så mycket information som möjligt.  
   * Det skulle vara klokt att göra en kortvisualisering med antalet unika besökare som värde för unika besökare.

   Det här är utseendet för ett exempel på en instrumentpanel med dessa diagram:

   ![visualiseringar](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Valfritt: Visualisera med en E-handelswebbplats

Du kommer nu att se hur du kan använda din nya Analysverktyget för att ansluta till en plats för verkliga e-handel. För att skapa e-handelswebbplats, använder du en Azure Cosmos DB-databas för att lagra listan över produktkategorier (kvinnor, män, Unisex), produktkatalogen och en lista över de mest populära.

1. Gå tillbaka till den [Azure-portalen](https://portal.azure.com/), sedan till din **Cosmos DB-konto**, sedan till **Datautforskaren**.  

   Lägg till två samlingar under **changefeedlabdatabase** - **produkter** och **kategorier** med fast lagringskapacitet.

   Lägg till en annan samling under **changefeedlabdatabase** med namnet **topItems** och **/punkt** som partitionsnyckel.

2. Välj den **topItems** samling, och under **skala och inställningar** ange den **Time to Live** vara **30 sekunder** så att topItems uppdateras med 30 sekunders mellanrum.

   ![Time to live](./media/changefeed-ecommerce-solution/time-to-live.png)

3. För att fylla i den **topItems** samlingen med de mest inköpta artiklar, gå tillbaka till **streamjob1** och lägga till en ny **utdata**. Välj **Cosmos DB**.

4. Fyll i de obligatoriska fälten som i bilden nedan.

   ![Cosmos-utdata](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Om du har lagt till valfri 5 FRÄMSTA fråga i den förra delen av övningen fortsätter du till en del 5a. Annars kan du gå vidare till del 5b.

   5a. I **streamjob1**väljer **redigera frågan** och klistra in följande fråga i din Azure Stream Analytics-frågeredigeraren under 5 FRÄMSTA frågan men ovan resten av frågorna.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. I **streamjob1**väljer **redigera frågan** och klistra in följande fråga i din Azure Stream Analytics frågeredigeraren ovanför alla andra frågor.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Öppna **EcommerceWebApp.sln** och navigera till den **Web.config** fil i den **Solution Explorer**.  

7. I den `<appSettings>` blockera, lägga till den **URI** och **PRIMÄRNYCKEL** som du sparade tidigare står det **din URI här** och **den primära nyckeln**. Lägg sedan till i din **databasnamn** och **samlingsnamn** som anges. (Dessa namn bör vara **changefeedlabdatabase** och **changefeedlabcollection** om du har angett att namnge din på olika sätt.)

   Fyll i din **produkter samlingsnamn**, **kategorier samlingsnamn**, och **de översta objekten samlingsnamn** som anges. (Dessa namn bör vara **produkter, kategorier och topItems** om du har angett att namnge din på olika sätt.)  

8. Navigera till och öppna den **checka ut mappen** inom **EcommerceWebApp.sln.** Öppna sedan den **Web.config** fil i mappen.  

9. I den `<appSettings>` blockera, lägga till den **URI** och **PRIMÄRNYCKEL** som du sparade tidigare där det anges. Lägg sedan till i din **databasnamn** och **samlingsnamn** som anges. (Dessa namn bör vara **changefeedlabdatabase** och **changefeedlabcollection** om du har angett att namnge din på olika sätt.)  

10. Tryck på **starta** överst på sidan för att köra programmet.  

11. Nu kan du experimentera på webbplatsen e-handel. När du visar ett objekt, lägga till ett objekt i kundvagnen, ändra antalet för ett objekt i kundvagnen eller köpa en artikel dessa händelser skickas via Cosmos-DB-ändringsflödet till Event Hub, ASA och sedan Power BI. Vi rekommenderar fortsätter att köra DataGenerator för att generera betydande web trafikdata och ger en realistisk uppsättning med ”frekvent produkter” på webbplatsen e-handel.

## <a name="delete-the-resources"></a>Ta bort resurser

Om du vill ta bort alla resurser som du skapade under den här övningen, navigera till resursgruppen på [Azure-portalen](https://portal.azure.com/)och välj sedan **ta bort resursgrupp** på menyn längst upp på sidan och följa anvisningarna tillhandahålla.

## <a name="next-steps"></a>Nästa steg 
  
* Läs mer om ändringsfeed i [arbeta med ändringen feed support i Azure Cosmos DB](change-feed.md) 
* [Meddelande-lösningen för ändringsfeed](change-feed-hl7-fhir-logic-apps.md) för inom organisationen med hjälp av Azure Cosmos DB.
