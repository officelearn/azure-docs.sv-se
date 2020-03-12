---
title: Guide till förutsägelseunderhåll för flygindustrin - Team Data Science Process
description: En teknisk guide till lösnings mal len för förutsägande underhåll i flyg, verktyg och transport.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3b3a0b00ee6e1e170023584c2e643a5802166428
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087544"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Teknisk guide till lösnings mal len för förutsägande underhåll i Aerospace

> [!Important]
> Den här artikeln är inaktuell. Diskussionen om förutsägelse underhåll i Aerospace är fortfarande relevant, men för aktuell information, se [lösnings översikt för företags mål grupper](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Solution templates har utformats för att påskynda processen med att skapa en E2E-demo. En distribuerad mall etablerar din prenumeration med nödvändiga komponenter och skapar sedan relationerna mellan dem. Det lägger även datapipeline med exempeldata från en data-generator programmet, vilket du hämta och installera på den lokala datorn när du har distribuerat lösningsmallen. Data från generatorn hydrates datapipeline och börja skapa machine learning-förutsägelser som sedan kan visualiseras i Power BI-instrumentpanelen.

Distributionsprocessen vägleder dig genom stegen för att ställa in dina autentiseringsuppgifter för lösningen. Kontrollera att du anteckna autentiseringsuppgifterna som lösningens namn, användarnamn och lösenord som du anger under distributionen. 


Målen för den här artikeln är att:
- Beskriv Referensarkitektur och komponenter som etablerats i din prenumeration.
- Visa hur du kan ersätta exempeldata med dina egna data. 
- Visa hur du ändrar lösningsmallen.  

## <a name="overview"></a>Översikt
![Arkitektur för förebyggande underhåll](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

När du distribuerar lösningen aktiverar den Azure-tjänster, inklusive Händelsehubben, Stream Analytics, HDInsight, Data Factory och Machine Learning. I arkitekturdiagrammet visas hur Förutsägelseunderhåll för flyg lösningsmallen är uppbyggd. Du kan undersöka de här tjänsterna i Azure portal genom att klicka på dem i mallen lösningsdiagrammet skapat med distributionen av lösningen (med undantag för HDInsight som har etablerats på begäran när relaterade pipeline-aktiviteter som krävs för att köra och är ta bort efteråt).
Ladda ned en [full storleks version av diagrammet](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I följande avsnitt beskrivs delarna av lösningen.

## <a name="data-source-and-ingestion"></a>Datakällan och inmatning
### <a name="synthetic-data-source"></a>Datakälla för syntetiskt
För den här mallen genereras data källan som används från ett nedladdat Skriv bords program som du kör lokalt efter en lyckad distribution.

Välj den första noden och förutsägande Underhåll Datagenerator på diagrammet för mall för att hitta instruktionerna för att ladda ned och installera det här programmet. Anvisningarna finns i fältet egenskaper. I det här programmet matas tjänsten [Azure Event Hub](#azure-event-hub) med data punkter eller händelser som används i resten av lösnings flödet. Den här data källan härleds från offentligt tillgängliga data från data [lagringen för NASA](https://c3.nasa.gov/dashlink/resources/139/) med [turbofan-datauppsättningen för motor degraderingen](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Händelsen generation programmet fyller Azure Event Hub endast medan det körs på datorn.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Tjänsten [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) är mottagaren av de indata som tillhandahålls av den syntetiska data källan.

## <a name="data-preparation-and-analysis"></a>Förberedelse av data och analys  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använd [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) för att tillhandahålla analys i nära real tid i indata-dataströmmen från tjänsten [Azure Event Hub](#azure-event-hub) . Sedan publicerar du resultaten på en [Power BI](https://powerbi.microsoft.com) instrument panel och arkiverar alla rå inkommande händelser till tjänsten [Azure Storage](https://azure.microsoft.com/services/storage/) för senare bearbetning av [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -tjänsten.

### <a name="hdinsight-custom-aggregation"></a>Anpassade HDInsight-aggregering
Kör [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skript (dirigerad av Azure Data Factory) med HDInsight för att tillhandahålla agg regeringar för de råa händelser som arkiveras med hjälp av Azure Stream Analytics resursen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Gör förutsägelser för återstående livs längd (RUL) för en viss flyg Plans motor med hjälp av de indata som tagits emot med [Azure Machine Learning-tjänsten](https://azure.microsoft.com/services/machine-learning/) (dirigerad av Azure Data Factory). 

## <a name="data-publishing"></a>Publicering av data
### <a name="azure-sql-database"></a>Azure SQL Database
Använd [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) för att lagra förutsägelserna som tagits emot av Azure Machine Learning, som sedan används i [Power BI](https://powerbi.microsoft.com) instrument panelen.

## <a name="data-consumption"></a>Dataförbrukning
### <a name="power-bi"></a>Power BI
Använd [Power BI](https://powerbi.microsoft.com) för att visa en instrument panel som innehåller agg regeringar och aviseringar som tillhandahålls av [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), samt RUL förutsägelser lagrade i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som har producerats med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Hur du hanterar dina egna data
Det här avsnittet beskrivs hur du hanterar dina egna data till Azure och vilka områden kräver ändringar för de data du sätta i den här arkitekturen.

Det är osannolikt att din data uppsättning matchar den data uppsättning som används av den [turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) som används för den här lösnings mal len. Förstå dina data och kraven är avgörande i hur du ändrar den här mallen ska fungera med dina egna data. 

I följande avsnitt beskrivs de delar av mallen som kräver ändringar när en ny datauppsättning införs.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub är allmänt; data kan publiceras till hubben i CSV eller JSON-format. Ingen särskild bearbetning sker i Azure Event Hub, men det är viktigt att du förstår de data som matas in.

Det här dokumentet beskriver inte hur att mata in dina data, men du kan enkelt skicka händelser eller data till en Azure-Händelsehubb med hjälp av API: er för Event Hub.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Använd Azure Stream Analytics-resursen för att tillhandahålla analys i nära real tid genom att läsa data strömmar och skicka data till valfritt antal källor.

Förutsägande Underhåll för flyg lösningsmallen består Azure Stream Analytics-fråga av fyra underfrågor varje fråga som förbrukar händelser från Azure Event Hub-tjänsten med utdata till fyra olika platser. Dessa utdata består av tre Power BI-datauppsättningar och en Azure-lagringsplats.

Azure Stream Analytics-fråga kan hittas genom att:

* Ansluta till Azure-portalen
* Hitta Stream Analytics jobb ![Stream Analytics ikon](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) som genererades när lösningen distribuerades (*till exempel* **maintenancesa02asapbi** och **maintenancesa02asablob** för lösningen för förutsägelse underhåll)
* Att välja
  
  * ***Indata*** för att Visa frågans indata
  * ***Fråga*** för att Visa själva frågan
  * ***Utdata*** för att visa olika utdata

Information om hur du Azure Stream Analytics Query-konstruktion finns i [referensen Stream Analytics frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) på MSDN.

I den här lösningen utdatauppsättningar frågorna tre med nära realtidsanalys information om inkommande dataström till en Power BI-instrumentpanel som tillhandahålls som en del av den här lösningsmallen. Eftersom det finns implicit kunskap om dataformat för inkommande, måste de här frågorna ändras baserat på dataformat.

Frågan i det andra Stream Analytics jobb- **maintenancesa02asablob** skapar helt enkelt alla [Event Hub](https://azure.microsoft.com/services/event-hubs/) -händelser som ska [Azure Storage](https://azure.microsoft.com/services/storage/) och kräver därför ingen ändring oavsett ditt data format eftersom all händelse information strömmas till lagrings utrymmet.

### <a name="azure-data-factory"></a>Azure Data Factory
Tjänsten [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) dirigerar flyttningen och bearbetningen av data. I det förutsägande underhållet av flyg lösnings mal len består data fabriken av tre [pipelines](../../data-factory/concepts-pipelines-activities.md) som flyttar och bearbetar data med hjälp av olika tekniker.  Komma åt din data factory genom att öppna noden Data Factory längst ned i mallen lösningsdiagrammet skapat med distribution av lösningen. Fel under dina datauppsättningar som på grund av data factory distribueras innan datageneratorn startades. Dessa fel kan ignoreras och förhindrar inte att din data factory fungerar

![Fel för data Factory-datauppsättningen](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

I det här avsnittet beskrivs nödvändiga [pipeliner och aktiviteter](../../data-factory/concepts-pipelines-activities.md) som finns i [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Här är en diagramvy av lösningen.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Två av pipelinen i den här fabriken innehåller [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skript som används för att partitionera och samla in data. När detta anges finns skripten i det [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under installationen. Deras plats är: maintenancesascript\\\\skriptet\\\\Hive\\\\ (eller https://[ditt lösnings namn]. blob. Core. Windows. net/maintenancesascript).

Precis som med [Azure Stream Analytics](#azure-stream-analytics-1) frågor, har [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skripten implicit kunskap om det inkommande data formatet och måste ändras baserat på ditt data format.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -aktivitet som använder en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör ett [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skript för att partitionera data i [Azure Storage](https://azure.microsoft.com/services/storage/) under [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobbet.

[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skriptet för den här partitionerings aktiviteten är ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter vars slut resultat är de poäng förutsägelser från det [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet som är associerat med den här lösnings mal len.

Aktiviteter som ingår är:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -aktivitet med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör ett [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skript för att utföra agg regeringar och funktions teknik som krävs för [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet.
  [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -skriptet för den här partitionerings aktiviteten är ***PrepareMLInput. HQL***.
* [Kopierings](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet från [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -aktiviteten till en enda [Azure Storage](https://azure.microsoft.com/services/storage/) -blob som används av [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) -aktiviteten.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) -aktiviteten anropar [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet med resultat som finns i en enda [Azure Storage](https://azure.microsoft.com/services/storage/) -blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Den [här pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – [en kopierings](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet av [Azure Machine Learning](#azure-machine-learning) experimentet från ***MLScoringPipeline*** till den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som ingår i installationen av lösnings mal len.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet som används för den här lösnings mal len tillhandahåller återstående livs längd (RUL) för en flyg Plans motor. Experimentet är specifik för den datauppsättning som används och kräver ändring av eller ersättning som är specifika för data som finns.

## <a name="monitor-progress"></a>Övervaka förloppet
När Datageneratorn startas pipelinen börjar dehydratisera och de olika komponenterna i din lösning starta starta i åtgärden följande kommandon som utfärdas av data factory. Det finns två sätt att övervaka pipelinen.

* En av Stream Analytics-jobb skriver inkommande rådata till blob storage. Om du klickar på Blob Storage komponent i din lösning från skärmen har du distribuerat lösningen och klickar sedan på öppna i den högra panelen, så tar du till [Azure Portal](https://portal.azure.com/). När det, klickar du på BLOB-objekt. Nästa panelen visas en lista över behållare. Klicka på **maintenancesadata**. I nästa panel finns mappen **rawData** . I mappen \data är mappar med namn, t.ex timme = 17 och timme = 18. Förekomsten av dessa mappar anger rådata som genereras på datorn och lagras i blob storage. Du bör se csv-filer med begränsad storlek i MB i dessa mappar.
* Det sista steget i pipelinen är att skriva data (till exempel förutsägelser från maskininlärning) i SQL-databas. Du kan behöva vänta högst tre timmar innan data visas i SQL-databas. Ett sätt att övervaka hur mycket data som är tillgängligt i SQL Database är genom [Azure Portal](https://portal.azure.com/). På den vänstra panelen, leta upp SQL-databaser ![SQL-ikon](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) och klicka på den. Leta sedan reda på databasens **pmaintenancedb** och klicka på den. På nästa sida längst ned på sidan, klicka på Hantera
   
    ![Hantera ikon](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Här kan du klicka på ny fråga och fråga efter hur många rader (till exempel väljer count(*) från PMResult). Län, öka antalet rader i tabellen.

## <a name="power-bi-dashboard"></a>Power BI-instrumentpanel

Ställ in en Power BI-instrumentpanel för att visualisera dina Azure Stream Analytics-data (het sökväg) och batch förutsagda resultaten från Azure machine learning-(kall sökväg).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurera den kalla sökväg-instrumentpanelen
Målet är att hämta förutsägande RUL (återstående livslängd) för varje flygplansmotor när den har en flygning (växla) i datapipeline kalla sökvägen. Förutsägelse resultatet uppdateras var tredje timme för att förutsäga flygplansmotorer som är klar med en flygning under de senaste 3 timmarna.

Power BI ansluter till en Azure SQL Database som data källa där förutsägelse resultatet lagras. 

Obs! 
1.    När du distribuerar din lösning visas en förutsägelse i databasen inom tre timmar. Pbix-filen som medföljde nedladdningen Generator innehåller vissa seed-data så att du kan skapa Power BI-instrumentpanel direkt. 
2.    I det här steget är förutsättningen att ladda ned och installera den kostnads fria program varan [Power BI Skriv bordet](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Följande steg hjälper dig om hur du ansluter pbix-filen till den SQL-databas som har kunde vid tidpunkten för distribution som innehåller data (till exempel förutsägelser) för visualisering.

1. Hämta autentiseringsuppgifterna på databasen.
   
   Du behöver **databas server namn, databas namn, användar namn och lösen ord** innan du fortsätter till nästa steg. Här följer stegen för att guida dig hur du hittar dem.
   
   * När **Azure SQL Database** i lösningens diagram blir grönt, klickar du på det och sedan på **Öppna**.
   * Du ser en ny webbflik/fönster som visar Azure Portal sidan. Klicka på **resurs grupper** på den vänstra panelen.
   * Välj den prenumeration som du använder för att distribuera lösningen och välj sedan **YourSolutionName\_ResourceGroup**.
   * I den nya popup-panelen klickar du på ikonen för ![SQL-ikonen](./media/predictive-maintenance-technical-guide/icon-sql.png) för att komma åt databasen. Ditt databas namn är intill den här ikonen (till exempel **' pmaintenancedb '** ) och **databas Server namnet** visas under egenskapen Server namn och bör se ut ungefär som **YourSolutionName.Database.Windows.net**.
   * Ditt **användar namn** och **lösen ord** för databasen är samma som det användar namn och lösen ord som tidigare registrerades under distributionen av lösningen.
2. Uppdatera datakällan för rapportfilen kalla sökvägen med Power BI Desktop.
   
   * I mappen där du laddade ned och zippa Generator-filen dubbelklickar du på filen **PowerBI\\PredictiveMaintenanceAerospace. pbix** . Om du ser alla varningsmeddelanden när du öppnar filen kan ignorera dem. Klicka på **Redigera frågor**överst i filen.
     
     ![Redigera frågor](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Du ser två tabeller, **RemainingUsefulLife** och **PMResult**. Välj den första tabellen och klicka på ikonen för ![frågeinställningar](./media/predictive-maintenance-technical-guide/icon-query-settings.png) bredvid **"källa"** under **"tillämpade steg"** på den högra panelens **inställnings** panel. Ignorera alla varningsmeddelanden som visas.
   * Ersätt **"Server"** och **"Database"** i fönstret för popup-fönster med dina egna Server-och databas namn och klicka sedan på **OK**. För Server namn, se till att du anger port 1433 (**YourSolutionName.Database.Windows.net, 1433**). Lämna databas fältet som **pmaintenancedb**. Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa popup-fönster visas två alternativ i den vänstra rutan (**Windows** och **databas**). Klicka på **"databas"** , Fyll i ditt **"username"** och **"Password"** (användar namn och lösen ord som du angav när du först distribuerade lösningen och skapade en Azure SQL Database). I ***Välj vilken nivå som inställningarna ska tillämpas på***kontrollerar du alternativet på databas nivå. Klicka sedan på **Anslut**.
   * Klicka på den andra tabellen **PMResult** klicka sedan på ![navigerings ikon](./media/predictive-maintenance-technical-guide/icon-navigation.png) bredvid **"källa"** under **"tillämpade steg"** på den **högra panelen** och uppdatera Server-och databas namnen som i ovanstående steg och klicka på OK.
   * Stäng fönstret när du är interaktiva tillbaka till föregående sida. Ett meddelande visas – klicka på **Använd**. Klicka slutligen på knappen **Spara** för att spara ändringarna. Power BI-filen har nu upprätta anslutningen till servern. Om dina visualiseringar är tomma, kontrollera att du radera markeringarna på visualiseringar i visualisera alla data genom att klicka på raderingsikonen i det övre högra hörnet av seriemönstren. Använd uppdateringsknappen ska visa nya data på visualiseringar. Första gången se du bara seed-data på dina visualiseringar som data factory schemaläggs för uppdatering var tredje timme. När 3 timmar visas nya förutsägelser som visas i dina visualiseringar när du uppdaterar data.
3. Valfritt Publicera instrument panelen för kall bana till [Power BI online](https://www.powerbi.com/). Det här steget behöver ett Power BI konto (eller ett Office 365-konto).
   
   * Klicka på **publicera** och få sekunder senare visas ett fönster som visar publicering till Power BI lyckades! med en grön bockmarkering. Klicka på länken nedan ”öppna PredictiveMaintenanceAerospace.pbix i Power BI”. Detaljerade instruktioner finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * För att skapa en ny instrument panel: Klicka på **+** tecknet bredvid avsnittet **instrument paneler** i det vänstra fönstret. Ange namnet ”förutsägande Underhåll Demo” för den här nya instrumentpanelen.
   * När du har öppnat rapporten klickar du på ![fäst ikon](./media/predictive-maintenance-technical-guide/icon-pin.png) för att fästa alla visualiseringar på instrument panelen. Detaljerade instruktioner finns i [fästa en panel på en Power BI instrument panel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrumentpanelssidan och justera storleken och platsen för dina visualiseringar och redigera sina titlar. Detaljerade anvisningar om hur du redigerar dina paneler finns i [Redigera en panel – ändra storlek, flytta, byta namn, fästa, ta bort, Lägg till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är en exempel-instrumentpanel med vissa kalla sökvägen visualiseringar fästa på den.  Beroende på hur länge du kör din datagenerator, skilja dina nummer på visualiseringar sig.
     <br/>
     ![slutlig vy](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Om du vill schemalägga uppdatering av data håller du mus pekaren över **PredictiveMaintenanceAerospace** -datauppsättningen, klickar på ![ellips-ikon](./media/predictive-maintenance-technical-guide/icon-elipsis.png) och väljer sedan **Schemalägg uppdatering**.
     <br/>
     **Obs:** Om du ser ett varnings massage klickar du på **Redigera autentiseringsuppgifter** och kontrollerar att autentiseringsuppgifterna för databasen är desamma som de som beskrivs i steg 1.
     <br/>
     ![Schemalägg uppdatering](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Expandera avsnittet **schema uppdatering** . Aktivera ”Håll dina data aktuella”.
     <br/>
   * Schemalägga en uppdatering efter dina behov. Mer information finns i [data uppdatering i Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Konfigurera heta sökvägen instrumentpanel
Följande steg hjälper dig hur du visualisera data utdata från Stream Analytics-jobb som genererades vid tidpunkten för distribution. Det krävs ett [Power BI online](https://www.powerbi.com/) -konto för att utföra följande steg. Om du inte har något konto kan du [skapa ett](https://powerbi.microsoft.com/pricing).

1. Lägg till Power BI-utdata i Azure Stream Analytics (ASA).
   
   * Du måste följa anvisningarna i [Azure Stream Analytics & Power BI: en analys instrument panel för real tids visning av strömmande data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) för att konfigurera utdata för ditt Azure Stream Analytics-jobb som Power BI instrument panel.
   * ASA-frågan har tre utdata som är **aircraftmonitor**, **aircraftalert**och **flightsbyhour**. Du kan visa frågan genom att klicka på fliken fråga. motsvarar var och en av tabellerna måste du lägga till utdata till ASA. När du lägger till den första utdatan (**aircraftmonitor**) kontrollerar du att **utdata-aliaset**, **data uppsättningens namn** och **tabell namnet** är samma (**aircraftmonitor**). Upprepa stegen för att lägga till utdata för **aircraftalert**och **flightsbyhour**. När du har lagt till alla tre utdatatabellerna och igång ASA-jobbet, bör du få ett bekräftelsemeddelande (”starta Stream Analytics-jobbet maintenancesa02asapbi lyckades”).
2. Logga in på [Power BI online](https://www.powerbi.com)
   
   * På den vänstra panelens data uppsättnings avsnitt i min arbets yta visas ***data uppsättnings*** namnen **aircraftmonitor**, **aircraftalert**och **flightsbyhour** . Det här är den strömmande data som du push-överfört från Azure Stream Analytics i föregående steg. Data uppsättningen **flightsbyhour** får inte visas samtidigt som de andra två data uppsättningarna på grund av SQL-frågans natur. Men bör den visas efter en timme.
   * Kontrol lera att ***visualiserings*** fönstret är öppet och visas på höger sida av skärmen.
3. När du har data som flödar till Power BI kan börja du visualisera strömmande data. Nedan är en exempel-instrumentpanel med vissa heta sökvägen visualiseringar som fästs till den. Du kan skapa andra instrumentpaneler som baseras på lämpliga datauppsättningar. Beroende på hur länge du kör din datagenerator, skilja dina nummer på visualiseringar sig.

    ![Instrumentpanelsvy](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Här följer några steg för att skapa en av panelerna ovan – panelen "flottan av sensor 11 vs. Threshold 48,26":
   
   * Klicka på data uppsättning **aircraftmonitor** i avsnittet data uppsättningar för den vänstra panelen.
   * Klicka på **linje diagrams** ikonen.
   * Klicka på **bearbetad** i fönstret **fält** så att det visas under axel i fönstret **visualiseringar** .
   * Klicka på "S11" och "S11\_Alert" så att båda visas under "värden". Klicka på den lilla pilen bredvid **S11** och **S11\_varning**, ändra sum till Average.
   * Klicka på **Spara** högst upp och ge rapporten namnet "aircraftmonitor". Rapporten med namnet "aircraftmonitor" visas i avsnittet **rapporter** i **navigerings** fönstret till vänster.
   * Klicka på den **visuella ikonen fäst** i det övre högra hörnet i det här linje diagrammet. Ett fönster ”fäst på instrumentpanelen” kanske visas där du kan välja en instrumentpanel. ”Förutsägande Underhåll Demo” och sedan klicka på ”PIN-kod”.
   * Hovra över musen över den här panelen på instrument panelen, klicka på ikonen "redigera" i det övre högra hörnet för att ändra titeln till "flottan av sensor 11 vs. Threshold 48,26" och under rubrik till "genomsnitt över flottan över tid".

## <a name="delete-your-solution"></a>Ta bort lösningen
Se till att du stoppar datageneratorn när du inte aktivt använder lösningen som att köra datageneratorn medför större kostnader. Ta bort lösningen om du inte använder den. Tar bort lösningen tar du bort alla komponenter som etablerats i din prenumeration när du distribuerade lösningen. Om du vill ta bort lösningen klickar du på lösningens namn i den vänstra panelen i lösnings mal len och sedan på **ta bort**.

## <a name="cost-estimation-tools"></a>Verktyg för kostnadsuppskattning
Följande två verktyg är tillgängliga för att bättre förstå totala kostnader för att köra förebyggande underhåll för flyg lösningsmallen i din prenumeration:

* [Verktyget Microsoft Azure kostnads uppskattning (online)](https://azure.microsoft.com/pricing/calculator/)
* [Verktyget Microsoft Azure kostnads uppskattning (skriv bord)](https://www.microsoft.com/download/details.aspx?id=43376)

