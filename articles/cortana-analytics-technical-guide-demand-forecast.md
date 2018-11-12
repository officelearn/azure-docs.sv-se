---
title: Kräver en prognos i energi teknisk Guide
description: En teknisk guide till lösningsmallen med Microsoft Cortana Intelligence för begäran Prognostisera i energi.
services: machine-learning
author: garyericson
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/16/2016
ms.author: garye
ms.openlocfilehash: e18e1fb3e97dd9f846ee71be4f0fbb66aeca3d88
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238870"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Teknisk guide till lösningsmallen Cortana Intelligence för begäran Prognostisera i energi
## <a name="overview"></a>**Översikt**
Lösningsmallar är utformade att skynda på processen för att skapa en E2E-demo ovanpå Cortana Intelligence Suite. En mall för distribuerade etablerar din prenumeration med nödvändig komponent för Cortana Intelligence och skapa relationer mellan. Det lägger även datapipelinen med exempeldata komma genereras från ett program för simulering av data. Ladda ned datasimulatorn från länken och installera den på din lokala dator, finns i filen Viktigt.txt anvisningar om hur du använder simulatorn. Data som genereras från simulatorn hydrates datapipeline och starta genererar förutsägelser för maskininlärning, som sedan kan visualiseras i Power BI-instrumentpanelen.

Lösningsmallen finns [här](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Distributionsprocessen vägleder dig genom stegen för att ställa in dina autentiseringsuppgifter för lösningen. Kontrollera att du registrerar dessa autentiseringsuppgifter, till exempel lösningens namn, användarnamn och lösenord som du anger under distributionen.

Målet med det här dokumentet är att förklara Referensarkitektur och olika komponenter som etablerats i din prenumeration som en del av den här lösningsmallen. Dokumentet berättar också om hur du ersätter exempeldata, med verkliga data på egen hand för att kunna se insikter/förutsägelser från du vann data. Dessutom kan de dokumentet berättar om delar av lösningsmallen som kommer att ändras om du vill anpassa lösningen med dina egna data. Anvisningar om hur du skapar Power BI-instrumentpanelen för den här lösningsmallen finns i slutet.

## <a name="details"></a>**Detaljer**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arkitekturen beskrivs
När lösningen har distribuerats kan olika Azure-tjänster i Cortana Analytics Suite aktiveras (det vill säga Event Hub, Stream Analytics, HDInsight, Data Factory, Machine Learning, *etc.*). I arkitekturdiagrammet visas, på en hög nivå hur Efterfrågeprognostisering för energi lösningsmallen konstrueras från slutpunkt till slutpunkt. Du kan undersöka dessa tjänster genom att klicka på dem på mallen lösningsdiagrammet skapat med distribution av lösningen. I följande avsnitt beskrivs varje del.

## <a name="data-source-and-ingestion"></a>**Datakällan och inmatning**
### <a name="synthetic-data-source"></a>Syntetisk datakälla
I den här mallen skapas den använda datakällan från ett program som du hämtar och kör lokalt efter slutförd distribution. Du hittar anvisningar för att ladda ned och installera det här programmet i fältet egenskaper när du väljer den första noden energi prognoser Datasimulatorn med namnet på mallen diagrammet. Det här programmet feeds den [Azure Event Hub](#azure-event-hub) tjänst med datapunkter eller händelser som används i resten av lösningsflödet.

Händelsen generation programmet fyller Azure Event Hub endast medan det körs på datorn.

### <a name="azure-event-hub"></a>Azure händelsehubb
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) tjänsten är mottagaren av indata som angetts av syntetiska datakällan som beskrivs.

## <a name="data-preparation-and-analysis"></a>**Förberedelse av data och analys**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) används för att tillhandahålla analys på Indataströmmen från nära realtid den [Azure Event Hub](#azure-event-hub) -tjänsten och publicera resultaten på en [Power BI](https://powerbi.microsoft.com)instrumentpanelen som arkiverar alla inkommande råhändelser i [Azure Storage](https://azure.microsoft.com/services/storage/) för senare bearbetning av den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service.

### <a name="hdinsight-custom-aggregation"></a>HDInsight anpassad aggregering
Tjänsten Azure HDInsight används till att köra [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript (som orkestreras via Azure Data Factory) att tillhandahålla sammansställningar av råhändelser som arkiverats med hjälp av Azure Stream Analytics-tjänsten.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) tjänsten används (orkestreras via Azure Data Factory) till att göra prognoser för framtida energiförbrukningen för en viss region givet mottagna indata.

## <a name="data-publishing"></a>**Publicering av data**
### <a name="azure-sql-database-service"></a>Azure SQL Database-tjänsten
Den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) används för att lagra (hanteras av Azure Data Factory) förutsägelser som tagits emot av tjänsten Azure Machine Learning som förbrukas i den [Power BI](https://powerbi.microsoft.com) instrumentpanelen.

## <a name="data-consumption"></a>**Dataförbrukning**
### <a name="power-bi"></a>Power BI
Den [Power BI](https://powerbi.microsoft.com) används för att visa en instrumentpanel som innehåller aggregeringar som tillhandahålls av den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) tjänsten samt begäran förutspå resultaten lagras i [Azure SQL Databasen](https://azure.microsoft.com/services/sql-database/) som skapas med hjälp av den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) service. Anvisningar om hur du skapar Power BI-instrumentpanelen för den här lösningsmallen finns i följande avsnitt.

## <a name="how-to-bring-in-your-own-data"></a>**Hur du hanterar dina egna data**
Det här avsnittet beskrivs hur du hanterar dina egna data till Azure och vilka områden kräver ändringar för de data du sätta i den här arkitekturen.

Det är osannolikt att alla datauppsättningar som du kan aktivera matchar den datauppsättning som används för den här lösningsmallen. Förstå dina data och kraven är avgörande i hur du ändrar den här mallen ska fungera med dina egna data. Om inte har använt Azure Machine Learning-tjänsten, du kan få en introduktion till den med hjälp av exemplet i [hur du skapar ditt första experiment](machine-learning/studio/create-experiment.md).

I följande avsnitt beskrivs i avsnitt på den mall som kräver ändringar när en ny datauppsättning införs.

### <a name="azure-event-hub"></a>Azure händelsehubb
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) tjänsten är allmänna, så att data kan publiceras till hubben i CSV eller JSON-format. Ingen särskild bearbetning sker i Azure Event Hub, men det är viktigt att du förstår de data som matas in.

Det här dokumentet beskriver inte hur att mata in dina data, men något enkelt skicka händelser eller data till en Azure-Händelsehubb med hjälp av den [Event Hub API](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) används för att tillhandahålla analys i nära realtid genom att läsa från dataströmmar och skickar data till valfritt antal källor.

Efterfrågeprognostisering för energi lösningsmallen består Azure Stream Analytics-fråga av två underfrågor, var och en förbrukar händelse från Azure Event Hub-tjänsten som indata och utdata behöva två olika platser. Dessa utdata består av en Power BI-datauppsättning och en Azure-lagringsplats.

Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) fråga kan hittas genom:

* Logga in på den [Azure-portalen](https://portal.azure.com/)
* Hitta stream analytics-jobb ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) som genererades när lösningen har distribuerats. En är till för att skicka data till blob storage (till exempel mytest1streaming432822asablob) och den andra är för att skicka data till Power BI (till exempel mytest1streaming432822asapbi).
* Att välja

  * ***INDATA*** kan du se frågan indata
  * ***FRÅGA*** kan du se frågan själva
  * ***Matar ut*** att visa olika utdata

Information om Azure Stream Analytics frågekonstruktion finns i den [referens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I den här lösningen tillhandahåller Azure Stream Analytics-jobb som producerar datauppsättningen med nära realtidsanalys information om inkommande dataström till en Power BI-instrumentpanel som en del av den här lösningsmallen. Eftersom det finns implicit kunskap om dataformat för inkommande, skulle de här frågorna behöva ändras baserat på dataformat.

Andra Azure Stream Analytics-jobbet matar ut alla [Händelsehubb](https://azure.microsoft.com/services/event-hubs/) händelser till [Azure Storage](https://azure.microsoft.com/services/storage/) och därför kräver inga ändringar oavsett dina dataformat som fullständig händelseinformationen strömmas till lagring.

### <a name="azure-data-factory"></a>Azure Data Factory
Den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) dirigerar förflyttning och bearbetning av data. I Efterfrågeprognostisering för energi lösningsmallen data factory består av 12 [pipelines](data-factory/concepts-pipelines-activities.md) som flytta och bearbeta data med olika tekniker.

  Du kan komma åt din data factory genom att öppna noden Data Factory längst ned i mallen lösningsdiagrammet skapat med distribution av lösningen. Du ser att datafabriken i Azure-portalen. Om du ser fel under dina datauppsättningar, kan du ignorera de som de är på grund av data factory som distribueras innan datageneratorn startades. Dessa fel förhindrar inte din data factory från att fungera.

Det här avsnittet beskrivs nödvändiga [pipelines](data-factory/concepts-pipelines-activities.md) och [aktiviteter](data-factory/concepts-pipelines-activities.md) i den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Följande bild är i diagramvyn på lösningen:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Fem av pipelines för den här fabriken innehåller [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript som används för att partitionera och aggregera data. När anges är skript som finns i den [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under konfigurationen. Var de är: demandforecasting\\\\skriptet\\\\hive\\ \\ (eller https://[Your lösning name].blob.core.windows.net/demandforecasting).

Liknar den [Azure Stream Analytics](#azure-stream-analytics-1) frågor, den [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripten har implicit kunskap om dataformat för inkommande, de här frågorna skulle behöva ändras beroende på dataformat och [funktionstekniker](machine-learning/team-data-science-process/create-features.md) krav.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript till sammanställda strömmas i begäran data var tionde sekund i omformaren nivå till varje timme regionnivå och placera i [Azure Storage](https://azure.microsoft.com/services/storage/) via Azure Stream Analytics-jobbet.

Den [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för detta partitionering är ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller två aktiviteter:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör ett Hive-skript för att aggregera data per timme historik begäran i omformaren nivå till varje timme regionnivå och placera i Azure Storage under Azure Stream Analytics-jobb
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar sammanställda data från Azure Storage-blob till Azure SQL-databasen som har etablerats som en del av lösningen mall installationen.

Den [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för den här uppgiften är ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Dessa [pipelines](data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter och vars slutresultatet är poängsatta förutsägelser från Azure Machine Learning-experiment som är associerade med den här lösningsmallen. De är nästan identiska förutom dem endast hanterar annan region, som görs av olika RegionID som skickades i ADF pipelinen och hive-skriptet för varje region.  
De aktiviteter i denna pipeline är:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör ett Hive-skript för att utföra aggregeringar och funktionstekniker som krävs för Azure Machine Learning-experiment. Hive-skript för den här uppgiften är respektive ***PrepareMLInputRegionX.hql***.
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultaten från den [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivitet till en enda Azure Storage-blob som kan vara åtkomst av den [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet som anropar Azure Machine Learning-experiment, vilket resulterar i resultaten de placeras i en enda Azure Storage-blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [kopia](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet av Azure Machine Learning-experiment från respektive ***MLScoringRegionXPipeline***till Azure SQL-databas som har etablerats som en del av lösningen mall installationen.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [kopia](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar data aggregerade pågående begäran från ***LoadHistoryDemandDataPipeline*** till Azure SQL Databas som har etablerats som en del av lösningen mall installationen.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enda aktivitet – en [kopia](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar referensdata för Region/understation/Topologygeo som överförs till Azure Storage-blob som en del av lösningsmallen installationen till Azure SQL-databas som har etablerats som en del av lösningen mall installationen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentera används för den här lösningsmallen erbjuder förutsägelser av begäran av region. Experimentet är specifik för den datauppsättning som används och därför kräver ändras eller ersättas som är specifika för de data som är online i.

## <a name="monitor-progress"></a>**Övervaka förloppet**
När Datageneratorn startas pipelinen börjar hämta hydrerat och de olika komponenterna i din lösning starta starta i åtgärden följande kommandon som utfärdas av Data Factory. Det finns två sätt som du kan övervaka pipelinen.

1. Kontrollera data från Azure Blob Storage.

    En av Stream Analytics-jobb skriver inkommande rådata till blob storage. Om du klickar på **Azure Blob Storage** komponent i din lösning från skärmen du har distribuerat lösningen och klicka sedan på **öppna** i den högra panelen kommer du till den [Azure portalen](https://portal.azure.com). När det, klickar du på **Blobar**. Nästa panelen visas en lista över behållare. Klicka på **”energysadata”**. I nästa panel visas den **”demandongoing”** mapp. I mappen \data du se mappar med namn, t.ex datum = 2016-01-28 osv. Om du ser dessa mappar, betyder det att rådata är har som genereras på datorn och lagras i blob storage. Du bör se filer som ska ha begränsad storlek i MB i dessa mappar.
2. Kontrollera data från Azure SQL Database.

    Det sista steget i pipelinen är att skriva data (till exempel förutsägelser från maskininlärning) i SQL-databas. Du kan behöva vänta högst två timmar innan data visas i SQL-databas. Ett sätt att övervaka hur mycket data är tillgängliga i SQL-databasen är via [Azure-portalen](https://portal.azure.com/). Leta upp SQL-databaser på den vänstra panelen![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) och klicka på den. Sedan leta upp din databas (d.v.s. demo123456db) och klicka på den. På nästa sida under **”Anslut till databasen”** klickar du på **”kör Transact-SQL-frågor mot SQL-databasen”**.

    Här kan du klicka på ny fråga och fråga efter hur många rader (till exempel ”Välj count(*) från DemandRealHourly)” att län, öka antalet rader i tabellen.)
3. Kontrollera data från Power BI-instrumentpanel.

    Du kan ställa in Power BI-instrumentpanel för heta sökvägen för att övervaka inkommande rådata. Följ anvisningarna i avsnittet ”Power BI-instrumentpanel”.

## <a name="power-bi-dashboard"></a>**Power BI-instrumentpanel**
### <a name="overview"></a>Översikt
Det här avsnittet beskrivs hur du ställer in Power BI-instrumentpanelen för att visualisera dina data i realtid från Azure stream analytics (het sökväg), samt förutspå resultaten från Azure machine learning-(kall sökväg).

### <a name="setup-hot-path-dashboard"></a>Konfigurera heta sökvägen instrumentpanel
Följande steg hjälper dig hur du visualisera data i realtid utdata från Stream Analytics-jobb som genererades vid tidpunkten för distribution. En [Power BI online](http://www.powerbi.com/) konto krävs för att utföra följande steg. Om du inte har ett konto, kan du [skapar ett](https://powerbi.microsoft.com/pricing).

1. Lägg till Power BI-utdata i Azure Stream Analytics (ASA).

   * Du måste följa anvisningarna i [Azure Stream Analytics och Power BI: en instrumentpanel för analys i realtid för realtid för strömmande data](stream-analytics/stream-analytics-power-bi-dashboard.md) att konfigurera utdata för din Azure Stream Analytics-jobb som Power BI-instrumentpanel .
   * Leta upp stream analytics-jobbet i din [Azure-portalen](https://portal.azure.com). Namnet på jobbet ska vara: YourSolutionName + ”streamingjob” + slumpmässigt nummer + ”asapbi” (dvs. demostreamingjob123456asapbi).
   * Lägg till en Power BI-utdata för ASA-jobbet. Ange den **utdata Alias** som **'PBIoutput'**. Ange din **Datamängdsnamn** och **tabellnamn** som **'EnergyStreamData'**. När du har lagt till utdata, klickar du på **”Start”** längst ned på sidan för att starta Stream Analytics-jobbet. Du bör få ett bekräftelsemeddelande (till exempel ”från stream analytics-jobbet myteststreamingjob12345asablob lyckades”).
2. Logga in på [Power BI online](http://www.powerbi.com)

   * På den vänstra panelen datauppsättningar avsnitt i Min arbetsyta bör du kunna se en ny datauppsättning som visar på den vänstra panelen för Power BI. Det här är den strömmande data som du push-överfört från Azure Stream Analytics i föregående steg.
   * Kontrollera att den ***visualiseringar*** fönstret är öppet och visas på höger sida av skärmen.
3. Skapa panelen ”begäran av Timestamp”:

   * Klicka på datauppsättningen **'EnergyStreamData'** på den vänstra panelen datauppsättningar avsnittet.
   * Klicka på **”linjediagram”** ikonen ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Klicka på ”EnergyStreamData” i **fält** panelen.
   * Klicka på **”Timestamp”** och se till att den visar under ”axel”. Klicka på **”inläsning”** och se till att den visar under ”Values”.
   * Klicka på **spara** högst upp och namn på rapporten som ”EnergyStreamDataReport”. Rapporten med namnet ”EnergyStreamDataReport” visas i rapportavsnittet i navigeringsfönstret till vänster.
   * Klicka på **”PIN-kod Visual”** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) -ikonen i övre högra hörnet av det här linjediagrammet, ett ”Fäst på instrumentpanelen” fönster kan visas där du kan välja en instrumentpanel. ”EnergyStreamDataReport” och sedan klicka på ”PIN-kod”.
   * Hovra med musen över den här panelen på instrumentpanelen på ”Redigera”-ikonen i övre högra hörnet för att ändra dess rubrik som ”begäran av Timestamp”
4. Skapa andra instrumentpaneler som baseras på lämpliga datauppsättningar. Instrumentpanelsvyn för slutlig: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Konfigurera kalla sökvägen instrumentpanel
I kalla sökvägen datapipeline är viktigt målet att hämta prognosen för efterfrågan för varje region. Powerbi ansluter till en Azure SQL-databas som datakälla, där de förutsagda resultaten som lagras.

> [!NOTE]
> 1) Det tar några timmar att samla in tillräckligt prognoser resultat för instrumentpanelen. Vi rekommenderar att du börjar den här processen 2 – 3 timmar efter att du lunch datageneratorn. 2) i det här steget är nödvändiga att ladda ned och installera den kostnadsfria programvaran [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Hämta autentiseringsuppgifterna på databasen.

   Du behöver **databasen servernamnet, databasnamnet, användarnamnet och lösenordet** innan du fortsätter till nästa steg. Här följer stegen för att guida dig hur du hittar dem.

   * En gång **”Azure SQL Database”** på din mall diagram blir grön, klickar du på den och klicka sedan på **”öppet”**. Vägleds du till Azure-portalen och din information databassida öppnas också.
   * Du kan hitta ett ”databas”-avsnitt på sidan. Den visar ut databasen som du har skapat. Namnet på din databas bör vara **”din lösningsnamn + slumpmässiga numret +” db ””** (till exempel ”mytest12345db”).
   * Klicka på din databas i den nya frigörs panelen, hittar du din Databasservernamnet längst upp. Namnet på servern databasen ska vara `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (till exempel ”mytest12345.database.windows.net,1433”).
   * Din databas **användarnamn** och **lösenord** är samma som användarnamnet och lösenordet tidigare registreras under distributionen av lösningen.
2. Uppdatera datakällan för Power BI-filen kalla sökvägen

   * Kontrollera att du har installerat den senaste versionen av [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * I den **”DemandForecastingDataGeneratorv1.0”** mapp som du har hämtat, dubbelklickar du på den **”Power BI Template\DemandForecastPowerBI.pbix'** fil. Inledande visualiseringar baserade på dummy data. **Obs:** om du ser ett felmeddelande, se till att du har installerat den senaste versionen av Power BI Desktop.

     När du öppnar den överst i filen, klickar du på **redigera frågor**. I frigörs fönster, dubbelklickar du på **'Source'** i den högra rutan.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * I frigörs fönster, ersätter **”Server”** och **”databas”** med din egen och databasnamn och sedan på **”OK”**. Kontrollera att du anger porten 1433 för servernamnet, (**YourSolutionName.database.windows.net, 1433**). Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa frigörs fönstret, ser du två alternativ i det vänstra fönstret (**Windows** och **databasen**). Klicka på **”databas”**, Fyll i din **”användarnamn”** och **”Password”** (detta är det användarnamn och lösenord som du angav när du först distribueras lösningen och skapat en Azure SQL-databas). I ***väljer vilken nivå dessa inställningar ska tillämpas***, kontrollera nivån databasalternativ. Klicka sedan på **”Anslut”**.
   * Stäng fönstret när du är interaktiva tillbaka till föregående sida. Ett meddelande POP ut – Klicka på **tillämpa**. Klicka slutligen på den **spara** för att spara ändringarna. Power BI-filen har nu upprätta anslutningen till servern. Om dina visualiseringar är tomma, kontrollera att du radera markeringarna på visualiseringar i visualisera alla data genom att klicka på raderingsikonen i det övre högra hörnet av seriemönstren. Använd uppdateringsknappen ska visa nya data på visualiseringar. Första gången se du bara seed-data på dina visualiseringar som data factory schemaläggs för uppdatering var tredje timme. När 3 timmar Se nya förutsägelser som visas i dina visualiseringar när du uppdaterar data.
3. (Valfritt) Publicera instrumentpanelen kalla sökvägen till [Power BI online](http://www.powerbi.com/). Observera att det här steget en Power BI-konto (eller Office 365-konto).

   * Klicka på **”publicera”** och några sekunder senare ett fönster visas med ”publicering till Power BI lyckades”! med en grön bockmarkering. Klicka på länken nedan ”öppna demoprediction.pbix i Powerbi”. Detaljerade anvisningar finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Att skapa en ny instrumentpanel: Klicka på den **+** logga bredvid den **instrumentpaneler** avsnitt i det vänstra fönstret. Ange namnet ”begäran prognoser Demo” för den här nya instrumentpanelen.
   * När du öppnar rapporten klickar du på ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) att fästa alla visualiseringar på instrumentpanelen. Detaljerade anvisningar finns i [fästa en panel på en Power BI-instrumentpanel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrumentpanelssidan och justera storleken och platsen för dina visualiseringar och redigera sina titlar. Detaljerade anvisningar om hur du redigerar dina paneler finns i [redigera en panel – ändra storlek, flytta, Byt namn på, PIN-kod, ta bort, lägga till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är en exempel-instrumentpanel med vissa kalla sökvägen visualiseringar fästa på den.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Valfritt) Schemalägga en uppdatering av datakällan.

   * Att schemalägga en uppdatering av data, håller du muspekaren över den **EnergyBPI-slutlig** datauppsättning, klickar du på ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) och välj sedan **Schemalägg uppdatering**.
     **Obs:** om du ser en varning massage, klickar du på **redigera autentiseringsuppgifter** och se till att dina autentiseringsuppgifter på databasen är samma som de som beskrivs i steg 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Expandera den **Schemalägg uppdatering** avsnittet. Aktivera ”Håll dina data aktuella”.
   * Schemalägga en uppdatering efter dina behov. Mer information finns i [datauppdatering i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Ta bort din lösning**
Se till att du stoppar datageneratorn när du inte aktivt använder lösningen som att köra datageneratorn medför högre kostnader. Ta bort lösningen om du inte använder den. Tar bort lösningen tar du bort alla komponenter som etablerats i din prenumeration när du distribuerade lösningen. Om du vill ta bort lösningen klickar du på din lösningsnamn i den vänstra panelen lösningsmallen och klicka på Ta bort.

## <a name="cost-estimation-tools"></a>**Kostnad uppskattning av verktyg**
Följande två verktyg är tillgängliga för att bättre förstå totala kostnader för att köra Efterfrågeprognostisering för energi lösningsmallen i din prenumeration:

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator Tool (skrivbord)](https://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Bekräftelser**
Den här artikeln har skrivits av dataexpert Yijing Chen och programvaruutvecklare Qiu Min på Microsoft.
