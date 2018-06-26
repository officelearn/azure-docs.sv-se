---
title: Förutsägande Underhåll investerar med Azure - Cortana Intelligence tekniska lösningsguide | Microsoft Docs
description: En teknisk guide till Lösningsmall med Microsoft Cortana Intelligence för förebyggande underhåll i aerospace, verktyg och transport.
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
ms.openlocfilehash: 2916252c08c599d2e528595a8cdf2abca8ea89a3
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938452"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Teknisk guide till Cortana Intelligence-Lösningsmall för förebyggande underhåll i aerospace och andra företag

>[!Important]
Den här artikeln har ersatts. Diskussion om förutsägande Underhåll investerar är relevant, men för aktuell information, referera till [lösning: översikt för Business målgrupper](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Lösningsmallar är avsedda att skynda på processen för att skapa en E2E demo ovanpå Cortana Intelligence Suite. En mall för distribuerade etablerar prenumerationen med nödvändiga komponenter i Cortana Intelligence och relationer mellan dem och skapar sedan. Det lägger också data rörledningen med exempeldata från ett generator program, som du hämtar och installerar på din lokala dator när du distribuerar lösningen mallen. Data från generatorn hydrates i data pipeline och starta genererar maskininlärning förutsägelser som kan sedan visualiserade på Power BI-instrumentpanelen.

Distributionsprocessen vägleder dig genom stegen för att ställa in autentiseringsuppgifterna lösning. Kontrollera att du spelar in autentiseringsuppgifter, till exempel lösningens namn, användarnamn och lösenord som du anger under distributionen. 


Målen för den här artikeln är att:
- Beskriv Referensarkitektur och komponenter som har etablerats i din prenumeration.
- Visa hur du kan ersätta exempeldata med din egen information. 
- Visa hur du ändrar lösningsmall.  

> [!TIP]
> Du kan hämta och skriva ut en [PDF-version av den här artikeln](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Översikt
![Arkitektur för förutsägande Underhåll](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

När du distribuerar lösningen aktiverar Azure-tjänster i Cortana Analytics sviten (inklusive Event Hub, Stream Analytics, HDInsight, Data Factory och Maskininlärning). För Arkitekturdiagram visar hur förutsägande Underhåll för flyg Lösningsmall är uppbyggd. Du kan undersöka dessa tjänster i Azure-portalen genom att klicka på dem i lösningen mallen diagram som skapats med distributionen av lösningen (förutom HDInsight, som har etablerats på begäran när relaterade pipeline-aktiviteter som krävs för att köra och är ta bort efteråt).
Hämta en [i full storlek version av diagrammet](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I följande avsnitt beskrivs delarna av lösningen.

## <a name="data-source-and-ingestion"></a>Datakällan och införandet
### <a name="synthetic-data-source"></a>Syntetiska datakälla
Datakällan som används genereras från ett program som du hämtar och kör lokalt efter slutförd distribution för den här mallen.

Välj den första noden förutsägande Underhåll Datagenerator i lösningen mallen diagrammet för att hitta instruktionerna för att ladda ned och installera det här programmet. Anvisningarna finns i fältet egenskaper. Det här programmet feeds den [Azure Event Hub](#azure-event-hub) med datapunkter, eller händelser som används i resten av flödet lösning. Den här datakällan har härletts från offentligt tillgängliga data från den [NASA data databasen](https://c3.nasa.gov/dashlink/resources/139/) med hjälp av den [turbofläktmotorer endast motorn försämring simuleringen datauppsättning](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Händelsen generation programmet fyller Azure Event Hub endast när det körs på datorn.  

### <a name="azure-event-hub"></a>Azure händelsehubb  
Den [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) service är mottagaren av indata som angetts av syntetiska datakällan.

## <a name="data-preparation-and-analysis"></a>Förberedelse av data och analys  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använd [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) att tillhandahålla nästan analys i realtid på Indataströmmen från den [Azure Event Hub](#azure-event-hub) service. Sedan publicera resultaten till en [Power BI](https://powerbi.microsoft.com) instrumentpanelen samt Arkivera alla rådata inkommande händelser till den [Azure Storage](https://azure.microsoft.com/services/storage/) tjänsten för senare bearbetning av den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)service.

### <a name="hdinsight-custom-aggregation"></a>Anpassade HDInsight-sammanställning
Kör [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript (styrd av Azure Data Factory) med hjälp av HDInsight aggregeringar på raw händelser arkiveras med Azure Stream Analytics-tjänsten.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Göra förutsägelser på återstående livslängd (RUL) en viss flygplan motor med hjälp av indata som tagits emot med [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (styrd av Azure Data Factory). 

## <a name="data-publishing"></a>Publicering av data
### <a name="azure-sql-database"></a>Azure SQL Database
Använd [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) att lagra förutsägelser som tagits emot av tjänsten Azure Machine Learning, som sedan används i den [Power BI](https://powerbi.microsoft.com) instrumentpanelen.

## <a name="data-consumption"></a>Dataförbrukning
### <a name="power-bi"></a>Power BI
Använd [Power BI](https://powerbi.microsoft.com) att visa en instrumentpanel som innehåller aggregeringar och aviseringar som tillhandahålls av [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), samt RUL förutsägelser lagras i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som skapas med hjälp av [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Hur du hanterar i dina egna data
Det här avsnittet beskrivs hur du hanterar dina egna data till Azure och vilka områden som behöver ändras för de data du sätta i den här arkitekturen.

Det är inte troligt att datamängden matchar den datamängd som används av den [turbofläktmotorer endast motorn försämring simuleringen datauppsättning](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) används för den här lösningen mallen. Förstå dina data och kraven är avgörande för hur du ändrar den här mallen för att arbeta med dina egna data. 

I följande avsnitt beskrivs de delar av mallen som kräver ändringar när en ny datamängd införs.

### <a name="azure-event-hub"></a>Azure händelsehubb
Azure Event Hub är generisk; data kan vara upp till hubben i CSV- eller JSON-format. Ingen särskild bearbetning sker i Azure-Händelsehubb, men det är viktigt att du förstår de data som matas in.

Det här dokumentet beskriver inte hur att mata in data, men kan du enkelt skicka händelser eller data till en Azure-Händelsehubb med hjälp av Event Hub-API: er.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använda Azure Stream Analytics-tjänsten för att tillhandahålla nästan analys i realtid genom att läsa från dataströmmar och skickar data till flera källor.

Den förutsägande Underhåll för flyg Lösningsmall består Azure Stream Analytics-fråga av fyra underfrågor varje fråga som förbrukar händelser från Azure Event Hub-tjänsten med utdata till fyra olika platser. Dessa utdata består av tre Power BI-datauppsättningar och en Azure-lagringsplats.

Azure Stream Analytics-fråga kan hittas av:

* Ansluta till Azure-portalen
* Hitta Stream Analytics-jobb ![Stream Analytics-ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) som genererades när lösningen har distribuerats (*till exempel*, **maintenancesa02asapbi** och **maintenancesa02asablob** för lösningen förutsägande underhåll)
* Att välja
  
  * ***INDATA*** kan du se frågan indata
  * ***FRÅGAN*** kan du se frågan sig själv
  * ***Matar ut*** att visa olika utdata

Information om Azure Stream Analytics query konstruktionen kan hittas i den [referens för Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I den här lösningen utdata frågorna tre DataSet med nästan realtidsanalys information om den inkommande dataströmmen till en Power BI-instrumentpanel som ingår i den här lösningen mallen. Eftersom det är implicit kunskap om dataformatet för inkommande, måste de här frågorna ändras baserat på din dataformat.

Frågan i andra Stream Analytics-jobbet **maintenancesa02asablob** bara matar ut alla [Händelsehubb](https://azure.microsoft.com/services/event-hubs/) händelser till [Azure Storage](https://azure.microsoft.com/services/storage/) och därför kräver inga ändringar oavsett dina dataformat som händelsen fullständig strömmas information till lagring.

### <a name="azure-data-factory"></a>Azure Data Factory
Den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service samordnar rörelser och bearbetning av data. I det förutsägande Underhåll för flyg Lösningsmall datafabriken består av tre [pipelines](../../data-factory/concepts-pipelines-activities.md) att flytta och bearbeta de data som använder olika tekniker.  Åtkomst till din data factory genom att öppna noden Data Factory längst ned i lösningen mallen diagram som skapats med distributionen av lösningen. Fel under dina datauppsättningar distribueras på grund av data factory innan datagenerator startades. Dessa fel kan ignoreras och hindrar inte din data factory från att fungera

![Data Factory dataset fel](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Det här avsnittet beskrivs nödvändiga [pipelines och aktiviteter](../../data-factory/concepts-pipelines-activities.md) som ingår i den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Här är en diagramvy av lösningen.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Två av pipelines i den här fabriken innehåller [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript som används för att partitionera och aggregera data. När anges skript finns i den [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under installationen. Deras plats är: maintenancesascript\\\\skriptet\\\\hive\\ \\ (eller https://[Your lösning name].blob.core.windows.net/maintenancesascript).

Liknar [Azure Stream Analytics](#azure-stream-analytics-1) frågor, den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript har implicit kunskap om dataformatet för inkommande och måste ändras baserat på din dataformat.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Detta [pipeline](../../data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för att partitionera data placeras i [Azure Storage](https://azure.microsoft.com/services/storage/) under den [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobb.

Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för uppgiften partitionering är ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Detta [pipeline](../../data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter vars slutresultatet är poängsatta förutsägelser från den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment som är associerade med den här lösningen mallen.

Aktiviteter är:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) med en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) som kör en [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för att utföra aggregeringar och egenskapsval som krävs för den [Azure-dator Lär dig](https://azure.microsoft.com/services/machine-learning/) experiment.
  Den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript för uppgiften partitionering är ***PrepareMLInput.hql***.
* [Kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultaten från den [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivitet för en enskild [Azure Storage](https://azure.microsoft.com/services/storage/) blob som nås av den [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet anrop av [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment med resultat som placeras i en enda [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Detta [pipeline](../../data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [kopiera](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet som flyttar resultatet av den [Azure Machine Learning](#azure-machine-learning) experiment från den  ***MLScoringPipeline*** till den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) etablerats som en del av installationen mallen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentera används för den här lösningen mallen innehåller de återstående användbar livslängd (RUL) ett flygplan-motorn. Experimentet är specifik för den datamängd som används och kräver ändring eller ersättning som är specifika för data som finns.

Information om hur Azure Machine Learning-experiment skapades finns [förutsägande Underhåll: steg 1 av 3, förberedelse av data och funktionen tekniker](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Övervaka förloppet
När Data Generator har startats pipeline börjar dehydratisera och de olika komponenterna i din lösning starta lanseras i åtgärden följande kommandon från data factory. Det finns två sätt att övervaka pipeline.

1. En av Stream Analytics-jobb skriver inkommande rådata till blob storage. Om du klickar på Blob Storage-komponenten i din lösning från skärmen du har distribuerat lösningen och klicka på Öppna i den högra panelen, tar du det [Azure-portalen](https://portal.azure.com/). När det, klickar du på Blobbar. I panelen nästa visas en lista över behållare. Klicka på **maintenancesadata**. I nästa panelen är den **\data** mapp. I mappen \data är mappar med namn som timme = 17 och timme = 18. Förekomsten av dessa mappar anger rådata som genereras på datorn och lagras i blob storage. Du bör se csv-filer med begränsad storlek i MB i mapparna.
2. Det sista steget i pipeline är att skriva data (till exempel förutsägelser från maskininlärning) i SQL-databasen. Du kan behöva vänta högst tre timmar för data som visas i SQL-databas. Ett sätt att övervaka hur mycket data som är tillgängliga i SQL-databasen är via den [Azure-portalen](https://portal.azure.com/). Leta reda på SQL-databaser på den vänstra panelen ![SQL ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) och klicka på den. Leta upp din databas **pmaintenancedb** och klicka på den. Klicka på Hantera på nästa sida längst ned
   
    ![Hantera ikon](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    Här kan du klicka på ny fråga och fråga för hur många rader (till exempel väljer count(*) från PMResult). Län, öka antalet rader i tabellen.

## <a name="power-bi-dashboard"></a>Power BI-instrumentpanel

Ställ in en Power BI-instrumentpanel visualisera dina Azure Stream Analytics-data (varm sökväg) och batch förutsägelse resultat från Azure machine learning (kalla sökväg).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurera instrumentpanelen cold sökväg
Målet är att hämta förutsägande RUL (återstående livslängd) varje flygplan-motorn när den är klar svarta (cykel) i cold sökväg data pipeline. Förutsägelse resultatet uppdateras var 3: e timme för att förutsäga flygplan motorer som har slutfört en svarta under de senaste 3 timmarna.

Powerbi ansluter till en Azure SQL database som datakälla, där förutsägelse resultatet lagras. Obs: 1) om hur du distribuerar din lösning, en förutsägelse visas i databasen i tre timmar.
Pbix-fil som medföljde nedladdningen Generator innehåller vissa seed-data så att du kan skapa Power BI-instrumentpanelen direkt. 2) i det här steget i förutsättning är att hämta och installera den kostnadsfria programvaran [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Följande steg anvisningar du om hur du ansluter pbix-fil till SQL-databasen har efter en redundansväxling vid tidpunkten för lösningsdistribution av som innehåller data (till exempel förutsägelse resultat) för visualisering.

1. Hämta autentiseringsuppgifterna på databasen.
   
   Du behöver **databasen servernamn, databasnamn, användarnamn och lösenord** innan du fortsätter till nästa steg. Här följer stegen för att hjälpa dig hur du hittar dem.
   
   * En gång **Azure SQL-databas** på din lösningsmall diagram blir grön, klickar du på den och klicka sedan på **'Öppen'**.
   * Ser du ett nytt fliken/webbläsarfönster som visar sidan för Azure-portalen. Klicka på **'Resursgrupper'** på den vänstra panelen.
   * Välj den prenumeration som du använder för att distribuera lösningen och välj sedan **' YourSolutionName\_ResourceGroup'**.
   * I den nya pop i Kontrollpanelen klickar du på den ![SQL ikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikon för att ansluta till databasen. Databasnamnet är bredvid ikonen (till exempel **'pmaintenancedb'**), och **Databasservernamnet** anges under egenskapen Server name och bör likna  **YourSoutionName.database.windows.net**.
   * Databasen **användarnamn** och **lösenord** är samma som användarnamnet och lösenordet tidigare registreras under distributionen av lösningen.
2. Uppdatera datakällan för rapportfilen cold sökväg med Power BI Desktop.
   
   * I den mapp där du hämtade och unzipped Generator-filen, dubbelklickar du på den **PowerBI\\PredictiveMaintenanceAerospace.pbix** fil. Om du ser alla varningsmeddelanden när du öppnar filen kan ignorera dem. Överst i filen, klickar du på **redigera frågor**.
     
     ![Redigera frågor](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Du ser två tabeller **RemainingUsefulLife** och **PMResult**. Välj den första tabellen och klicka på ![frågan inställningsikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) bredvid **'Source'** under **tillämpas steg** till höger **inställningar för frågan** panelen. Ignorera alla varningsmeddelanden som visas.
   * Ersätt i pop ut fönstret **'Server'** och **”databas”** med dina egna och databasnamn, och klicka sedan på **'OK'**. Kontrollera att du anger porten 1433 för servernamn (**YourSoutionName.database.windows.net 1433**). Lämna fältet databas som **pmaintenancedb**. Ignorera varningsmeddelanden som visas på skärmen.
   * I nästa pop ut fönstret ser du två alternativ i det vänstra fönstret (**Windows** och **databasen**). Klicka på **”databas”**, Fyll i din **'Användarnamn'** och **'Password'** (detta är det användarnamn och lösenord som du angav när du distribuerade lösningen och skapa en Azure SQL-databas). I ***Välj vilken datanivå att inställningarna ska***, kontrollera nivån databasalternativet. Klicka på **”Anslut”**.
   * Klicka på den andra tabellen **PMResult** Klicka ![Navigeringsikonen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) bredvid **'Source'** under **tillämpas steg** till höger **Inställningar för frågan** panelen, och uppdatera namnen på servern och databasen enligt stegen ovan och klicka på OK.
   * Stäng fönstret när du är interaktiv tillbaka till föregående sida. Ett meddelande visas – Klicka på **tillämpa**. Klicka slutligen på den **spara** för att spara ändringarna. Power BI-fil har nu upprätta anslutningen till servern. Om din visualiseringar är tomma, kontrollera att du radera markeringarna på visualiseringar visualisera alla data genom att klicka på ikonen Radera i det övre högra hörnet av förklaringar. Använd uppdateringsknappen för att reflektera nya data i visualiseringar. Först bara ser du seed-data på din visualiseringar som data factory kommer att uppdatera var 3: e timme. Efter 3 timmar visas nya förutsägelser som visas i din visualiseringar när du uppdaterar data.
3. (Valfritt) Publicera instrumentpanelen cold sökväg till [Power BI online](http://www.powerbi.com/). Observera att det här steget behöver ett Power BI-konto (eller Office 365-konto).
   
   * Klicka på **”publicera”** senare några sekunder visas ett fönster visas ”publicering till Power BI lyckades”! med en grön bock. Klicka på länken nedan ”öppna PredictiveMaintenanceAerospace.pbix i Power BI”. Detaljerade anvisningar finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Att skapa en ny instrumentpanel: Klicka på den **+** logga bredvid den **instrumentpaneler** avsnitt i det vänstra fönstret. Ange namnet ”förutsägande Underhåll Demo” för den här nya instrumentpanelen.
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
Följande steg hjälper dig hur du visualisera data från Stream Analytics-jobb som genererades vid tidpunkten för lösningsdistribution av. En [Power BI online](http://www.powerbi.com/) konto krävs för att utföra följande steg. Om du inte har ett konto kan du [skapar du en](https://powerbi.microsoft.com/pricing).

1. Lägga till Power BI-utdata i Azure Stream Analytics (ASA).
   
   * Du måste följa anvisningarna i [Azure Stream Analytics & Power BI: en instrumentpanelen för realtid synligheten för strömmande data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) att ställa in utdata för din Azure Stream Analytics-jobb som Power BI-instrumentpanel.
   * ASA-frågan har tre utdata som **aircraftmonitor**, **aircraftalert**, och **flightsbyhour**. Du kan visa frågan genom att klicka på fliken fråga. Motsvarar var och en av dessa tabeller som du behöver lägga till utdata till ASA. När du lägger till den första utdatan (**aircraftmonitor**) kontrollera att den **Utdataalias**, **Dataset-namnet** och **tabellnamn** är samma (**aircraftmonitor**). Upprepa stegen för att lägga till utdata för **aircraftalert**, och **flightsbyhour**. När du har lagt till alla tre utdata tabeller och startade jobbet ASA, bör du få ett bekräftelsemeddelande (”starta Stream Analytics-jobbet maintenancesa02asapbi lyckades”).
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
   * Klicka på den **PIN-kod Visual** ikonen i det övre högra hörnet av den här linjediagram. Ett ”PIN-kod till instrumentpanel” fönster kan visas som du kan välja en instrumentpanel. Välj ”förutsägande Underhåll Demo” och klicka på ”Pin”.
   * Markören över den här panelen på instrumentpanelen, klicka på ikonen ”edit” i det övre högra hörnet för att ändra dess namn till ”flottan vy av Sensor 11 vs. Tröskelvärde för 48,26 ”och underrubrik till” medelvärde över flottan över tid ”.

## <a name="delete-your-solution"></a>Ta bort din lösning
Se till att du avbryter datagenerator när inte aktivt med lösningen som kör datagenerator påförs högre kostnader. Ta bort lösningen om du inte använder den. Tar bort din lösning för alla komponenter som har etablerats i din prenumeration när du distribuerade lösningen. Om du vill ta bort lösningen, klickar du på din lösning i den vänstra panelen av lösningen mallen och klicka sedan på **ta bort**.

## <a name="cost-estimation-tools"></a>Kostnad uppskattning verktyg
Följande två verktyg är tillgängliga som hjälper dig att bättre förstå de totala kostnaderna för med förutsägande Underhåll för flyg Lösningsmall i din prenumeration:

* [Microsoft Azure kostnaden exteriörbedömning-verktyget (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure kostnaden exteriörbedömning Tool (stationär dator)](http://www.microsoft.com/download/details.aspx?id=43376)

