---
title: "Förutsägande Underhåll investerar med Azure - Cortana Intelligence tekniska lösningsguide | Microsoft Docs"
description: "En teknisk guide till Lösningsmall med Microsoft Cortana Intelligence för förebyggande underhåll i aerospace, verktyg och transport."
services: cortana-analytics
documentationcenter: 
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: fboylu
ms.openlocfilehash: a6d9cd05eb370399fc95cc64bae892e8b5a73fe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Teknisk guide till Cortana Intelligence-Lösningsmall för förebyggande underhåll i aerospace och andra företag

## <a name="important"></a>**Viktigt**
Den här artikeln har ersatts. Informationen som är relevant för det aktuella problemet, d.v.s. förutsägande Underhåll investerar, men den senaste artikeln med den senaste informationen finns [här](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace). 

## <a name="acknowledgements"></a>**Bekräftelser**
Den här artikeln har skrivits av datavetare Yan Zhang Gauher Shaheen, Fidan Boylu Uz och programvara tekniker Dan Grecoe på Microsoft.

## <a name="overview"></a>**Översikt**
Lösningsmallar är avsedda att skynda på processen för att skapa en E2E demo ovanpå Cortana Intelligence Suite. En mall för distribuerade ska etablera din prenumeration med nödvändiga komponenter i Cortana Intelligence och skapa relationer mellan dem. Det lägger också pipeline data med exempeldata som genereras från ett generator program som du hämtas och installeras på din lokala dator när du distribuerar lösningen mallen. Data som genereras från generatorn kommer hydrate i data pipeline och starta genererar machine learning förutsägelser som kan sedan visualiserade på Power BI-instrumentpanelen. Distributionsprocessen vägleder dig igenom flera steg för att ställa in autentiseringsuppgifterna lösning. Kontrollera att du registrera dessa autentiseringsuppgifter, till exempel lösningens namn, användarnamn och lösenord som du anger under distributionen.  

Målet med det här dokumentet är att förklara Referensarkitektur och olika komponenter som har etablerats i din prenumeration som en del av den här lösningen mallen. Dokumentet också talar om hur du kan ersätta exempeldata med verkliga data egen för att kunna se insikter och förutsägelser från dina egna data. Dessutom beskriver dokumentet delarna av Lösningsmall som behöver ändras om du vill anpassa lösningen med dina egna data. Instruktioner om hur du skapar Power BI-instrumentpanelen för den här lösningen mallen finns i slutet.

> [!TIP]
> Du kan hämta och skriva ut en [PDF-version av det här dokumentet](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="big-picture"></a>**Helhetsbild**
![Arkitektur för förutsägande Underhåll](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

När lösningen har distribuerats kan olika Azure-tjänster i Cortana Analytics Suite aktiveras (*dvs.* Event Hub, Stream Analytics, HDInsight, Data Factory Maskininlärning *etc*). För arkitekturdiagrammet ovan visas på en hög nivå hur förutsägande Underhåll för flyg Lösningsmall är uppbyggd från slutpunkt till slutpunkt. Du kommer att kunna undersöka dessa tjänster i azure-portalen genom att klicka på dem i lösningen mallen diagrammet skapas med distributionen av lösningen med undantag för HDInsight som den här tjänsten har etablerats på begäran när relaterade pipeline aktiviteter som krävs till kör och borttagna efteråt.
Du kan hämta en [i full storlek version av diagrammet](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I följande avsnitt beskrivs varje del.

## <a name="data-source-and-ingestion"></a>**Datakällan och införandet**
### <a name="synthetic-data-source"></a>Syntetiska datakälla
Datakällan som används genereras från ett program som du kan hämta och kör lokalt efter slutförd distribution för den här mallen. Du hittar instruktioner för att hämta och installera det här programmet i fältet egenskaper när du väljer den första noden som kallas förutsägande Underhåll Datagenerator i lösningen mallen diagrammet. Det här programmet feeds den [Azure Event Hub](#azure-event-hub) med datapunkter, eller händelser som ska användas i resten av flödet lösning. Den här datakällan består av eller härledd från offentligt tillgängliga data från den [NASA data databasen](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) med hjälp av den [turbofläktmotorer endast motorn försämring simuleringen datauppsättning](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Händelsen generation programmet kommer att fylla i Azure-Händelsehubb endast när det körs på datorn.

### <a name="azure-event-hub"></a>Azure händelsehubb
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) service är mottagaren av indata som angetts av syntetiska datakällan som beskrivs ovan.

## <a name="data-preparation-and-analysis"></a>**Förberedelse av data och analys**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) används för att tillhandahålla nästan analys i realtid på Indataströmmen från den [Azure Event Hub](#azure-event-hub) tjänst och publicera resultatet till en [Power BI](https://powerbi.microsoft.com) instrumentpanelen som arkiverar alla rådata inkommande händelser till den [Azure Storage](https://azure.microsoft.com/services/storage/) tjänsten för senare bearbetning av den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service.

### <a name="hd-insights-custom-aggregation"></a>HD insikter anpassade aggregering
Tjänsten Azure HD Insight används för att köra [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript (styrd av Azure Data Factory) att tillhandahålla aggregeringar för raw-händelser som har arkiverats med Azure Stream Analytics-tjänsten.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) tjänsten används (styrd av Azure Data Factory) att göra förutsägelser återstående livslängd (RUL) en viss flygplan motor som är angivna indata som tagits emot.

## <a name="data-publishing"></a>**Publicering av data**
### <a name="azure-sql-database-service"></a>Azure SQL Database-tjänsten
Den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) används för att lagra (hanteras av Azure Data Factory) förutsägelser som tagits emot av tjänsten Azure Machine Learning som ska användas i den [Power BI](https://powerbi.microsoft.com) instrumentpanelen.

## <a name="data-consumption"></a>**Användning av data**
### <a name="power-bi"></a>Power BI
Den [Power BI](https://powerbi.microsoft.com) används för att visa en instrumentpanel som innehåller aggregeringar och aviseringar som tillhandahålls av den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) tjänsten samt RUL förutsägelser som lagras i [Azure SQL Databasen](https://azure.microsoft.com/services/sql-database/) som skapas med hjälp av den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) service. Anvisningar om hur du skapar Power BI-instrumentpanelen för den här lösningen-mallen finns i avsnittet nedan.

## <a name="how-to-bring-in-your-own-data"></a>**Hur du hanterar i dina egna data**
Det här avsnittet beskrivs hur du hanterar dina egna data till Azure och vilka områden kräver ändringar för de data du sätta i den här arkitekturen.

Det är inte troligt att alla dataset som du kan ta matchar den datamängd som används av den [turbofläktmotorer endast motorn försämring simuleringen datauppsättning](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) används för den här lösningen mallen. Förstå dina data och kraven kommer att vara avgörande i hur du ändrar den här mallen för att arbeta med dina egna data. Om det här är din första exponeringen till tjänsten Azure Machine Learning, kan du få en introduktion till den med hjälp av exemplet i [skapa ditt första experiment](../studio/create-experiment.md).

I följande avsnitt beskrivs i avsnitt på den mall som kräver ändringar när en ny datamängd införs.

### <a name="azure-event-hub"></a>Azure Event Hub
Tjänsten Azure Event Hub är mycket allmänna, så att data kan vara upp till hubben i CSV- eller JSON-format. Ingen särskild bearbetning sker i Azure-Händelsehubb, men det är viktigt att du förstår de data som matas in.

Det här dokumentet beskriver inte hur att mata in data, men kan du enkelt skicka händelser eller data till en Azure-Händelsehubb med hjälp av Event Hub API.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics-tjänsten används för att tillhandahålla nästan analys i realtid genom att läsa från dataströmmar och skickar data till flera källor.

Den förutsägande Underhåll för flyg Lösningsmall består Azure Stream Analytics-fråga av fyra underfrågor varje konsumerande händelser från Azure Event Hub-tjänsten och utdata behöver fyra olika platser. Dessa utdata består av tre Power BI-datauppsättningar och en Azure-lagringsplats.

Azure Stream Analytics-fråga kan hittas av:

* Logga in på Azure-portalen
* Hitta Stream Analytics-jobb ![Stream Analytics-ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) som genererades när lösningen har distribuerats (*t.ex.*, **maintenancesa02asapbi** och **maintenancesa02asablob** för lösningen förutsägande underhåll)
* Att välja
  
  * ***INDATA*** kan du se frågan indata
  * ***FRÅGAN*** kan du se frågan sig själv
  * ***Matar ut*** att visa olika utdata

Information om Azure Stream Analytics query konstruktionen kan hittas i den [referens för Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I den här lösningen utdata frågorna tre DataSet med nästan realtidsanalys information om den inkommande dataströmmen till en Power BI-instrumentpanel som har angetts som en del av den här lösningen mallen. Eftersom det är implicit kunskap om dataformatet för inkommande, skulle de här frågorna behöva ändras baserat på din dataformat.

Frågan i andra Stream Analytics-jobbet **maintenancesa02asablob** bara matar ut alla [Händelsehubb](https://azure.microsoft.com/services/event-hubs/) händelser till [Azure Storage](https://azure.microsoft.com/services/storage/) och därför kräver inga ändringar oavsett dina dataformat som händelsen fullständig strömmas information till lagring.

### <a name="azure-data-factory"></a>Azure Data Factory
Den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service samordnar rörelser och bearbetning av data. I det förutsägande Underhåll för flyg Lösningsmall datafabriken består av tre [pipelines](../../data-factory/v1/data-factory-create-pipelines.md) att flytta och bearbeta de data som använder olika tekniker.  Du kan komma åt din data factory genom att öppna den noden Data Factory längst ned i lösningen mallen diagram som skapats med distributionen av lösningen. Detta leder till datafabriken på Azure-portalen. Om du ser fel under dina datauppsättningar kan ignorera du de som de är på grund av datafabriken som distribueras innan datagenerator startades. Dessa fel förhindrar inte din data factory från att fungera.

![Data Factory dataset fel](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Det här avsnittet beskrivs nödvändiga [pipelines](../../data-factory/v1/data-factory-create-pipelines.md) och [aktiviteter](../../data-factory/v1/data-factory-create-pipelines.md) som ingår i den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Nedan visas diagramvyn av lösningen.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Två av pipelines i den här fabriken innehåller [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript som används för att partitionera och aggregera data. När anges är skript kommer att finnas i den [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under installationen. Deras plats: maintenancesascript\\\\skriptet\\\\hive\\ \\ (eller https://[Your lösning name].blob.core.windows.net/maintenancesascript).

Liknar den [Azure Stream Analytics](#azure-stream-analytics-1) frågor, den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript har implicit kunskap om dataformatet för inkommande, de här frågorna behöver ändras baserat på din dataformat.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Detta [pipeline](../../data-factory/v1/data-factory-create-pipelines.md) innehåller en enskild aktivitet – en [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för att partitionera data placeras i [Azure Storage](https://azure.microsoft.com/services/storage/) under den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobb.

Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för uppgiften partitionering är ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Detta [pipeline](../../data-factory/v1/data-factory-create-pipelines.md) innehåller flera aktiviteter och vars slutresultatet är poängsatta förutsägelser från den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment som är associerade med den här lösningen mallen.

Aktiviteter i det här är:

* [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för att utföra aggregeringar och egenskapsval som krävs för den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment.
  Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för uppgiften partitionering är ***PrepareMLInput.hql***.
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultaten från den [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) aktivitet för en enskild [Azure Storage](https://azure.microsoft.com/services/storage/) blob som kan vara åtkomst av den [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)aktivitet.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet som anropar den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment som resulterar i resultatet som placeras i en enda [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Detta [pipeline](../../data-factory/v1/data-factory-create-pipelines.md) innehåller en enskild aktivitet – en [kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet av den [Azure Machine Learning](#azure-machine-learning) experiment från den  ***MLScoringPipeline*** till den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som har etablerats som en del av installationen mallen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentera används för den här lösningen mallen innehåller de återstående användbar livslängd (RUL) ett flygplan-motorn. Experimentet är specifik för den datamängd som används och därför kräver ändras eller ersättas som är specifika för de data som tas i.

Information om hur Azure Machine Learning-experiment skapades finns [förutsägande Underhåll: steg 1 av 3, förberedelse av data och funktionen tekniker](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Övervaka förloppet**
 När Data Generator har startats pipeline börjar hämta ur och de olika komponenterna i din lösning starta lanseras i åtgärden följande kommandon från Data Factory. Det finns två sätt som du kan övervaka pipeline.

1. En av Stream Analytics-jobbet skriver inkommande rådata till blob storage. Om du klickar på Blob Storage-komponenten i din lösning från skärmen du har distribuerat lösningen och klicka på Öppna i den högra panelen, tar du det [hanteringsportalen](https://portal.azure.com/). När det, klickar du på Blobbar. Nästa panelen visas en lista över behållare. Klicka på **maintenancesadata**. I panelen nästa visas de **\data** mapp. I mappen \data visas en mapp med namn som timme = 17, timme = 18 osv. Om du ser dessa mappar, betyder det att rådata är korrekt som genereras på datorn och lagras i blob storage. Du bör se csv-filer som ska ha begränsad storlek i MB i mapparna.
2. Det sista steget i pipeline är att skriva data (t.ex. förutsägelser från maskininlärning) i SQL-databasen. Du kan behöva vänta högst tre timmar för data som visas i SQL-databas. Ett sätt att övervaka hur mycket data som är tillgängliga i SQL-databasen är via [azure-portalen](https://manage.windowsazure.com/). Leta reda på SQL-databaser på den vänstra panelen ![SQL ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) och klicka på den. Leta upp din databas **pmaintenancedb** och klicka på den. Klicka på Hantera på nästa sida längst ned
   
    ![Hantera ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    Här kan du klicka på ny fråga och fråga för hur många rader (t.ex. väljer count(*) från PMResult). Län, öka antalet rader i tabellen.

## <a name="power-bi-dashboard"></a>**Power BI-instrumentpanel**
### <a name="overview"></a>Översikt
Det här avsnittet beskrivs hur du ställer in Power BI-instrumentpanel visualisera dina realtidsdata från Azure Stream Analytics (varm sökväg) samt batch förutsägelse resultat från Azure machine learning (kalla sökväg).

### <a name="setup-cold-path-dashboard"></a>Installationsprogrammet cold sökväg instrumentpanelen
I cold sökväg data pipeline är viktigt målet att hämta förutsägande RUL (återstående livslängd) varje flygplan-motorn när den är klar svarta (cykel). Förutsägelse resultatet uppdateras var 3: e timme för att förutsäga flygplan motorer som har slutfört en svarta under de senaste 3 timmarna.

Powerbi ansluter till en Azure SQL database som datakälla, där förutsägelse resultatet lagras. Obs: 1) om hur du distribuerar lösningen en verklig förutsägelse visas i databasen i tre timmar.
Pbix-fil som medföljde nedladdningen Generator innehåller vissa seed-data så att du kan skapa Power BI-instrumentpanelen direkt. 2) i det här steget i förutsättning är att hämta och installera den kostnadsfria programvaran [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Följande steg får du anvisningar om hur du ansluter pbix-fil till SQL-databasen har efter en redundansväxling vid tidpunkten för lösningsdistribution av som innehåller data (*t.ex*. resultat för förutsägelse) för visualisering.

1. Hämta autentiseringsuppgifterna på databasen.
   
   Du behöver **databasen servernamn, databasnamn, användarnamn och lösenord** innan du fortsätter till nästa steg. Här följer stegen för att hjälpa dig hur du hittar dem.
   
   * En gång **Azure SQL-databas** på din lösningsmall diagram blir grön, klickar du på den och klicka sedan på **'Öppen'**.
   * Ser du ett nytt fliken/webbläsarfönster som visar sidan för Azure-portalen. Klicka på **'Resursgrupper'** på den vänstra panelen.
   * Välj den prenumeration som du använder för att distribuera lösningen och välj sedan **' YourSolutionName\_ResourceGroup'**.
   * I den nya pop i Kontrollpanelen klickar du på den ![SQL ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikon för att ansluta till databasen. Databasnamnet är bredvid den här ikonen (*t.ex.*, **'pmaintenancedb'**), och **Databasservernamnet** anges under egenskapen Server name och bör se ut ungefär att **YourSoutionName.database.windows.net**.
   * Databasen **användarnamn** och **lösenord** är samma som användarnamnet och lösenordet tidigare registreras under distributionen av lösningen.
2. Uppdatera datakällan för rapportfilen cold sökväg med Power BI Desktop.
   
   * I mappen på datorn där du hämtade och unzipped filen Generator dubbelklickar du på den **PowerBI\\PredictiveMaintenanceAerospace.pbix** fil. Om du ser alla varningsmeddelanden när du öppnar filen kan ignorera dem. Överst i filen, klickar du på **redigera frågor**.
     
     ![Redigera frågor](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Du ser två tabeller **RemainingUsefulLife** och **PMResult**. Välj den första tabellen och klicka på ![frågan inställningsikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) bredvid **'Source'** under **tillämpas steg** till höger **inställningar för frågan** panelen. Ignorera alla varningsmeddelanden som visas.
   * Ersätt i pop ut fönstret **'Server'** och **”databas”** med dina egna och databasnamn, och klicka sedan på **'OK'**. Kontrollera att du anger porten 1433 för servernamn (**YourSoutionName.database.windows.net 1433**). Lämna fältet databas som **pmaintenancedb**. Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa pop ut fönstret ser du två alternativ i det vänstra fönstret (**Windows** och **databasen**). Klicka på **”databas”**, Fyll i din **'Användarnamn'** och **'Password'** (detta är det användarnamn och lösenord som du angav när du distribuerade lösningen och skapa en Azure SQL-databas). I ***Välj vilken datanivå att inställningarna ska***, kontrollera nivån databasalternativet. Klicka på **”Anslut”**.
   * Klicka på den andra tabellen **PMResult** Klicka ![Navigeringsikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) bredvid **'Source'** under **tillämpas steg** till höger **Inställningar för frågan** panelen, och uppdatera namnen på servern och databasen enligt stegen ovan och klicka på OK.
   * Stäng fönstret när du är interaktiv tillbaka till föregående sida. Ett meddelande visas out - Klicka **tillämpa**. Klicka slutligen på den **spara** för att spara ändringarna. Power BI-fil har nu upprätta anslutningen till servern. Om din visualiseringar är tomma, kontrollera att du radera markeringarna på visualiseringar visualisera alla data genom att klicka på ikonen Radera i det övre högra hörnet av förklaringar. Använd uppdateringsknappen för att reflektera nya data i visualiseringar. Först visas bara seed-data på din visualiseringar som data factory kommer att uppdatera var 3: e timme. Efter 3 timmar visas nya förutsägelser som visas i din visualiseringar när du uppdaterar data.
3. (Valfritt) Publicera instrumentpanelen cold sökväg till [Power BI online](http://www.powerbi.com/). Observera att det här steget behöver ett Power BI-konto (eller Office 365-konto).
   
   * Klicka på **”publicera”** senare några sekunder visas ett fönster visas ”publicering till Power BI lyckades”! med en grön bock. Klicka på länken nedan ”öppna PredictiveMaintenanceAerospace.pbix i Power BI”. Detaljerade anvisningar finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Att skapa en ny instrumentpanel: Klicka på den  **+**  logga bredvid den **instrumentpaneler** avsnitt i det vänstra fönstret. Ange namnet ”förutsägande Underhåll Demo” för den här nya instrumentpanelen.
   * När du öppnar rapporten klickar du på ![fästikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) fästa alla visualiseringar på instrumentpanelen. Detaljerade anvisningar finns i [fästa panelen till en Power BI-instrumentpanel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrumentpanelssidan och justera storleken och platsen för din visualiseringar och redigera sin rubriker. Du hittar detaljerade anvisningar om hur du redigerar dina paneler [redigera en sida vid sida - storlek, flytta, Byt namn, PIN-kod, ta bort, lägga till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är ett exempel instrumentpanel med vissa cold sökväg visualiseringar fäst på den.  Dina nummer på visualiseringar kan vara olika beroende på hur länge du kör din datagenerator.
     <br/>
     ![Vyn slutgiltig](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Att schemalägga en uppdatering av data, håller du muspekaren över den **PredictiveMaintenanceAerospace** dataset, klickar du på ![ellips ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) och välj sedan **Uppdatera schema**.
     <br/>
     **Obs:** om du ser en varning massage, klickar du på **redigera autentiseringsuppgifter** och se till att dina autentiseringsuppgifter på databasen är samma som de som beskrivs i steg 1.
     <br/>
     ![Schemalägga en uppdatering](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expandera den **Uppdatera schema** avsnitt. Aktivera ”hålla data uppdaterade”.
     <br/>
   * Schemalägga en uppdatering baserat på dina behov. Mer information finns i [uppdatering av Data i Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Installationsprogrammet varm sökväg instrumentpanelen
Följande steg vägleder dig hur du visualisera data i realtid från Stream Analytics-jobb som genererades vid tidpunkten för lösningsdistribution av. En [Power BI online](http://www.powerbi.com/) konto krävs för att utföra följande steg. Om du inte har ett konto kan du [skapar du en](https://powerbi.microsoft.com/pricing).

1. Lägga till Power BI-utdata i Azure Stream Analytics (ASA).
   
   * Måste du följa instruktionerna i [Azure Stream Analytics & Power BI: en analys i realtid instrumentpanel för realtid synligheten för strömmande data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) att ställa in utdata för din Azure Stream Analytics-jobb som Power BI-instrumentpanel.
   * ASA-frågan har tre utdata som **aircraftmonitor**, **aircraftalert**, och **flightsbyhour**. Du kan visa frågan genom att klicka på fliken fråga. Motsvarar var och en av dessa tabeller behöver du lägga till utdata till ASA. När du lägger till den första utdatan (*t.ex.* **aircraftmonitor**) kontrollera att den **Utdataalias**, **Dataset-namnet** och **tabell Namnet** är samma (**aircraftmonitor**). Upprepa stegen för att lägga till utdata för **aircraftalert**, och **flightsbyhour**. När du har lagt till alla tre utdata tabeller och startade jobbet ASA du bör få ett bekräftelsemeddelande (*t.ex.*, ”Start Stream Analytics-jobbet maintenancesa02asapbi lyckades”).
2. Logga in på [Power BI online](http://www.powerbi.com)
   
   * På den vänstra panelen datauppsättningar avsnitt i Min arbetsyta i ***DATASET*** namn **aircraftmonitor**, **aircraftalert**, och **flightsbyhour** ska visas. Det här är den strömmande data du flyttas från Azure Stream Analytics i föregående steg. Dataset **flightsbyhour** kanske inte visas på samma gång som de andra två datauppsättningarna på grund av SQL-frågan bakom det. Men bör det visas efter en timme.
   * Kontrollera att den ***visualiseringar*** fönstret är öppet och visas på höger sida av skärmen.
3. När du har de data som flödar till Power BI kan börja du visualisera strömmande data. Nedan fästs en exempel-instrumentpanel med vissa visualiseringar varm sökvägen till den. Du kan skapa andra instrumentpaneler baserat på lämplig datauppsättningar. Dina nummer på visualiseringar kan vara olika beroende på hur länge du kör din datagenerator.

    ![Instrumentpanelsvy](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Här följer några steg för att skapa ett av rutor ovan – ”flottan visningen av Sensor 11 vs. Tröskelvärdet 48,26 ”panelen:
   
   * Klicka på dataset **aircraftmonitor** på den vänstra panelen datauppsättningar avsnitt.
   * Klicka på den **linjediagram** ikon.
   * Klicka på **bearbetas** i den **fält** rutan så att den visar under ”axel” i den **visualiseringar** fönstret.
   * Klicka på ”s11” och ”s11\_aviseringen” så att de båda visas under ”värden”. Klicka på små pilen bredvid **s11** och **s11\_avisering**, ändra ”Sum” på ”medel”.
   * Klicka på **spara** högst upp och namn på rapporten ”aircraftmonitor”. Rapporten med namnet ”aircraftmonitor” visas i den **rapporter** under den **Navigator** fönstret till vänster.
   * Klicka på den **PIN-kod Visual** ikonen i det övre högra hörnet av den här linjediagram. Ett ”PIN-kod till instrumentpanel” fönster kan visas som du kan välja en instrumentpanel. Markera ”förutsägande Underhåll Demo” och klicka på ”Pin”.
   * Markören över den här panelen på instrumentpanelen, klicka på ikonen ”edit” i det övre högra hörnet för att ändra dess namn till ”flottan vy av Sensor 11 vs. Tröskelvärde 48,26 ”och underrubrik till” medelvärde över flottan över tid ”.

## <a name="how-to-delete-your-solution"></a>**Hur du tar bort din lösning**
Se till att du avbryter datagenerator när inte aktivt med lösningen som kör datagenerator påförs högre kostnader. Ta bort lösningen om du inte använder den. Om din lösning tas bort alla komponenter som har etablerats i din prenumeration när du distribuerade lösningen. Om du vill ta bort lösningen klickar du på din lösningens namn i den vänstra panelen av lösningsmall och klicka på Ta bort.

## <a name="cost-estimation-tools"></a>**Kostnad uppskattning verktyg**
Följande två verktyg är tillgängliga som hjälper dig att bättre förstå de totala kostnaderna för med förutsägande Underhåll för flyg Lösningsmall i din prenumeration:

* [Microsoft Azure kostnaden exteriörbedömning-verktyget (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure kostnaden exteriörbedömning Tool (stationär dator)](http://www.microsoft.com/download/details.aspx?id=43376)

