---
title: "Kräver prognos i energi tekniska Guide | Microsoft Docs"
description: "En teknisk guide till Lösningsmall med Microsoft Cortana Intelligence för begäran vid en prognos i energiförbrukning."
services: cortana-analytics
documentationcenter: 
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: ed2a17fd735c1b0e67cbf5d08450d36620d4c857
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Teknisk guide till Cortana Intelligence lösning mallen för begäran vid en prognos i energi
## <a name="overview"></a>**Översikt**
Lösningsmallar är avsedda att skynda på processen för att skapa en E2E demo ovanpå Cortana Intelligence Suite. En mall för distribuerade etablerar prenumerationen med nödvändiga Cortana Intelligence-komponenten och skapa relationer mellan. Det lägger också pipeline data med exempeldata komma genereras från ett simuleringen program. Hämta data simulatorn från länken och installera den på den lokala datorn, finns i Viktigt.txt anvisningar om hur du använder simulatorn. Data som genereras av simulatorn hydrates i data pipeline och starta genererar maskininlärning prognoser, vilket kan sedan visualiserade på Power BI-instrumentpanelen.

Mallen lösning hittar [här](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Distributionsprocessen vägleder dig genom stegen för att ställa in autentiseringsuppgifterna lösning. Kontrollera att du registrera dessa autentiseringsuppgifter, till exempel lösningens namn, användarnamn och lösenord som du anger under distributionen.

Målet med det här dokumentet är att förklara Referensarkitektur och olika komponenter som har etablerats i din prenumeration som en del av den här lösningen mallen. Dokumentet också talar om hur du ersätter exempeldata, med verkliga data egen för att kunna se insikter/förutsägelser från du vann data. Dessutom kan de dokumentet pratar om delar av Lösningsmall som behöver ändras om du vill anpassa lösningen med dina egna data. Instruktioner om hur du skapar Power BI-instrumentpanelen för den här lösningen mallen finns i slutet.

## <a name="details"></a>**Detaljer**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arkitekturen beskrivs
När lösningen har distribuerats kan olika Azure-tjänster i Cortana Analytics Suite är aktiverat (det vill säga Event Hub, Stream Analytics, HDInsight, Data Factory, Machine Learning *etc.*). Diagram över arkitektur visas på en hög nivå hur begäran prognoser för energi Lösningsmall är uppbyggd från slutpunkt till slutpunkt. Du kan undersöka dessa tjänster genom att klicka på dem på lösningen mallen diagram som skapats med distributionen av lösningen. I följande avsnitt beskrivs varje del.

## <a name="data-source-and-ingestion"></a>**Datakällan och införandet**
### <a name="synthetic-data-source"></a>Syntetiska datakälla
Datakällan som används genereras från ett program som du hämtar och kör lokalt efter slutförd distribution för den här mallen. Du hittar instruktioner för att hämta och installera det här programmet i fältet egenskaper när du väljer den första noden som kallas energi prognoser Data Simulator i lösningen mallen diagrammet. Det här programmet feeds den [Azure Event Hub](#azure-event-hub) med datapunkter, eller händelser som används i resten av flödet lösning.

Händelsen generation programmet fyller Azure Event Hub endast när det körs på datorn.

### <a name="azure-event-hub"></a>Azure Event Hub
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) service är mottagaren av indata som angetts av syntetiska datakällan beskrivs.

## <a name="data-preparation-and-analysis"></a>**Förberedelse av data och analys**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) används för att tillhandahålla nästan analys i realtid på Indataströmmen från den [Azure Event Hub](#azure-event-hub) tjänst och publicera resultatet till en [Power BI](https://powerbi.microsoft.com) instrumentpanelen som arkiverar alla rådata inkommande händelser till den [Azure Storage](https://azure.microsoft.com/services/storage/) tjänsten för senare bearbetning av den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service.

### <a name="hdinsight-custom-aggregation"></a>HDInsight anpassad aggregering
Azure HDInsight-tjänst som används för att köra [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript (styrd av Azure Data Factory) att tillhandahålla aggregeringar för raw-händelser som har arkiverats med Azure Stream Analytics-tjänsten.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) tjänsten används (styrd av Azure Data Factory) så att prognosens på framtida energiförbrukningen för en viss region angivna indata som tagits emot.

## <a name="data-publishing"></a>**Publicering av data**
### <a name="azure-sql-database-service"></a>Azure SQL Database-tjänsten
Den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) används för att lagra (hanteras av Azure Data Factory) förutsägelser som tagits emot av tjänsten Azure Machine Learning som används i den [Power BI](https://powerbi.microsoft.com) instrumentpanelen.

## <a name="data-consumption"></a>**Användning av data**
### <a name="power-bi"></a>Power BI
Den [Power BI](https://powerbi.microsoft.com) används för att visa en instrumentpanel som innehåller aggregeringar som tillhandahålls av den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) tjänsten samt begäran prognos resultat lagras i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som skapas med hjälp av den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) service. Anvisningar om hur du skapar Power BI-instrumentpanelen för den här lösningen-mallen finns i följande avsnitt.

## <a name="how-to-bring-in-your-own-data"></a>**Hur du hanterar i dina egna data**
Det här avsnittet beskrivs hur du hanterar dina egna data till Azure och vilka områden kräver ändringar för de data du sätta i den här arkitekturen.

Det är inte troligt att alla dataset som du kan aktivera matchar datamängden som används för den här lösningen mallen. Förstå dina data och kraven är avgörande för hur du ändrar den här mallen för att arbeta med dina egna data. Om nya Azure Machine Learning-tjänsten, du kan få en introduktion till den med hjälp av exemplet i [skapa ditt första experiment](machine-learning/studio/create-experiment.md).

I följande avsnitt beskrivs i avsnitt på den mall som kräver ändringar när en ny datamängd införs.

### <a name="azure-event-hub"></a>Azure Event Hub
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) service är generisk, så att data kan vara upp till hubben i CSV- eller JSON-format. Ingen särskild bearbetning sker i Azure-Händelsehubb, men det är viktigt att du förstår de data som matas in.

Det här dokumentet beskriver inte hur att mata in data, men ett enkelt skicka händelser eller data till en Azure-Händelsehubb med hjälp av den [Event Hub API](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) används för att tillhandahålla nästan analys i realtid genom att läsa från dataströmmar och skickar data till flera källor.

Den begäran prognoser för energi Lösningsmall består Azure Stream Analytics-fråga av två underfrågor varje förbrukar händelse från Azure Event Hub-tjänsten som indata och utdata behöver två olika platser. Dessa utdata består av en Power BI datauppsättning och en Azure-lagringsplats.

Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) fråga kan hittas av:

* Logga in på den [Azure-portalen](https://manage.windowsazure.com/)
* Hitta stream analytics-jobb ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) som genererades när lösningen har distribuerats. En för att skicka data till blob storage (till exempel mytest1streaming432822asablob) och den andra är för att skicka data till Power BI (till exempel mytest1streaming432822asapbi).
* Att välja

  * ***INDATA*** kan du se frågan indata
  * ***FRÅGAN*** kan du se frågan sig själv
  * ***Matar ut*** att visa olika utdata

Information om Azure Stream Analytics query konstruktionen kan hittas i den [referens för Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I den här lösningen tillhandahålls Azure Stream Analytics-jobbet som producerar datauppsättningen med nästan realtidsanalys information om den inkommande dataströmmen till en Power BI-instrumentpanel som en del av den här lösningen mallen. Eftersom det är implicit kunskap om dataformatet för inkommande, skulle de här frågorna behöva ändras baserat på din dataformat.

Andra Azure Stream Analytics-jobbet matar ut alla [Händelsehubb](https://azure.microsoft.com/services/event-hubs/) händelser till [Azure Storage](https://azure.microsoft.com/services/storage/) och därför kräver inga ändringar oavsett din dataformat som fullständig händelseinformation strömmas till lagring.

### <a name="azure-data-factory"></a>Azure Data Factory
Den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service samordnar rörelser och bearbetning av data. I begäran prognoser för energi Lösningsmall datafabriken består av 12 [pipelines](data-factory/concepts-pipelines-activities.md) att flytta och bearbeta de data som använder olika tekniker.

  Du kan komma åt din data factory genom att öppna noden Data Factory längst ned i lösningen mallen diagram som skapats med distributionen av lösningen. Du kan se datafabriken i Azure-portalen. Om du ser fel under dina datauppsättningar kan ignorera du de som de är på grund av datafabriken som distribueras innan datagenerator startades. Dessa fel förhindrar inte din data factory från att fungera.

Det här avsnittet beskrivs nödvändiga [pipelines](data-factory/concepts-pipelines-activities.md) och [aktiviteter](data-factory/concepts-pipelines-activities.md) som ingår i den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Följande bild är diagramvy för lösningen:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Fem pipelines i den här fabriken innehåller [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript som används för att partitionera och aggregera data. När anges skript finns i den [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under installationen. Deras plats är: demandforecasting\\\\skriptet\\\\hive\\ \\ (eller https://[Your lösning name].blob.core.windows.net/demandforecasting).

Liknar den [Azure Stream Analytics](#azure-stream-analytics-1) frågor, den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript har implicit kunskap om dataformatet för inkommande, de här frågorna behöver ändras baserat på din dataformat och [egenskapsval](machine-learning/team-data-science-process/create-features.md) krav.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript till sammanställd direktuppspelat i begäran data var 10: e sekund i omformaren nivå till varje timme regionnivå och placera i [Azure Storage](https://azure.microsoft.com/services/storage/) via Azure Stream Analytics-jobbet.

Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för uppgiften partitionering är ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller två aktiviteter:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en Hive-skript för att sammanställa timvis historik begäran data i omformaren nivå till varje timme regionnivå och placera i Azure Storage under Azure Stream Analytics-jobbet
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar sammanställda data från Azure Storage blob till Azure SQL-databas som har etablerats som en del av installationen mallen.

Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för den här uppgiften är ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Dessa [pipelines](data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter och vars slutresultatet är poängsatta förutsägelser från Azure Machine Learning-experiment som är associerade med den här lösningen mallen. De är nästan identisk förutom dem endast hanterar annan region, vilket görs av olika RegionID som skickades i ADF-pipeline och hive-skript för varje region.  
Aktiviteter i denna pipeline är:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en Hive-skript för att utföra aggregeringar och egenskapsval som krävs för Azure Machine Learning-experiment. Hive-skript för den här uppgiften är respektive ***PrepareMLInputRegionX.hql***.
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultaten från den [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivitet till en enda Azure Storage blob som kan vara åtkomst av den [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet som anropar Azure Machine Learning-experiment, vilket resulterar i resultatet som placeras i en enda Azure Storage blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet av Azure Machine Learning-experiment från respektive ***MLScoringRegionXPipeline***till Azure SQL-databas som har etablerats som en del av installationen mallen.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar data aggregerade pågående begäran från ***LoadHistoryDemandDataPipeline*** till SQL Azure Databas som har etablerats som en del av installationen mallen.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline CopySubstationDataPipeline, CopyTopologyDataPipeline*
Detta [pipeline](data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar referensdata för Topologygeo-Region/understation som överförs till Azure Storage blob som en del av lösningen mallen installationen av Azure SQL-databas som har etablerats som en del av installationen mallen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentera används för den här lösningen mallen innehåller förutsägelser av begäran av region. Experimentet är specifik för den datamängd som används och därför måste ändras eller ersättas som är specifika för de data som tas i.

## <a name="monitor-progress"></a>**Övervaka förloppet**
När Data Generator har startats pipeline börjar hämta ur och de olika komponenterna i din lösning starta lanseras i åtgärden följande kommandon från Data Factory. Det finns två sätt som du kan övervaka pipeline.

1. Kontrollera om data från Azure Blob Storage.

    En av Stream Analytics-jobb skriver inkommande rådata till blob storage. Om du klickar på **Azure Blob Storage** komponent i lösningen från skärmen du distribuera lösningen och klicka sedan på **öppna** i den högra panelen, det tar dig till den [Azure portalen](https://portal.azure.com). När det, klickar du på på **Blobbar**. I panelen nästa visas en lista över behållare. Klicka på **”energysadata”**. I panelen nästa visas de **”demandongoing”** mapp. I mappen \data du se mappar med namn som datum = 2016-01-28 osv. Om du ser dessa mappar, betyder det att rådata är korrekt som genereras på datorn och lagras i blob storage. Du bör se filer som ska ha begränsad storlek i MB i mapparna.
2. Kontrollera om data från Azure SQL Database.

    Det sista steget i pipeline är att skriva data (till exempel förutsägelser från maskininlärning) i SQL-databasen. Du måste kanske vänta en högsta oftwo timmar för data som visas i SQL-databas. Ett sätt att övervaka hur mycket data som är tillgängliga i SQL-databasen är via [Azure-portalen](https://manage.windowsazure.com/). Leta reda på SQL-databaser på den vänstra panelen![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) och klicka på den. Leta upp din databas (d.v.s. demo123456db) och klicka på den sedan. På nästa sida under **”ansluta till databasen”** klickar du på **”kör Transact-SQL-frågor mot din SQL-databas”**.

    Här kan du klicka på ny fråga och fråga för hur många rader (till exempel ”Välj count(*) från DemandRealHourly)” att län, öka antalet rader i tabellen.)
3. Kontrollera om data från Power BI-instrumentpanelen.

    Du kan ställa in Power BI-instrumentpanel varm sökvägen att övervaka inkommande rådata. Följ anvisningarna i avsnittet ”Power BI-instrumentpanel”.

## <a name="power-bi-dashboard"></a>**Power BI-instrumentpanel**
### <a name="overview"></a>Översikt
Det här avsnittet beskrivs hur du ställer in Power BI-instrumentpanel att visualisera dina data i realtid från Azure stream analytics (varm sökväg) samt prognos resultat från Azure machine learning (kalla sökväg).

### <a name="setup-hot-path-dashboard"></a>Installationsprogrammet varm sökväg instrumentpanelen
Följande steg hjälper dig hur du visualisera data i realtid utdata från Stream Analytics-jobb som genererades vid tidpunkten för lösningsdistribution av. En [Power BI online](http://www.powerbi.com/) konto krävs för att utföra följande steg. Om du inte har ett konto kan du [skapar du en](https://powerbi.microsoft.com/pricing).

1. Lägga till Power BI-utdata i Azure Stream Analytics (ASA).

   * Du bör följa anvisningarna i [Azure Stream Analytics & Power BI: en analys i realtid instrumentpanel för realtid synligheten för strömmande data](stream-analytics/stream-analytics-power-bi-dashboard.md) att ställa in utdata för din Azure Stream Analytics-jobb som Power BI-instrumentpanel .
   * Leta upp stream analytics-jobbet i din [Azure-portalen](https://manage.windowsazure.com). Namnet på jobbet bör vara: YourSolutionName + ”streamingjob” + slumpmässiga nummer + ”asapbi” (d.v.s. demostreamingjob123456asapbi).
   * Lägga till en PowerBI-utdata för jobbet ASA. Ange den **utdata Alias** som **'PBIoutput'**. Ange din **Dataset-namnet** och **tabellnamn** som **'EnergyStreamData'**. När du har lagt till utdata, klickar du på **”Start”** längst ned på sidan för att starta Stream Analytics-jobbet. Du bör få ett bekräftelsemeddelande (till exempel ”start stream analytics-jobbet myteststreamingjob12345asablob lyckades”).
2. Logga in på [Power BI online](http://www.powerbi.com)

   * På den vänstra panelen datauppsättningar avsnitt i Min arbetsyta bör du kunna se en ny datamängd som visar på den vänstra panelen i Power BI. Det här är den strömmande data du flyttas från Azure Stream Analytics i föregående steg.
   * Kontrollera att den ***visualiseringar*** fönstret är öppet och visas på höger sida av skärmen.
3. Skapa panelen ”begäran av tidsstämpel”:

   * Klicka på dataset **'EnergyStreamData'** på den vänstra panelen datauppsättningar avsnitt.
   * Klicka på **”linjediagram”** ikonen ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Klicka på 'EnergyStreamData' i **fält** panelen.
   * Klicka på **”tidsstämpel”** och kontrollera att den visar under ”axel”. Klicka på **”belastningen”** och se till att det visas under ”värden”.
   * Klicka på **spara** högst upp och namn för rapporten som ”EnergyStreamDataReport”. Rapporten med namnet ”EnergyStreamDataReport” visas i rapporter i fönstret Navigator vänster.
   * Klicka på **”PIN-kod Visual”** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ikon i övre högra hörnet av den här linjediagram, ett ”PIN-kod till instrumentpanel” fönster kan visas som du kan välja en instrumentpanel. Markera ”EnergyStreamDataReport” och klicka på ”Pin”.
   * Placera markören över den här panelen på instrumentpanelen, klicka på ”Redigera”-ikonen i övre högra hörnet för att ändra dess namn som ”begäran av tidsstämpel”
4. Skapa andra instrumentpaneler baserat på lämplig datauppsättningar. Den slutliga instrumentpanelsvyn:![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Installationsprogrammet Cold sökväg instrumentpanelen
I cold sökväg data pipeline är väsentliga målet att hämta begäran prognosen för varje region. Powerbi ansluter till en Azure SQL database som datakälla, där förutsägelse resultatet lagras.

> [!NOTE]
> 1) Det tar flera timmar att samla in tillräckligt prognosen resultat för instrumentpanelen. Vi rekommenderar att du startar processen 2 – 3 timmar efter att du lunch generatorn data. 2) i det här steget i förutsättning är att hämta och installera den kostnadsfria programvaran [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Hämta autentiseringsuppgifterna på databasen.

   Du behöver **databasen servernamn, databasnamn, användarnamn och lösenord** innan du fortsätter till nästa steg. Här följer stegen för att hjälpa dig hur du hittar dem.

   * En gång **”Azure SQL Database”** på din lösningsmall diagram blir grön, klickar du på den och klicka sedan på **”öppen”**. Vägleds du Azure-portalen och databasen informationssidan öppnas också.
   * Du kan hitta ett ”databas” avsnitt på sidan. Den visar databasen som du har skapat. Namnet på din databas bör vara **”din lösningsnamn slumpmässiga nummer + 'db'”** (till exempel ”mytest12345db”).
   * Klicka på din databas i den nya pop ut panelen du hittar din Databasservernamnet högst upp. Databasservernamn bör vara `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (till exempel ”mytest12345.database.windows.net,1433”).
   * Databasen **användarnamn** och **lösenord** är samma som användarnamnet och lösenordet tidigare registreras under distributionen av lösningen.
2. Uppdatera datakällan för cold sökväg Power BI-fil

   * Kontrollera att du har installerat den senaste versionen av [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * I den **”DemandForecastingDataGeneratorv1.0”** mapp som du har hämtat, dubbelklickar du på den **'Power BI Template\DemandForecastPowerBI.pbix'** fil. Inledande visualiseringar baseras på falska data. **Obs:** om ett felmeddelande visas kontrollerar du att du har installerat den senaste versionen av Power BI Desktop.

     När du öppnar den överst i filen, klickar du på **redigera frågor**. Dubbelklicka i pop ut fönstret **'Source'** i den högra rutan.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * Ersätt i pop ut fönstret **”Server”** och **”databas”** med dina egna och databasnamn, och klicka sedan på **”OK”**. Kontrollera att du anger porten 1433 för servernamn (**YourSolutionName.database.windows.net 1433**). Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa pop ut fönstret ser du två alternativ i det vänstra fönstret (**Windows** och **databasen**). Klicka på **”databas”**, Fyll i din **”användarnamn”** och **”Password”** (detta är det användarnamn och lösenord som du angav när du distribuerade lösningen och skapa en Azure SQL database). I ***Välj vilken datanivå att inställningarna ska***, kontrollera nivån databasalternativet. Klicka på **”Anslut”**.
   * Stäng fönstret när du är interaktiv tillbaka till föregående sida. Ett meddelande POP - ut klickar du på **tillämpa**. Klicka slutligen på den **spara** för att spara ändringarna. Power BI-fil har nu upprätta anslutningen till servern. Om din visualiseringar är tomma, kontrollera att du radera markeringarna på visualiseringar visualisera alla data genom att klicka på ikonen Radera i det övre högra hörnet av förklaringar. Använd uppdateringsknappen för att reflektera nya data i visualiseringar. Först bara ser du seed-data på din visualiseringar som data factory kommer att uppdatera var 3: e timme. Efter 3 timmar finns nya förutsägelser som visas i din visualiseringar när du uppdaterar data.
3. (Valfritt) Publicera instrumentpanelen cold sökväg till [Power BI online](http://www.powerbi.com/). Observera att det här steget behöver ett Power BI-konto (eller Office 365-konto).

   * Klicka på **”publicera”** senare några sekunder visas ett fönster visas ”publicering till Power BI lyckades”! med en grön bock. Klicka på länken nedan ”öppna demoprediction.pbix i Powerbi”. Detaljerade anvisningar finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Att skapa en ny instrumentpanel: Klicka på den  **+**  logga bredvid den **instrumentpaneler** avsnitt i det vänstra fönstret. Ange namnet ”begäran prognoser Demo” för den här nya instrumentpanelen.
   * När du öppnar rapporten klickar du på ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) fästa alla visualiseringar på instrumentpanelen. Detaljerade anvisningar finns i [fästa panelen till en Power BI-instrumentpanel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrumentpanelssidan och justera storleken och platsen för din visualiseringar och redigera sin rubriker. Du hittar detaljerade anvisningar om hur du redigerar dina paneler [redigera en sida vid sida - storlek, flytta, Byt namn, PIN-kod, ta bort, lägga till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är ett exempel instrumentpanel med vissa cold sökväg visualiseringar fäst på den.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Valfritt) Schemalägga en uppdatering av datakällan.

   * Att schemalägga en uppdatering av data, håller du muspekaren över den **EnergyBPI-slutlig** dataset, klickar du på ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) och välj sedan **Uppdatera schema**.
     **Obs:** om du ser en varning massage, klickar du på **redigera autentiseringsuppgifter** och se till att dina autentiseringsuppgifter på databasen är samma som de som beskrivs i steg 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Expandera den **Uppdatera schema** avsnitt. Aktivera ”hålla data uppdaterade”.
   * Schemalägga en uppdatering baserat på dina behov. Mer information finns i [uppdatering av Data i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Hur du tar bort din lösning**
Se till att du avbryter datagenerator när inte aktivt med lösningen som kör datagenerator ådrar sig högre kostnader. Ta bort lösningen om du inte använder den. Tar bort din lösning för alla komponenter som har etablerats i din prenumeration när du distribuerade lösningen. Om du vill ta bort lösningen klickar du på din lösningens namn i den vänstra panelen av lösningsmall och klicka på Ta bort.

## <a name="cost-estimation-tools"></a>**Kostnad uppskattning verktyg**
Följande två verktyg är tillgängliga för att hjälpa dig att bättre förstå totala kostnaderna för kör begäran prognoser för energi Lösningsmall i din prenumeration:

* [Microsoft Azure kostnaden exteriörbedömning-verktyget (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure kostnaden exteriörbedömning Tool (stationär dator)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Bekräftelser**
Den här artikeln har skrivits av data forskare Yijing Chen och programvara tekniker Qiu Min hos Microsoft.
