---
title: Använda Azure Cosmos DB-ändringsflöde för att visualisera dataanalys i realtid
description: I den här artikeln beskrivs hur ändringsflöde kan användas av ett detaljhandelsföretag för att förstå användarmönster, utföra dataanalys och visualisering i realtid
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: c0c1a28dc399d3f176f92e656621fec1bc92dbfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513517"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Använda Azure Cosmos DB-ändringsflöde för att visualisera dataanalys i realtid

Azure Cosmos DB-ändringsfeeden är en mekanism för att få en kontinuerlig och inkrementell feed av poster från en Azure Cosmos-behållare när dessa poster skapas eller ändras. Ändra feedstöd fungerar genom att lyssna på behållaren för eventuella ändringar. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Mer information om ändringsflödet finns i [Arbeta med ändringsflödesartikel.](change-feed.md) 

I den här artikeln beskrivs hur ändringsflödet kan användas av ett e-handelsföretag för att förstå användarmönster, utföra dataanalys och visualisering i realtid. Du ska analysera händelser som en användare som visar ett objekt, lägga till en artikel i kundvagnen eller köpa en artikel. När en av dessa händelser inträffar skapas en ny post och ändringsfeedloggarna som postar. Ändra feed utlöser sedan en serie steg som resulterar i visualisering av mått som analyserar företagets prestanda och aktivitet. Exempelmått som du kan visualisera inkluderar intäkter, unika besökare, mest populära objekt och genomsnittspriset för de artiklar som visas jämfört med läggas till i en kundvagn jämfört med köpt. Dessa exempel mått kan hjälpa ett e-handelsföretag utvärdera sin webbplats popularitet, utveckla sin reklam och prisstrategier, och fatta beslut om vad lagret att investera i.

Du är intresserad av att titta på en video om lösningen innan du börjar, se följande video:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Lösningskomponenter
Följande diagram representerar dataflödet och komponenterna som ingår i lösningen:

![Visuellt projekt](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Datagenerering:** Datasimulator används för att generera butiksdata som representerar händelser som en användare som visar ett objekt, lägger till en artikel i kundvagnen och köper en artikel. Du kan generera stora uppsättning exempeldata med hjälp av datageneratorn. De genererade exempeldata innehåller dokument i följande format:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Kosmos DB:** Genererade data lagras i en Azure Cosmos-behållare.  

3. **Ändra flöde:** Ändringsflödet lyssnar efter ändringar i Azure Cosmos-behållaren. Varje gång ett nytt dokument läggs till i samlingen (det vill en händelse inträffar som en sådan användare som visar ett objekt, lägger till ett objekt i kundvagnen eller köper ett objekt) utlöser ändringsflödet en [Azure-funktion](../azure-functions/functions-overview.md).  

4. **Azure-funktion:** Azure-funktionen bearbetar de nya data och skickar den till en [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Händelsehubb:** Azure Event Hub lagrar dessa händelser och skickar dem till [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) för att utföra ytterligare analyser.  

6. **Azure Stream-analys:** Azure Stream Analytics definierar frågor för att bearbeta händelserna och utföra dataanalys i realtid. Dessa data skickas sedan till [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI används för att visualisera data som skickas av Azure Stream Analytics. Du kan skapa en instrumentpanel för att se hur måtten ändras i realtid.  

## <a name="prerequisites"></a>Krav

* Microsoft .NET Framework 4.7.1 eller senare

* Microsoft .NET Core 2.1 (eller senare)

* Visual Studio med universell Windows-plattformsutveckling, .NET-skrivbordsutveckling och ASP.NET- och webbutvecklingsarbetsbelastningar

* Microsoft Azure-prenumeration

* Microsoft Power BI-konto

* Hämta [azure Cosmos DB-ändringsfeedlabbet](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) från GitHub. 

## <a name="create-azure-resources"></a>Skapa Azure-resurser 

Skapa Azure-resurser - Azure Cosmos DB, Storage-konto, Event Hub, Stream Analytics som krävs av lösningen. Du kommer att distribuera dessa resurser via en Azure Resource Manager-mall. Så här distribuerar du följande resurser: 

1. Ange windows powershell-körningsprincipen till **Obegränsad**. Det gör du genom att öppna **Windows PowerShell som administratör** och köra följande kommandon:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Från GitHub-databasen som du hämtade i föregående steg navigerar du till mappen **Azure Resource Manager** och öppnar filen **parameters.json-filen.**  

3. Ange värden för cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parametrar som anges i **filen parameters.json.** Du måste använda de namn som du ger till var och en av dina resurser senare.  

4. Från **Windows PowerShell**navigerar du till mappen **Azure Resource Manager** och kör följande kommando:

   ```powershell
   .\deploy.ps1
   ```
5. När du uppmanas till det anger du ditt **Azure-prenumerations-ID,** **ändrarfeedlab** för resursgruppsnamnet och **kör1** för distributionsnamnet. När resurserna börjar distribuera kan det ta upp till 10 minuter innan den har slutförts.

## <a name="create-a-database-and-the-collection"></a>Skapa en databas och samlingen

Du kommer nu att skapa en samling för att hålla e-handelsplats händelser. När en användare visar ett objekt, lägger till en artikel i sin kundvagn eller köper en artikel, får samlingen en post som innehåller åtgärden ("visad", "tillagd" eller "köpt"), namnet på den berörda artikeln, priset på den berörda artikeln och ID-numret för användarvagnen Inblandade.

1. Gå till [Azure-portalen](https://portal.azure.com/) och hitta **Azure Cosmos DB-konto** som skapas av malldistributionen.  

2. Välj **Ny samling** i fönstret **Datautforskare** och fyll i formuläret med följande information:  

   * För fältet **Databas-ID** väljer du **Skapa ny**och anger sedan **changefeedlabdatabase**. Låt rutan **Etablera databasdataflöde** avmarkerat.  
   * För **Collection** fältet Samlings-ID anger du **changefeedlabcollection**.  
   * För fältet **Partitionsnyckel** anger du **/Item**. Detta är skiftlägeskänsligt, så se till att du anger det korrekt.  
   * För **fältet Dataflöde** anger du **10000**.  
   * Välj knappen **OK**.  

3. Skapa sedan en annan samling med namnet **lån** för ändringsflödesbearbetning. Lånesamlingen koordinerar bearbetningen av ändringsflödet för flera arbetare. En separat samling används för att lagra lånen med ett lån per partition.  

4. Gå tillbaka till **fönstret Data explorer** och välj Ny **samling** och fyll i formuläret med följande information:

   * För fältet **Databas-ID** väljer du **Använd befintlig**och anger sedan **changefeedlabdatabase**.  
   * Ange lån för fältet **leases** **Samlings-ID** .  
   * För **lagringskapacitet**väljer du **Fast**.  
   * Lämna **fältet Dataflöde** inställt på standardvärdet.  
   * Välj knappen **OK**.

## <a name="get-the-connection-string-and-keys"></a>Hämta anslutningssträngen och knapparna

### <a name="get-the-azure-cosmos-db-connection-string"></a>Hämta Azure Cosmos DB-anslutningssträngen

1. Gå till [Azure-portalen](https://portal.azure.com/) och hitta **Azure Cosmos DB-konto** som skapas av malldistributionen.  

2. Navigera till **fönstret Nycklar,** kopiera den primära anslutningssträngen och kopiera den till ett anteckningsblock eller ett annat dokument som du har tillgång till i hela labbet. Du bör märka det **Cosmos DB Connection String**. Du måste kopiera strängen till koden senare, så notera var du lagrar den.

### <a name="get-the-storage-account-key-and-connection-string"></a>Hämta nyckeln och anslutningssträngen för lagringskonto

Azure Storage-konton tillåter användare att lagra data. I det här labbet använder du ett lagringskonto för att lagra data som används av Azure-funktionen. Azure-funktionen utlöses när ändringar görs i samlingen.

1. Gå tillbaka till resursgruppen och öppna lagringskontot som du skapade tidigare  

2. Välj **Access-tangenter** på menyn till vänster.  

3. Kopiera värdena under **knapp 1** till ett anteckningsblock eller ett annat dokument som du har tillgång till i hela labbet. Du bör märka **nyckeln** som **lagringsnyckel** och **anslutningssträngen** som **lagringsanslutningssträng**. Du måste kopiera dessa strängar till din kod senare, så ta en anteckning och kom ihåg var du lagrar dem.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Hämta anslutningssträngen för händelsehubbens namnområde

En Azure Event Hub tar emot händelsedata, lagrar, processer och vidarebefordrar data. I det här labbet får Azure Event Hub ett dokument varje gång en ny händelse inträffar (d.v.s. ett objekt visas av en användare, läggs till i en användares kundvagn eller köps av en användare) och vidarebefordrar sedan dokumentet till Azure Stream Analytics.

1. Gå tillbaka till resursgruppen och öppna **namnområdet för händelsehubben** som du skapade och namngav i förlab.  

2. Välj **Principer för delad åtkomst** på menyn till vänster.  

3. Välj **RootManageSharedAccessKey**. Kopiera **anslutningssträngens primärnyckel** till ett anteckningsblock eller ett annat dokument som du har tillgång till i hela labbet. Du bör märka den till anslutningssträngen **För händelsehubbens namnområde.** Du måste kopiera strängen till koden senare, så notera var du lagrar den.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Konfigurera Azure-funktion för att läsa ändringsflödet

När ett nytt dokument skapas eller ett aktuellt dokument ändras i en Cosmos-behållare, lägger ändringsflödet automatiskt till det ändrade dokumentet i historiken över insamlingsändringar. Du kommer nu att skapa och köra en Azure-funktion som bearbetar ändringsflödet. När ett dokument skapas eller ändras i samlingen som du skapade utlöses Azure-funktionen av ändringsflödet. Sedan skickar Azure-funktionen det ändrade dokumentet till händelsehubben.

1. Gå tillbaka till databasen som du klonade på enheten.  

2. Högerklicka på filen **ChangeFeedLabSolution.sln** och välj **Öppna med Visual Studio**.  

3. Navigera till **local.settings.json** i Visual Studio. Använd sedan de värden som du spelade in tidigare för att fylla i ämnena.  

4. Navigera till **ChangeFeedProcessor.cs**. Utför följande åtgärder i parametrarna för funktionen **Kör:**  

   * Ersätt texten **DITT SAMLINGSNAMN HÄR** MED namnet på din samling. Om du har följt tidigare instruktioner är namnet på din samling ändring av matningsflöde.  
   * Ersätt texten **DITT LEASINGINSAMLINGSNAMN HÄR** MED namnet på din lånesamling. Om du har följt tidigare instruktioner är namnet på din lånesamling **lån.**  
   * Högst upp i Visual Studio kontrollerar du att rutan Starta projekt till vänster om den gröna pilen säger **ChangeFeedFunction**.  
   * Välj **Start** högst upp på sidan för att köra programmet  
   * Du kan bekräfta att funktionen körs när konsolappen säger "Jobbvärd startade".

## <a name="insert-data-into-azure-cosmos-db"></a>Infoga data i Azure Cosmos DB 

Om du vill se hur ändringsflödet bearbetar nya åtgärder på en e-handelsplats måste du simulera data som representerar användare som visar artiklar från produktkatalogen, lägga till dessa artiklar i sina kundvagnar och köpa artiklarna i sina kundvagnar. Dessa data är godtyckliga och i syfte att replikera vilka data på en e-handelsplats skulle se ut.

1. Navigera tillbaka till databasen i Utforskaren och högerklicka på **ChangeFeedFunction.sln** för att öppna den igen i ett nytt Visual Studio-fönster.  

2. Navigera till **filen App.config.** Lägg `<appSettings>` till slutpunkten och den unika **PRIMÄRNYCKELn** som för ditt Azure Cosmos DB-konto som du hämtade tidigare i blockeringen.  

3. Lägg till i **samlings-** och **databasnamnen.** (Dessa namn bör **ändras och** **ändrafeedlabdatabase** om du inte väljer att namnge din annorlunda.)

   ![Uppdatera anslutningssträngar](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Spara ändringarna på alla filer som redigerats.  

5. Högst upp i Visual Studio kontrollerar du att rutan **Starta projekt** till vänster om den gröna pilen säger **DataGenerator**. Välj sedan **Start** högst upp på sidan för att köra programmet.  
 
6. Vänta tills programmet körs. Stjärnorna betyder att data kommer in! Håll programmet igång - det är viktigt att massor av data samlas in.  

7. Om du navigerar till [Azure-portalen](https://portal.azure.com/) och sedan till Cosmos DB-konto i resursgruppen visas de randomiserade data som importeras i **ändringsflödet.** **Data Explorer**
 
   ![Data som genereras i portalen](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Konfigurera ett dataflödesanalysjobb

Azure Stream Analytics är en fullständigt hanterad molntjänst för realtidsbearbetning av strömmande data. I det här labbet använder du direktanalys för att bearbeta nya händelser från eventhubben (d.v.s. när ett objekt visas, läggs till i en kundvagn eller köps), införliva dessa händelser i dataanalys i realtid och skicka dem till Power BI för visualisering.

1. Från [Azure-portalen](https://portal.azure.com/)navigerar du till din resursgrupp och streamar sedan **till streamjob1** (dataströmsanalysjobbet som du skapade i förlabbet).  

2. Välj **Ingångar** som visas nedan.  

   ![Skapa indata](./media/changefeed-ecommerce-solution/create-input.png)

3. Välj **+ Lägg till indata för ström**. Välj sedan **Händelsehubben** på den nedrullningsbara menyn.  

4. Fyll i det nya inmatningsformuläret med följande information:

   * Ange **indata**i fältet **Indataalias** .  
   * Välj alternativet för **Välj händelsehubb från dina prenumerationer**.  
   * Ange fältet **Prenumeration** på din prenumeration.  
   * Ange namnet på namnområdet För **händelsehubben** som du skapade under förlabbet.  
   * I **fältet Händelsehubbnamn** väljer du alternativet Använd **befintligt** och väljer **händelse-hub1** på den nedrullningsbara menyn.  
   * Lämna principnamnsfältet **för händelsehubben** inställt på standardvärdet.  
   * Lämna **händelseserieformat** som **JSON**.  
   * Lämna **kodningsfält** inställt på **UTF-8**.  
   * Lämna fältet **Händelsekomprimeringstyp** inställt på **Ingen**.  
   * Välj knappen **Spara**.

5. Navigera tillbaka till jobbsidan för direktuppspelningsanalys och välj **Utdata**.  

6. Välj **+ Lägg till**. Välj sedan **Power BI** på den nedrullningsbara menyn.  

7. Så här skapar du en ny Power BI-utdata för att visualisera genomsnittspriset:

   * Ange **genomsnittligt Prisutdrag**i fältet **Utdataalias** .  
   * Lämna fältet **Grupparbetsyta** inställt **på Auktorisera anslutning till lastarbetsytor**.  
   * Ange **medelvärdepris**i fältet **Datauppsättningsnamn** .  
   * Ange **medelvärdepris**i fältet **Tabellnamn** .  
   * Välj knappen **Auktorisera** och följ sedan instruktionerna för att auktorisera anslutningen till Power BI.  
   * Välj knappen **Spara**.  

8. Gå sedan tillbaka till **streamjob1** och välj **Redigera fråga**.

   ![Redigera fråga](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Klistra in följande fråga i frågefönstret. Frågan **GENOMSNITTSPRIS** beräknar genomsnittspriset för alla artiklar som visas av användare, genomsnittspriset för alla artiklar som läggs till i användarnas kundvagnar och genomsnittspriset för alla artiklar som köps av användare. Detta mått kan hjälpa e-handelsföretag bestämma vilka priser att sälja objekt på och vad lager att investera i. Om till exempel det genomsnittliga priset för artiklar som visas är mycket högre än genomsnittspriset för köpta artiklar, kan ett företag välja att lägga till billigare artiklar i lagret.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Välj sedan **Spara** i det övre vänstra hörnet.  

11. Gå nu tillbaka till **streamjob1** och välj **Start-knappen** högst upp på sidan. Azure Stream Analytics kan ta några minuter att starta, men så småningom kommer du att se det ändras från "Start" till "Kör".

## <a name="connect-to-power-bi"></a>Ansluta till Power BI

Power BI är en uppsättning verktyg för företagsanalys för att analysera och dela information. Det är ett bra exempel på hur du strategiskt kan visualisera de analyserade data.

1. Logga in på Power BI och navigera till **Min arbetsyta** genom att öppna menyn till vänster på sidan.  

2. Välj **+ Skapa** i det övre högra hörnet och välj sedan **Instrumentpanel för** att skapa en instrumentpanel.  

3. Välj **+ Lägg till panel** i det övre högra hörnet.  

4. Välj **Anpassade strömmande data**och välj sedan knappen **Nästa.**  
 
5. Välj **medelpris från** **DINA DATASETS**och välj sedan **Nästa**.  

6. Välj **Grupperat stapeldiagram** i fältet **Visualiseringstyp** på den nedrullningsliga menyn. Lägg till åtgärd under **Axel.** Hoppa över **Legend** utan att lägga till något. Lägg sedan till **avg**under nästa avsnitt som heter **Värde**. Välj **Nästa**, sedan rubrik diagrammet och välj **Använd**. Du bör se ett nytt diagram på instrumentpanelen!  

7. Nu, om du vill visualisera fler mått, kan du gå tillbaka till **streamjob1** och skapa ytterligare tre utdata med följande fält.

   a. **Utdataalias:** incomingRevenueOutput, Dataset namnger: incomingRevenue, Bordlägga känt: incomingRevenue  
   b. **Utdataalias:** top5Utflöde, Datauppsättningsnamn: topp5, Tabellnamn: topp5  
   c. **Utdataalias:** uniqueVisitorCountOutput, Dataset name: uniqueVisitorCount, Tabellnamn: uniqueVisitorCount

   Välj sedan **Redigera fråga** och klistra in följande frågor **ovanför** den du redan har skrivit.

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
   
   Top 5-frågan beräknar de 5 översta objekten, rangordnade efter antalet gånger som de har köpts. Det här måttet kan hjälpa e-handelsföretag att utvärdera vilka objekt som är mest populära och kan påverka företagets annonsering, prissättning och lagerbeslut.

   Frågan INTÄkt beräknar intäkten genom att summera priserna för alla artiklar som köps varje minut. Det här måttet kan hjälpa e-handelsföretag att utvärdera sitt ekonomiska resultat och även förstå vilka tider på dygnet bidrar till de flesta intäkter. Detta kan påverka den övergripande företagets strategi, marknadsföring i synnerhet.

   Frågan UNIKA BESÖKARE beräknar hur många unika besökare som finns på webbplatsen var femte sekund genom att identifiera unika kundvagns-ID: n. Detta mått kan hjälpa e-handelsföretag utvärdera sin webbplats aktivitet och strategize hur man får fler kunder.

8. Du kan nu lägga till paneler för dessa datauppsättningar också.

   * För Topp 5 skulle det vara meningsfullt att göra ett grupperat stapeldiagram med objekten som axel och antal som värde.  
   * För Intäkter skulle det vara meningsfullt att göra ett linjediagram med tiden som axel och summan av priserna som värde. Tidsfönstret som ska visas bör vara det största möjliga för att kunna leverera så mycket information som möjligt.  
   * För unika besökare skulle det vara meningsfullt att göra en kortvisualisering med antalet unika besökare som värde.

   Så här ser en exempelinstrumentpanel ut med följande diagram:

   ![visualiseringar](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Valfritt: Visualisera med en e-handelsplats

Du kommer nu att observera hur du kan använda din nya dataanalys verktyg för att ansluta med en riktig e-handelsplats. För att skapa e-handelsplatsen använder du en Azure Cosmos-databas för att lagra listan över produktkategorier (kvinnor, män, Unisex), produktkatalogen och en lista över de mest populära objekten.

1. Navigera tillbaka till [Azure-portalen](https://portal.azure.com/)och sedan till ditt **Cosmos DB-konto**och sedan till **Data Explorer**.  

   Lägg till två samlingar under **changefeedlabdatabase-produkter** - **products** och **kategorier** med fast lagringskapacitet.

   Lägg till en annan samling under **changefeedlabdatabase** med namnet **topItems** och **/Item** som partitionsnyckel.

2. Välj **topItems-samlingen** och under **Skala och inställningar** ställer du in tiden till **Live** till **30 sekunder** så att topItems uppdateras var 30:e sekund.

   ![Time to live](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Om du vill fylla **i topItems-samlingen** med de vanligaste inköpta objekten navigerar du tillbaka till **streamjob1** och lägger till en ny **utdata**. Välj **Cosmos DB**.

4. Fyll i de obligatoriska fälten enligt bilden nedan.

   ![Cosmos-utgång](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Om du har lagt till den valfria TOP 5-frågan i föregående del av labbet fortsätter du till del 5a. Om inte, fortsätt till del 5b.

   5a. I **streamjob1**väljer du **Redigera fråga** och klistrar in följande fråga i din Azure Stream Analytics-frågeredigerare under TOP 5-frågan men ovanför resten av frågorna.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. I **streamjob1**väljer du **Redigera fråga** och klistrar in följande fråga i azure stream analytics-frågeredigeraren framför alla andra frågor.

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

6. Öppna **EcommerceWebApp.sln** och navigera till **filen Web.config** i **Solution Explorer**.  

7. I `<appSettings>` blocket lägger du till **URI** och **PRIMÄRNYCKEL** som du sparade tidigare där det står **din URI här** och **din primära nyckel här**. Lägg sedan till i **databasnamnet** och **samlingsnamnet** som anges. (Dessa namn ska **ändras i databasen** och **ändramatningslaborktyra** om du inte väljer att namnge ditt på ett annat sätt.)

   Fyll i **produktsamlingsnamnet,** **kategorierna samlingsnamn**och **samlingsnamn** för de översta objekten som anges. (Dessa namn ska vara **produkter, kategorier och topItems** om du inte väljer att namnge dina på ett annat sätt.)  

8. Navigera till och öppna **kassamappen** i **EcommerceWebApp.sln.** Öppna sedan **filen Web.config** i mappen.  

9. Lägg `<appSettings>` till **URI-** och **PRIMÄRNYCKELN** som du sparade tidigare i blocket. Lägg sedan till i **databasnamnet** och **samlingsnamnet** som anges. (Dessa namn ska **ändras i databasen** och **ändramatningslaborktyra** om du inte väljer att namnge ditt på ett annat sätt.)  

10. Tryck på **Start** högst upp på sidan för att köra programmet.  

11. Nu kan du leka på e-handelsplatsen. När du visar en artikel, lägger till en artikel i kundvagnen, ändrar kvantiteten för en artikel i kundvagnen eller köper en artikel, skickas dessa händelser genom Cosmos DB-ändringsflödet till Event Hub, ASA och sedan Power BI. Vi rekommenderar att du fortsätter att köra DataGenerator för att generera betydande webbtrafikdata och tillhandahålla en realistisk uppsättning "Heta produkter" på e-handelsplatsen.

## <a name="delete-the-resources"></a>Ta bort resurserna

Om du vill ta bort de resurser som du skapade under det här labbet navigerar du till resursgruppen på [Azure-portalen](https://portal.azure.com/)och väljer sedan **Ta bort resursgrupp** högst upp på sidan och följer instruktionerna.

## <a name="next-steps"></a>Nästa steg 
  
* Mer information om ändringsflödet finns [i Arbeta med stöd för ändringsfeed i Azure Cosmos DB](change-feed.md) 
