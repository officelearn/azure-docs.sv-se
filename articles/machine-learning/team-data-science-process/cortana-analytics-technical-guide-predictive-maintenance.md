---
title: Förebyggande underhåll av flygplansmotorer med Azure – Cortana Intelligence Solution teknisk guide | Microsoft Docs
description: En teknisk guide till mallen med Microsoft Cortana Intelligence-lösning för förutsägande underhåll av flygplansmotorer, verktyg och transport.
services: machine-learning
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: fboylu
ms.openlocfilehash: 985010457413c15622f85e8ce8c6955a64ae40e9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985906"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Teknisk guide till lösningsmallen Cortana Intelligence för förebyggande underhåll i flygindustrin och andra företag

>[!Important]
Den här artikeln är inaktuell. Diskussion om förebyggande underhåll av flygplansmotorer är fortfarande relevanta, men för aktuell information, se [översikt över lösning för Business målgrupper](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Lösningsmallar är utformade att skynda på processen för att skapa en E2E-demo ovanpå Cortana Intelligence Suite. En mall för distribuerade etablerar din prenumeration med nödvändiga komponenter i Cortana Intelligence och bygger relationer mellan dem sedan. Det lägger även datapipeline med exempeldata från en data-generator programmet, vilket du hämta och installera på den lokala datorn när du har distribuerat lösningsmallen. Data från generatorn hydrates datapipeline och börja skapa machine learning-förutsägelser som sedan kan visualiseras i Power BI-instrumentpanelen.

Distributionsprocessen vägleder dig genom stegen för att ställa in dina autentiseringsuppgifter för lösningen. Kontrollera att du anteckna autentiseringsuppgifterna som lösningens namn, användarnamn och lösenord som du anger under distributionen. 


Målen för den här artikeln är att:
- Beskriv Referensarkitektur och komponenter som etablerats i din prenumeration.
- Visa hur du kan ersätta exempeldata med dina egna data. 
- Visa hur du ändrar lösningsmallen.  

> [!TIP]
> Du kan hämta och skriva ut en [PDF-version av den här artikeln](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Översikt
![Arkitektur för förebyggande underhåll](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

När du distribuerar lösningen, aktiverar Azure-tjänster inom Cortana Analytics Suite (inklusive Event Hub, Stream Analytics, HDInsight, Datafabrik och Machine Learning). I arkitekturdiagrammet visas hur Förutsägelseunderhåll för flyg lösningsmallen är uppbyggd. Du kan undersöka de här tjänsterna i Azure portal genom att klicka på dem i mallen lösningsdiagrammet skapat med distributionen av lösningen (med undantag för HDInsight som har etablerats på begäran när relaterade pipeline-aktiviteter som krävs för att köra och är ta bort efteråt).
Ladda ned en [bilden version av diagrammet](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I följande avsnitt beskrivs delarna av lösningen.

## <a name="data-source-and-ingestion"></a>Datakällan och inmatning
### <a name="synthetic-data-source"></a>Datakälla för syntetiskt
I den här mallen skapas den använda datakällan från ett program som du hämtar och kör lokalt efter slutförd distribution.

Välj den första noden och förutsägande Underhåll Datagenerator på diagrammet för mall för att hitta instruktionerna för att ladda ned och installera det här programmet. Anvisningarna finns i fältet egenskaper. Det här programmet feeds den [Azure Event Hub](#azure-event-hub) tjänst med datapunkter eller händelser som används i resten av lösningsflödet. Den här datakällan härleds från offentligt tillgängliga data från den [NASA datalager](https://c3.nasa.gov/dashlink/resources/139/) med hjälp av den [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Händelsen generation programmet fyller Azure Event Hub endast medan det körs på datorn.  

### <a name="azure-event-hub"></a>Azure händelsehubb  
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) tjänsten är mottagaren av indata som angetts av syntetiska datakällan.

## <a name="data-preparation-and-analysis"></a>Förberedelse av data och analys  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använd [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) att tillhandahålla analys på Indataströmmen från nära realtid den [Azure Event Hub](#azure-event-hub) service. Du kan sedan publicera resultaten på en [Power BI](https://powerbi.microsoft.com) instrumentpanelen samt arkiverar alla inkommande råhändelser i [Azure Storage](https://azure.microsoft.com/services/storage/) för senare bearbetning av den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)service.

### <a name="hdinsight-custom-aggregation"></a>Anpassade HDInsight-aggregering
Kör [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript (som orkestreras via Azure Data Factory) använder HDInsight för att tillhandahålla sammansställningar av råhändelser som arkiverats med hjälp av Azure Stream Analytics-tjänsten.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Göra förutsägelser kring återstående livslängd (RUL) för en viss flygplansmotor med hjälp av indata som tagits emot med [Azure Machine Learning-tjänsten](https://azure.microsoft.com/services/machine-learning/) (orkestreras via Azure Data Factory). 

## <a name="data-publishing"></a>Publicering av data
### <a name="azure-sql-database"></a>Azure SQL Database
Använd [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) att lagra förutsägelser som tagits emot av tjänsten Azure Machine Learning, som sedan används i den [Power BI](https://powerbi.microsoft.com) instrumentpanelen.

## <a name="data-consumption"></a>Dataförbrukning
### <a name="power-bi"></a>Power BI
Använd [Power BI](https://powerbi.microsoft.com) att visa en instrumentpanel som innehåller aggregeringar och aviseringar som tillhandahålls av [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), samt RUL förutsägelser som lagras i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som skapas med hjälp av [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Hur du hanterar dina egna data
Det här avsnittet beskrivs hur du hanterar dina egna data till Azure och vilka områden kräver ändringar för de data du sätta i den här arkitekturen.

Det är inte troligt att din datauppsättning matchar den datauppsättning som används av den [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) används för den här lösningsmallen. Förstå dina data och kraven är avgörande i hur du ändrar den här mallen ska fungera med dina egna data. 

I följande avsnitt beskrivs de delar av mallen som kräver ändringar när en ny datauppsättning införs.

### <a name="azure-event-hub"></a>Azure händelsehubb
Azure Event Hub är allmänt; data kan publiceras till hubben i CSV eller JSON-format. Ingen särskild bearbetning sker i Azure Event Hub, men det är viktigt att du förstår de data som matas in.

Det här dokumentet beskriver inte hur att mata in dina data, men du kan enkelt skicka händelser eller data till en Azure-Händelsehubb med hjälp av API: er för Event Hub.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använd Azure Stream Analytics-tjänsten för att tillhandahålla analys i nära realtid genom att läsa från dataströmmar och skickar data till valfritt antal källor.

Förutsägande Underhåll för flyg lösningsmallen består Azure Stream Analytics-fråga av fyra underfrågor varje fråga som förbrukar händelser från Azure Event Hub-tjänsten med utdata till fyra olika platser. Dessa utdata består av tre Power BI-datauppsättningar och en Azure-lagringsplats.

Azure Stream Analytics-fråga kan hittas genom att:

* Ansluta till Azure-portalen
* Hitta Stream Analytics-jobb ![Stream Analytics-ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) som genererades när lösningen har distribuerats (*till exempel*, **maintenancesa02asapbi** och **maintenancesa02asablob** för lösningen för förebyggande underhåll)
* Att välja
  
  * ***INDATA*** kan du se frågan indata
  * ***FRÅGA*** kan du se frågan själva
  * ***Matar ut*** att visa olika utdata

Information om Azure Stream Analytics frågekonstruktion finns i den [referens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I den här lösningen utdatauppsättningar frågorna tre med nära realtidsanalys information om inkommande dataström till en Power BI-instrumentpanel som tillhandahålls som en del av den här lösningsmallen. Eftersom det finns implicit kunskap om dataformat för inkommande, måste de här frågorna ändras baserat på dataformat.

Frågan i det andra Stream Analytics-jobbet **maintenancesa02asablob** bara matar ut alla [Händelsehubb](https://azure.microsoft.com/services/event-hubs/) händelser till [Azure Storage](https://azure.microsoft.com/services/storage/) och därför kräver inga ändringar oavsett dina dataformat som den fullständiga händelsen strömmas information till lagring.

### <a name="azure-data-factory"></a>Azure Data Factory
Den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) dirigerar förflyttning och bearbetning av data. I den förebyggande underhåll för flyg lösningsmallen data factory består av tre [pipelines](../../data-factory/concepts-pipelines-activities.md) som flytta och bearbeta data med olika tekniker.  Komma åt din data factory genom att öppna noden Data Factory längst ned i mallen lösningsdiagrammet skapat med distribution av lösningen. Fel under dina datauppsättningar som på grund av data factory distribueras innan datageneratorn startades. Dessa fel kan ignoreras och förhindrar inte att din data factory fungerar

![Fel för data Factory-datauppsättningen](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Det här avsnittet beskrivs nödvändiga [pipelines och aktiviteter](../../data-factory/concepts-pipelines-activities.md) i den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Här är en diagramvy av lösningen.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Två av pipelines för den här fabriken innehåller [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript som används för att partitionera och aggregera data. När anges är skript som finns i den [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under konfigurationen. Var de är: maintenancesascript\\\\skriptet\\\\hive\\ \\ (eller https://[Your lösning name].blob.core.windows.net/maintenancesascript).

Liknar [Azure Stream Analytics](#azure-stream-analytics-1) frågor, den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript har implicit kunskap om dataformat för inkommande och måste ändras baserat på dataformat.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Detta [pipeline](../../data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för att partitionera data placeras i [Azure Storage](https://azure.microsoft.com/services/storage/) under den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobbet.

Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för detta partitionering är ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Detta [pipeline](../../data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter vars slutresultatet är poängsatta förutsägelser från den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment som är associerade med den här lösningsmallen.

Aktiviteter som ingår är:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptet för att utföra aggregeringar och funktionstekniker som krävs för den [Azure-dator Learning](https://azure.microsoft.com/services/machine-learning/) experimentera.
  Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för detta partitionering är ***PrepareMLInput.hql***.
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultaten från den [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivitet till en enda [Azure Storage](https://azure.microsoft.com/services/storage/) blob som nås av den [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet anropar den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment med resultat i en enda [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Detta [pipeline](../../data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [kopia](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet av den [Azure Machine Learning](#azure-machine-learning) experiment från den  ***MLScoringPipeline*** till den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) etablerats som en del av lösningen mall installationen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentera används för den här lösningsmallen erbjuder den återstående driftstid (RUL) för en flygplansmotor. Experimentet är specifik för den datauppsättning som används och kräver ändring av eller ersättning som är specifika för data som finns.

Information om hur Azure Machine Learning-experiment har skapats, finns i [förebyggande underhåll: steg 1 av 3, förberedelse av data och funktionsframställning](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Övervaka förloppet
När Datageneratorn startas pipelinen börjar dehydratisera och de olika komponenterna i din lösning starta starta i åtgärden följande kommandon som utfärdas av data factory. Det finns två sätt att övervaka pipelinen.

1. En av Stream Analytics-jobb skriver inkommande rådata till blob storage. Om du klickar på Blob Storage-komponenten i din lösning från skärmen för att du har distribuerat lösningen och klickar sedan på Öppna i den högra panelen, det tar att den [Azure-portalen](https://portal.azure.com/). När det, klickar du på BLOB-objekt. Nästa panelen visas en lista över behållare. Klicka på **maintenancesadata**. I nästa panelen är den **\data** mapp. I mappen \data är mappar med namn, t.ex timme = 17 och timme = 18. Förekomsten av dessa mappar anger rådata som genereras på datorn och lagras i blob storage. Du bör se csv-filer med begränsad storlek i MB i dessa mappar.
2. Det sista steget i pipelinen är att skriva data (till exempel förutsägelser från maskininlärning) i SQL-databas. Du kan behöva vänta högst tre timmar innan data visas i SQL-databas. Ett sätt att övervaka hur mycket data är tillgängliga i SQL-databasen är via den [Azure-portalen](https://portal.azure.com/). Leta upp SQL-databaser på den vänstra panelen ![SQL ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) och klicka på den. Leta upp din databas **pmaintenancedb** och klicka på den. På nästa sida längst ned på sidan, klicka på Hantera
   
    ![Hantera ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Här kan du klicka på ny fråga och fråga efter hur många rader (till exempel väljer count(*) från PMResult). Län, öka antalet rader i tabellen.

## <a name="power-bi-dashboard"></a>Power BI-instrumentpanel

Ställ in en Power BI-instrumentpanel för att visualisera dina Azure Stream Analytics-data (het sökväg) och batch förutsagda resultaten från Azure machine learning-(kall sökväg).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurera den kalla sökväg-instrumentpanelen
Målet är att hämta förutsägande RUL (återstående livslängd) för varje flygplansmotor när den har en flygning (växla) i datapipeline kalla sökvägen. Förutsägelse resultatet uppdateras var tredje timme för att förutsäga flygplansmotorer som är klar med en flygning under de senaste 3 timmarna.

Powerbi ansluter till en Azure SQL-databas som datakälla, där de förutsagda resultaten som lagras. Obs: 1) om hur du distribuerar din lösning, en förutsägelse visas i databasen inom 3 timmar.
Pbix-filen som medföljde nedladdningen Generator innehåller vissa seed-data så att du kan skapa Power BI-instrumentpanel direkt. 2) i det här steget är nödvändiga att ladda ned och installera den kostnadsfria programvaran [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Följande steg hjälper dig om hur du ansluter pbix-filen till den SQL-databas som har kunde vid tidpunkten för distribution som innehåller data (till exempel förutsägelser) för visualisering.

1. Hämta autentiseringsuppgifterna på databasen.
   
   Du behöver **databasen servernamnet, databasnamnet, användarnamnet och lösenordet** innan du fortsätter till nästa steg. Här följer stegen för att guida dig hur du hittar dem.
   
   * En gång **Azure SQL-databas** på din mall diagram blir grön, klickar du på den och klicka sedan på **”öppna'**.
   * Du ser ett nytt fliken/webbläsarfönster som visar sidan för Azure portal. Klicka på **”resursgrupper”** på den vänstra panelen.
   * Välj den prenumeration som du använder för att distribuera lösningen och välj sedan **' YourSolutionName\_ResourceGroup'**.
   * I den nya frigörs panelen, klickar du på den ![SQL ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikon för att komma åt databasen. Databasnamnet är bredvid ikonen (till exempel **'pmaintenancedb'**), och **Databasservernamnet** anges under egenskapen Server name och bör likna  **YourSoutionName.database.windows.net**.
   * Din databas **användarnamn** och **lösenord** är samma som användarnamnet och lösenordet tidigare registreras under distributionen av lösningen.
2. Uppdatera datakällan för rapportfilen kalla sökvägen med Power BI Desktop.
   
   * I den mapp där du hämtade och packat Generator-filen, dubbelklickar du på den **PowerBI\\PredictiveMaintenanceAerospace.pbix** fil. Om du ser alla varningsmeddelanden när du öppnar filen kan ignorera dem. Överst i filen, klickar du på **redigera frågor**.
     
     ![Redigera frågor](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Du ser två tabeller, **RemainingUsefulLife** och **PMResult**. Välj den första tabellen och klicka på ![fråga inställningsikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) bredvid **'Source'** under **TILLÄMPADE steg** till höger **'Frågeinställningar'** panelen. Ignorera alla varningsmeddelanden som visas.
   * I frigörs fönster, ersätter **'Server'** och **”databas”** med din egen och databasnamn och sedan på **'OK'**. Kontrollera att du anger porten 1433 för servernamnet, (**YourSoutionName.database.windows.net, 1433**). Lämna fältet databas som **pmaintenancedb**. Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa frigörs fönstret, ser du två alternativ i det vänstra fönstret (**Windows** och **databasen**). Klicka på **”databas”**, Fyll i din **'Användarnamn'** och **'Password'** (detta är det användarnamn och lösenord som du angav när du först distribueras lösningen och skapat en Azure SQL-databas). I ***väljer vilken nivå dessa inställningar ska tillämpas***, kontrollera nivån databasalternativ. Klicka sedan på **”Anslut”**.
   * Klicka på den andra tabellen **PMResult** klickar ![ikonen Rapportnavigering](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) bredvid **'Source'** under **TILLÄMPADE steg** till höger **'Frågeinställningar'** panelen och uppdatera namnen på servern och databasen som i ovanstående steg och klicka på OK.
   * Stäng fönstret när du är interaktiva tillbaka till föregående sida. Ett meddelande visas – Klicka på **tillämpa**. Klicka slutligen på den **spara** för att spara ändringarna. Power BI-filen har nu upprätta anslutningen till servern. Om dina visualiseringar är tomma, kontrollera att du radera markeringarna på visualiseringar i visualisera alla data genom att klicka på raderingsikonen i det övre högra hörnet av seriemönstren. Använd uppdateringsknappen ska visa nya data på visualiseringar. Första gången se du bara seed-data på dina visualiseringar som data factory schemaläggs för uppdatering var tredje timme. När 3 timmar visas nya förutsägelser som visas i dina visualiseringar när du uppdaterar data.
3. (Valfritt) Publicera instrumentpanelen kalla sökvägen till [Power BI online](http://www.powerbi.com/). Observera att det här steget en Power BI-konto (eller Office 365-konto).
   
   * Klicka på **”publicera”** och några sekunder senare ett fönster visas med ”publicering till Power BI lyckades”! med en grön bockmarkering. Klicka på länken nedan ”öppna PredictiveMaintenanceAerospace.pbix i Power BI”. Detaljerade anvisningar finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Att skapa en ny instrumentpanel: Klicka på den **+** logga bredvid den **instrumentpaneler** avsnitt i det vänstra fönstret. Ange namnet ”förutsägande Underhåll Demo” för den här nya instrumentpanelen.
   * När du öppnar rapporten klickar du på ![fästikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) att fästa alla visualiseringar på instrumentpanelen. Detaljerade anvisningar finns i [fästa en panel på en Power BI-instrumentpanel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrumentpanelssidan och justera storleken och platsen för dina visualiseringar och redigera sina titlar. Detaljerade anvisningar om hur du redigerar dina paneler finns i [redigera en panel – ändra storlek, flytta, Byt namn på, PIN-kod, ta bort, lägga till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är en exempel-instrumentpanel med vissa kalla sökvägen visualiseringar fästa på den.  Beroende på hur länge du kör din datagenerator, skilja dina nummer på visualiseringar sig.
     <br/>
     ![Sista vy](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Att schemalägga en uppdatering av data, håller du muspekaren över den **PredictiveMaintenanceAerospace** datauppsättning, klickar du på ![ellips-ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) och välj sedan **Schemalägg uppdatering**.
     <br/>
     **Obs:** om du ser en varning massage, klickar du på **redigera autentiseringsuppgifter** och se till att dina autentiseringsuppgifter på databasen är samma som de som beskrivs i steg 1.
     <br/>
     ![Schemalägg uppdatering](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expandera den **Schemalägg uppdatering** avsnittet. Aktivera ”Håll dina data aktuella”.
     <br/>
   * Schemalägga en uppdatering efter dina behov. Mer information finns i [datauppdatering i Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Konfigurera heta sökvägen instrumentpanel
Följande steg hjälper dig hur du visualisera data utdata från Stream Analytics-jobb som genererades vid tidpunkten för distribution. En [Power BI online](http://www.powerbi.com/) konto krävs för att utföra följande steg. Om du inte har ett konto, kan du [skapar ett](https://powerbi.microsoft.com/pricing).

1. Lägg till Power BI-utdata i Azure Stream Analytics (ASA).
   
   * Du måste följa anvisningarna i [Azure Stream Analytics och Power BI: en analysinstrumentpanel för realtidsinsyn för strömmande data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) att ställa in utdata från ditt Azure Stream Analytics-jobb som Power BI-instrumentpanelen.
   * ASA-frågan har tre utdata som är **aircraftmonitor**, **aircraftalert**, och **flightsbyhour**. Du kan visa frågan genom att klicka på fliken fråga. Motsvarar var och en av dessa tabeller som du behöver lägga till utdata i ASA. När du lägger till den första utdatan (**aircraftmonitor**) se till att den **Utdataaliaset**, **Datamängdsnamn** och **tabellnamn** är samma (**aircraftmonitor**). Upprepa stegen för att lägga till utdata för **aircraftalert**, och **flightsbyhour**. När du har lagt till alla tre utdatatabellerna och igång ASA-jobbet, bör du få ett bekräftelsemeddelande (”starta Stream Analytics-jobbet maintenancesa02asapbi lyckades”).
2. Logga in på [Power BI online](http://www.powerbi.com)
   
   * På den vänstra panelen datauppsättningar avsnitt i Min arbetsyta i ***DATAUPPSÄTTNING*** namn **aircraftmonitor**, **aircraftalert**, och **flightsbyhour** bör visas. Det här är den strömmande data som du push-överfört från Azure Stream Analytics i föregående steg. Datauppsättningen **flightsbyhour** kanske inte visas på samma gång som de andra två datauppsättningarna på grund av SQL-frågan bakom den. Men bör den visas efter en timme.
   * Kontrollera att den ***visualiseringar*** fönstret är öppet och visas på höger sida av skärmen.
3. När du har data som flödar till Power BI kan börja du visualisera strömmande data. Nedan är en exempel-instrumentpanel med vissa heta sökvägen visualiseringar som fästs till den. Du kan skapa andra instrumentpaneler som baseras på lämpliga datauppsättningar. Beroende på hur länge du kör din datagenerator, skilja dina nummer på visualiseringar sig.

    ![Instrumentpanelsvy](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Här följer några steg för att skapa någon av panelerna ovan – ”Vagnpark vyn av Sensor 11 vs. Tröskelvärdet 48,26 ”panelen:
   
   * Klicka på datauppsättningen **aircraftmonitor** på den vänstra panelen datauppsättningar avsnittet.
   * Klicka på den **linjediagram** ikon.
   * Klicka på **bearbetade** i den **fält** fönstret så att den visar under ”axel” i den **visualiseringar** fönstret.
   * Klicka på ”s11” och ”s11\_aviseringen” så att de båda visas under ”Values”. Klicka på den lilla pilen bredvid **s11** och **s11\_avisering**, ändra ”Sum” till ”Average”.
   * Klicka på **spara** högst upp och namn på rapporten ”aircraftmonitor”. Rapporten med namnet ”aircraftmonitor” visas i den **rapporter** i avsnittet den **Navigator** fönstret till vänster.
   * Klicka på den **PIN-kod Visual** -ikonen i det övre högra hörnet av det här linjediagrammet. Ett fönster ”fäst på instrumentpanelen” kanske visas där du kan välja en instrumentpanel. ”Förutsägande Underhåll Demo” och sedan klicka på ”PIN-kod”.
   * Hovra med musen över den här panelen på instrumentpanelen, klicka på ikonen ”Redigera” i det övre högra hörnet för att ändra rubriken till ”Vagnpark vy över Sensor 11 vs. Tröskelvärdet 48,26 ”och underrubriken för att” medelvärde över flotta med tiden ”.

## <a name="delete-your-solution"></a>Ta bort lösningen
Se till att du stoppar datageneratorn när du inte aktivt använder lösningen som att köra datageneratorn medför större kostnader. Ta bort lösningen om du inte använder den. Tar bort lösningen tar du bort alla komponenter som etablerats i din prenumeration när du distribuerade lösningen. Om du vill ta bort lösningen, klickar du på din lösning i den vänstra panelen i lösningsmallen och klicka sedan på **ta bort**.

## <a name="cost-estimation-tools"></a>Verktyg för kostnadsuppskattning
Följande två verktyg är tillgängliga för att bättre förstå totala kostnader för att köra förebyggande underhåll för flyg lösningsmallen i din prenumeration:

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator Tool (skrivbord)](http://www.microsoft.com/download/details.aspx?id=43376)

