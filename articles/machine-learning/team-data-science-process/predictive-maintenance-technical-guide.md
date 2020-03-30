---
title: Guide till prediktivt underhåll för flyg - Team Data Science Process
description: En teknisk guide till lösningsmallen för prediktivt underhåll inom flyg, verktyg och transporter.
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
ms.openlocfilehash: 0542106f70e96b6c2f63e8ca03d2532de191d365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477178"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Teknisk guide till lösningsmallen för prediktivt underhåll inom flygindustrin

> [!Important]
> Den här artikeln har tagits bort. Diskussionen om förutsägande underhåll inom flygbranschen är fortfarande relevant, men för aktuell information, se [Lösningsöversikt för affärsmålgrupp](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Lösningsmallar är utformade för att påskynda processen att bygga en E2E-demo. En distribuerad mall etablerar din prenumeration med nödvändiga komponenter och bygger sedan relationerna mellan dem. Den frön också datapipelinen med exempeldata från ett datageneratorprogram, som du hämtar och installerar på din lokala dator när du har distribuerat lösningsmallen. Data från generatorn återfuktar datapipelinen och börjar generera förutsägelser för maskininlärning, som sedan kan visualiseras på Power BI-instrumentpanelen.

Distributionsprocessen guidar dig genom flera steg för att konfigurera dina lösningsautentiseringsuppgifter. Se till att du registrerar autentiseringsuppgifter som lösningsnamn, användarnamn och lösenord som du anger under distributionen. 


Syftet med denna artikel är att:
- Beskriv referensarkitekturen och komponenterna som etablerats i prenumerationen.
- Visa hur du ersätter exempeldata med dina egna data. 
- Visa hur du ändrar lösningsmallen.  

## <a name="overview"></a>Översikt
![Arkitektur för förutsägande underhåll](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

När du distribuerar lösningen aktiveras Azure-tjänster, inklusive Event Hub, Stream Analytics, HDInsight, Data Factory och Machine Learning. Arkitekturdiagrammet visar hur mallen för förutsägande underhåll för flyglösningar är konstruerad. Du kan undersöka dessa tjänster i Azure-portalen genom att klicka på dem i lösningsmalldiagrammet som skapats med lösningsdistributionen (förutom HDInsight, som etableras på begäran när relaterade pipeline-aktiviteter krävs för att köras och raderas efteråt).
Ladda ner en [fullstor version av diagrammet](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I följande avsnitt beskrivs lösningsdelarna.

## <a name="data-source-and-ingestion"></a>Datakälla och inmatning
### <a name="synthetic-data-source"></a>Syntetisk datakälla
För den här mallen genereras datakällan som används från ett nedladdat skrivbordsprogram som du kör lokalt efter en lyckad distribution.

Om du vill hitta instruktionerna för att hämta och installera det här programmet väljer du den första noden, Predictive Maintenance Data Generator, i lösningsmalldiagrammet. Instruktionerna finns i fältet Egenskaper. Det här programmet matar [Azure Event Hub-tjänsten](#azure-event-hub) med datapunkter eller händelser som används i resten av lösningsflödet. Den här datakällan härleds från offentligt tillgängliga data från [NASA:s datadatabas](https://c3.nasa.gov/dashlink/resources/139/) med hjälp av [datauppsättningen för simulering av turbofansmotorförsämring](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Händelsegenereringsprogrammet fyller bara i Azure Event Hub medan det körs på din dator.  

### <a name="azure-event-hub"></a>Azure Event Hub  
[Azure Event Hub-tjänsten](https://azure.microsoft.com/services/event-hubs/) är mottagare av indata som tillhandahålls av den syntetiska datakällan.

## <a name="data-preparation-and-analysis"></a>Förberedelse och analys av data  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använd [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) för att tillhandahålla analys i nära realtid på indataströmmen från Azure Event [Hub-tjänsten.](#azure-event-hub) Du publicerar sedan resultat på en [Power BI-instrumentpanel](https://powerbi.microsoft.com) samt arkiverar alla råa inkommande händelser till Azure [Storage-tjänsten](https://azure.microsoft.com/services/storage/) för senare bearbetning av Azure Data [Factory-tjänsten.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>Anpassad HDInsight-aggregering
Kör [Hive-skript](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orkestrerade av Azure Data Factory) med HDInsight för att tillhandahålla aggregeringar på råa händelser som arkiveras med Azure Stream Analytics-resursen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Gör förutsägelser om den återstående livslängden (RUL) för en viss flygplansmotor med hjälp av de indata som tas emot med [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (orkestrerad av Azure Data Factory). 

## <a name="data-publishing"></a>Publicering av data
### <a name="azure-sql-database"></a>Azure SQL Database
Använd [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) för att lagra förutsägelser som tas emot av Azure Machine Learning, som sedan förbrukas i Power [BI-instrumentpanelen.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Dataförbrukning
### <a name="power-bi"></a>Power BI
Använd [Power BI](https://powerbi.microsoft.com) för att visa en instrumentpanel som innehåller aggregeringar och aviseringar som tillhandahålls av Azure Stream [Analytics](https://azure.microsoft.com/services/stream-analytics/), samt RUL-förutsägelser som lagras i Azure [SQL Database](https://azure.microsoft.com/services/sql-database/) och som har producerats med Azure [Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Så här tar du in dina egna data
I det här avsnittet beskrivs hur du tar med egna data till Azure och vilka områden som kräver ändringar för de data du tar med i den här arkitekturen.

Det är osannolikt att din datauppsättning matchar den datauppsättning som används av [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) som används för den här lösningsmallen. Att förstå dina data och kraven är avgörande för hur du ändrar den här mallen för att arbeta med dina egna data. 

I följande avsnitt beskrivs de delar av mallen som kräver ändringar när en ny datauppsättning introduceras.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub är allmän; data kan bokföras i navet i antingen CSV- eller JSON-format. Ingen speciell bearbetning sker i Azure Event Hub, men det är viktigt att du förstår de data som matas in i den.

Det här dokumentet beskriver inte hur du inbetar dina data, men du kan enkelt skicka händelser eller data till en Azure Event Hub med hjälp av Api:erna för händelsehubb.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Använd Azure Stream Analytics-resursen för att tillhandahålla analyser i nära realtid genom att läsa från dataströmmar och mata ut data till valfritt antal källor.

För den effektiva lösningsmallen för förutsägande underhåll för flyg- och rymdlösningar består Azure Stream Analytics-frågan av fyra underfrågor, där varje fråga förbrukar händelser från Azure Event Hub-tjänsten, med utdata till fyra olika platser. Dessa utdata består av tre Power BI-datauppsättningar och en Azure Storage-plats.

Azure Stream Analytics-frågan kan hittas av:

* Ansluta till Azure-portalen
* ![Hitta ikonen](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) Stream Analytics-jobb Stream Analytics som genererades när lösningen distribuerades (*till exempel* **underhållsa02asapbi** och **maintenancesa02asablob** för den prediktiva underhållslösningen)
* Välja
  
  * ***INDATA*** för att visa frågeindata
  * ***FRÅGA*** för att visa själva frågan
  * ***UTDATA*** för att visa de olika utgångarna

Information om Azure Stream Analytics frågekonstruktion finns i [Referensen för Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) på MSDN.

I den här lösningen utdatautdatautdatadatauppsättningar med analysinformation i nära realtid om inkommande dataström till en Power BI-instrumentpanel som tillhandahålls som en del av den här lösningsmallen. Eftersom det finns implicit kunskap om det inkommande dataformatet måste dessa frågor ändras baserat på ditt dataformat.

Frågan i den andra Stream **Analytics-jobbunderhållsa02asabloben** matar helt enkelt ut alla [Event Hub-händelser](https://azure.microsoft.com/services/event-hubs/) till [Azure Storage](https://azure.microsoft.com/services/storage/) och kräver därför ingen ändring oavsett ditt dataformat när den fullständiga händelseinformationen strömmas till lagring.

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory-tjänsten](https://azure.microsoft.com/documentation/services/data-factory/) dirigerar förflyttning och bearbetning av data. I mallen Predictive Maintenance for Aerospace Solution består datafabriken av tre [rörledningar](../../data-factory/concepts-pipelines-activities.md) som flyttar och bearbetar data med hjälp av olika tekniker.  Öppna datafabriken genom att öppna noden Data Factory längst ned i lösningsmalldiagrammet som skapats med lösningens distribution. Fel under dina datauppsättningar beror på att datafabriken distribueras innan datageneratorn startades. Dessa fel kan ignoreras och hindrar inte datafabriken från att fungera.

![Data Factory-datauppsättningsfel](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

I det här avsnittet beskrivs nödvändiga [pipelines och aktiviteter](../../data-factory/concepts-pipelines-activities.md) i Azure Data [Factory](https://azure.microsoft.com/documentation/services/data-factory/). Här är en diagramvy av lösningen.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Två av pipelines i den här fabriken innehåller [Hive-skript](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) som används för att partitionera och aggregera data. När det noteras finns skripten i [Azure Storage-kontot](https://azure.microsoft.com/services/storage/) som skapats under installationen. Deras plats är: underhållsbeskrivning\\\\skript\\\\hive\\ \\ (eller https://[Din lösningsnamn].blob.core.windows.net/maintenancesascript).

I likhet med [Azure Stream Analytics-frågor](#azure-stream-analytics-1) har [Hive-skripten](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) implicit kunskap om det inkommande dataformatet och måste ändras baserat på ditt dataformat.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [HDInsightHive-aktivitet](../../data-factory/transform-data-using-hadoop-hive.md) med hjälp av en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör ett [Hive-skript](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) för att partitionera data som placerats i [Azure Storage](https://azure.microsoft.com/services/storage/) under [Azure Stream Analytics-jobbet.](https://azure.microsoft.com/services/stream-analytics/)

[Hive-skriptet](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) för den här partitioneringsuppgiften är ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter vars slutresultat är de poängsatta förutsägelserna från [Azure Machine Learning-experimentet](https://azure.microsoft.com/services/machine-learning/) som är associerat med den här lösningsmallen.

Aktiviteter som ingår är:

* [HDInsightHive-aktivitet](../../data-factory/transform-data-using-hadoop-hive.md) med hjälp av en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör ett [Hive-skript](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) för att utföra aggregeringar och funktionsteknik som krävs för [Azure Machine Learning-experimentet.](https://azure.microsoft.com/services/machine-learning/)
  [Hive-skriptet](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) för den här partitioneringsuppgiften är ***PrepareMLInput.hql***.
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultaten från [HDInsightHive-aktiviteten](../../data-factory/transform-data-using-hadoop-hive.md) till en enda [Azure Storage-blob](https://azure.microsoft.com/services/storage/) som används av [AzureMLBatchScoring-aktiviteten.](https://msdn.microsoft.com/library/azure/dn894009.aspx)
* [AzureMLBatchScoring-aktivitet](https://msdn.microsoft.com/library/azure/dn894009.aspx) anropar [Azure Machine Learning-experimentet,](https://azure.microsoft.com/services/machine-learning/) med resultat som sätts i en enda Azure Storage-blob. [Azure Storage](https://azure.microsoft.com/services/storage/)

#### <a name="copyscoredresultpipeline"></a>*KopieraMålmålmålpipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet - en [kopieringsaktivitet](https://msdn.microsoft.com/library/azure/dn835035.aspx) som flyttar resultaten av [Azure Machine Learning-experimentet](#azure-machine-learning) från ***MLScoringPipeline*** till [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som etablerats som en del av lösningsmallsinstallationen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning-experimentet](https://azure.microsoft.com/services/machine-learning/) som används för den här lösningsmallen ger återstående livslängd (RUL) för en flygplansmotor. Experimentet är specifikt för den datauppsättning som förbrukas och kräver ändring eller ersättning som är specifik för de data som förs in.

## <a name="monitor-progress"></a>Övervaka förloppet
När datageneratorn har lanserats börjar pipelinen att torka ut, och de olika komponenterna i din lösning börjar sparka till handling efter de kommandon som utfärdats av datafabriken. Det finns två sätt att övervaka pipelinen.

* Ett av Stream Analytics-jobben skriver rådata till blob-lagring. Om du klickar på Blob Storage-komponenten i din lösning från skärmen som du har distribuerat lösningen och sedan klickar på Öppna på den högra panelen, tar den dig till [Azure-portalen](https://portal.azure.com/). En gång där, klicka på Blobbar. I nästa panel visas en lista över behållare. Klicka på **underhållsdata**. I nästa panel finns **rawdata-mappen.** I rawdata-mappen finns mappar med namn som hour=17 och hour=18. Förekomsten av dessa mappar indikerar att rådata genereras på din dator och lagras i blob-lagring. Du bör se CSV-filer med ändliga storlekar i MB i dessa mappar.
* Det sista steget i pipelinen är att skriva data (till exempel förutsägelser från maskininlärning) till SQL Database. Du kan behöva vänta högst tre timmar på att data ska visas i SQL Database. Ett sätt att övervaka hur mycket data som är tillgängligt i SQL Database är via [Azure-portalen](https://portal.azure.com/). Leta upp SQL DATABASES ![SQL-ikonen](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) på den vänstra panelen och klicka på den. Leta sedan upp din databas **pmaintenancedb** och klicka på den. Klicka på HANTERA på nästa sida längst ned.
   
    ![Ikonen Hantera](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Här kan du klicka på Ny fråga och fråga efter antalet rader (till exempel välj count(*) från PMResult). När databasen växer bör antalet rader i tabellen öka.

## <a name="power-bi-dashboard"></a>Power BI-instrumentpanel

Konfigurera en Power BI-instrumentpanel för att visualisera dina Azure Stream Analytics-data (heta sökvägar) och batchförutsägelsresultat från Azure-maskininlärning (kall sökväg).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurera instrumentpanelen för kall sökväg
I den kalla banan datapipeline, målet är att få prediktiva RUL (återstående livslängd) för varje flygplansmotor när den är klar en flygning (cykel). Förutsägelseresultatet uppdateras var tredje timme för att förutsäga de flygplansmotorer som har avslutat en flygning under de senaste 3 timmarna.

Power BI ansluter till en Azure SQL-databas som datakälla, där förutsägelseresultaten lagras. 

Obs! 
1.    När du distribuerar din lösning visas en förutsägelse i databasen inom 3 timmar. Pbix-filen som medföljde hämtningen av Generator innehåller vissa frödata så att du kan skapa Power BI-instrumentpanelen direkt. 
2.    I det här steget är förutsättningen att ladda ner och installera den fria programvaran [Power BI-skrivbordet](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Följande steg hjälper dig hur du ansluter pbix-filen till SQL-databasen som snurrades upp vid tidpunkten för lösningsdistribution som innehåller data (till exempel förutsägelseresultat) för visualisering.

1. Hämta databasautentiseringsuppgifterna.
   
   Du behöver **databasservernamn, databasnamn, användarnamn och lösenord** innan du går vidare till nästa steg. Här är stegen för att guida dig hur du hittar dem.
   
   * När **"Azure SQL Database"** på din lösning mall diagram blir grön, klicka på den och klicka sedan på **"Öppna".**
   * Du ser en ny webbläsarflik/ett nytt fönster som visar Azure-portalsidan. Klicka på **"Resursgrupper"** på den vänstra panelen.
   * Välj den prenumeration du använder för att distribuera lösningen och välj sedan **\_YourSolutionName ResourceGroup**.
   * På den nya ![popup-panelen klickar](./media/predictive-maintenance-technical-guide/icon-sql.png) du på SQL-ikonen för att komma åt databasen. Databasnamnet ligger bredvid den här ikonen (till exempel **"pmaintenancedb"**) och **databasservernamnet** visas under egenskapen Servernamn och bör se ut **ungefär som YourSolutionName.database.windows.net**.
   * Databasens **användarnamn** och **lösenord** är samma som användarnamnet och lösenordet som tidigare registrerats under distributionen av lösningen.
2. Uppdatera datakällan för rapportfilen för kall sökväg med Power BI Desktop.
   
   * Dubbelklicka på filen **\\PowerBI PredictiveMaintenanceAerospace.pbix** i mappen där du hämtade och packade upp generatorfilen. Om du ser några varningsmeddelanden när du öppnar filen ignorerar du dem. Klicka på **Redigera frågor**högst upp i filen .
     
     ![Redigera frågor](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Du ser två tabeller, **RemainingUsefulLife** och **PMResult**. Markera den första ![tabellen och](./media/predictive-maintenance-technical-guide/icon-query-settings.png) klicka på Ikonen Frågeinställningar **bredvid Källan** under **TILLÄMPADE STEG** på panelen **Frågeinställningar.** Ignorera alla varningsmeddelanden som visas.
   * I popup-fönstret, ersätta **"Server"** och **"Databas"** med din egen server och databasnamn, och klicka sedan på **"OK".** För servernamn kontrollerar du att du anger porten 1433 (**YourSolutionName.database.windows.net, 1433**). Lämna fältet Databas som **pmaintenancedb**. Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa popup-fönster visas två alternativ i den vänstra rutan (**Windows** och **Databas**). Klicka på **"Databas"**, fyll i ditt **användarnamn** och **"lösenord"** (användarnamnet och lösenordet som du angav när du först distribuerade lösningen och skapade en Azure SQL-databas). Kontrollera alternativet databasnivå ***i Välj vilken nivå som ska användas på.*** Klicka sedan på **"Anslut".**
   * Klicka på den andra tabellen **PMResult** klicka sedan på ![Navigeringsikonen](./media/predictive-maintenance-technical-guide/icon-navigation.png) bredvid **"Källa"** under **"APPLIED STEPS"** på rätt **"Frågeinställningar"** panel, och uppdatera server och databasnamn som i ovanstående steg och klicka på OK.
   * När du har guidats tillbaka till föregående sida stänger du fönstret. Ett meddelande visas - klicka på **Använd**. Klicka slutligen på knappen **Spara** för att spara ändringarna. Power BI-filen har nu upprättat anslutning till servern. Om visualiseringarna är tomma måste du avmarkera markeringarna på visualiseringarna för att visualisera alla data genom att klicka på radergummit i det övre högra hörnet av förklaringarna. Använd uppdateringsknappen för att återspegla nya data på visualiseringarna. Inledningsvis ser du bara sådddata på dina visualiseringar som datafabriken är schemalagd att uppdatera var tredje timme. Efter 3 timmar visas nya förutsägelser som återspeglas i dina visualiseringar när du uppdaterar data.
3. (Valfritt) Publicera instrumentpanelen för kall sökväg till [Power BI online](https://www.powerbi.com/). Det här steget behöver ett Power BI-konto (eller Office 365-konto).
   
   * Klicka på **Publicera** och några sekunder senare visas ett fönster med "Publicering till Power BI Success!" med en grön bock. Klicka på länken nedan "Öppna PredictiveMaintenanceAerospace.pbix i Power BI". Mer information finns i [Publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Så här skapar du **+** en ny instrumentpanel: klicka på tecknet bredvid avsnittet **Instrumentpaneler** i den vänstra rutan. Ange namnet "Predictive Maintenance Demo" för den här nya instrumentpanelen.
   * När du har öppnat ![rapporten](./media/predictive-maintenance-technical-guide/icon-pin.png) klickar du på PIN-ikonen för att fästa alla visualiseringar på instrumentpanelen. Mer information finns i [Fästa en panel på en Power BI-instrumentpanel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrumentpanelssidan och justera storleken och platsen för dina visualiseringar och redigera deras titlar. Information om hur du redigerar panelerna finns i [Redigera en panel – ändra storlek, flytta, byta namn på, fästa, ta bort, lägga till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är en exempelinstrumentpanel med några kalla banvisningar fästa.  Beroende på hur länge du kör datageneratorn kan dina siffror på visualiseringarna vara olika.
     <br/>
     ![Slutlig vy](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Om du vill schemalägga uppdatering av data håller du musen över **datauppsättningen PredictiveMaintenanceAerospace,** klickar på ![Ikonen](./media/predictive-maintenance-technical-guide/icon-elipsis.png) För ellips och väljer sedan **Schemauppdatering**.
     <br/>
     > [!NOTE]
     > Om du ser ett varningsmeddelande klickar du på **Redigera autentiseringsuppgifter** och kontrollerar att databasautentiseringsuppgifterna är desamma som de som beskrivs i steg 1.
     <br/>
     ![Schemalägg uppdatering](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Expandera avsnittet **Uppdatera schema.** Aktivera "Håll dina data uppdaterade".
     <br/>
   * Schemalägg uppdateringen baserat på dina behov. Mer information finns [i Uppdatera data i Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Instrumentpanelen för en aktiv sökväg för installation
Följande steg hjälper dig hur du visualiserar datautdata från Stream Analytics-jobb som genererades vid tidpunkten för lösningsdistributionen. Ett [Power BI-onlinekonto](https://www.powerbi.com/) krävs för att utföra följande steg. Om du inte har ett konto kan du [skapa ett](https://powerbi.microsoft.com/pricing).

1. Lägg till Power BI-utdata i Azure Stream Analytics (ASA).
   
   * Du måste följa instruktionerna i [Azure Stream Analytics & Power BI: En analysinstrumentpanel för realtidssynlighet för strömmande data för](../../stream-analytics/stream-analytics-power-bi-dashboard.md) att ställa in utdata för ditt Azure Stream Analytics-jobb som din Power BI-instrumentpanel.
   * ASA-frågan har tre utgångar som är **flygplansövervakare,** **aircraftalert**och **flygtid.** Du kan visa frågan genom att klicka på frågefliken. När du lägger till den första utgången **(aircraftmonitor)** kontrollerar du att **utdataaliaset**är **datasetnamn** och **tabellnamn** desamma (**aircraftmonitor**). Upprepa stegen för att lägga till utgångar för **flygplanalert**och **flygbytimmar**. När du har lagt till alla tre utdatatabellerna och startat ASA-jobbet bör du få ett bekräftelsemeddelande ("Starting Stream Analytics job maintenancesa02asapbi lyckades").
2. Logga in på [Power BI online](https://www.powerbi.com)
   
   * På avsnittet Dataset till vänster i Min arbetsyta ***namnger DATASET*** **aircraftmonitor,** **aircraftalert**och **flygbytimmar.** Det här är de strömmande data som du har drivit från Azure Stream Analytics i föregående steg. Datauppsättningen **flygbytimmar** kanske inte visas samtidigt som de andra två datauppsättningarna på grund av sql-frågans natur bakom den. Det bör dock dyka upp efter en timme.
   * Kontrollera att ***fönstret Visualiseringar*** är öppet och att det visas till höger på skärmen.
3. När du har data som flödar in i Power BI kan du börja visualisera strömmande data. Nedan finns en exempelinstrumentpanel med några aktiva sökvägsvisualiseringar fästa på den. Du kan skapa andra instrumentpanelspaneler baserat på lämpliga datauppsättningar. Beroende på hur länge du kör datageneratorn kan dina siffror på visualiseringarna vara olika.

    ![Instrumentpanelsvy](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Här är några steg för att skapa en av brickorna ovan - "Fleet View of Sensor 11 vs. Tröskel 48,26" kakel:
   
   * Klicka på **datauppsättningen aircraftmonitor** på avsnittet Datauppsättningar på vänster panel.
   * Klicka på ikonen **Linjediagram.**
   * Klicka på **Bearbetad** i **fältfönstret** så att den visas under "Axel" i fönstret **Visualiseringar.**
   * Klicka på "s11" och\_"s11 alert" så att de båda visas under "Värden". Klicka på den lilla pilen bredvid **s11** och **\_s11-aviseringen**, ändra "Summa" till "Medel".
   * Klicka på **SPARA** högst upp och namnge rapporten "aircraftmonitor". Rapporten "aircraftmonitor" visas i avsnittet **Rapporter** i **rutan Navigatör** till vänster.
   * Klicka på ikonen **Fäst visuellt** längst upp till höger i det här linjediagrammet. Ett fönster med fäst på instrumentpanelen kan visas så att du kan välja en instrumentpanel. Välj "Prediktiv underhållsdemo" och klicka sedan på "Pin".
   * Håll muspekaren över den här panelen på instrumentpanelen, klicka på "redigera"-ikonen längst upp till höger för att ändra titeln till "Fleet View of Sensor 11 vs. Threshold 48.26" och underrubrik till "Genomsnitt över flottan över tid".

## <a name="delete-your-solution"></a>Ta bort din lösning
Se till att du stoppar datageneratorn när du inte aktivt använder lösningen eftersom datageneratorn körs medför högre kostnader. Ta bort lösningen om du inte använder den. Om du tar bort lösningen tas alla komponenter som etablerats i prenumerationen bort när du har distribuerat lösningen. Om du vill ta bort lösningen klickar du på lösningsnamnet på den vänstra panelen i lösningsmallen och klickar sedan på **Ta bort**.

## <a name="cost-estimation-tools"></a>Verktyg för kostnadsuppskattning
Följande två verktyg finns tillgängliga för att hjälpa dig att bättre förstå de totala kostnaderna för att köra predictive Maintenance for Aerospace Solution Template i din prenumeration:

* [Microsoft Azure-kostnadsuppskattningsverktyg (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure-kostnadsuppskattningsverktyg (skrivbord)](https://www.microsoft.com/download/details.aspx?id=43376)

