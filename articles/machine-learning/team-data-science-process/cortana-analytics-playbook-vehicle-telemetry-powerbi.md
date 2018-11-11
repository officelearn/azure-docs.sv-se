---
title: Power BI-instrumentpanel för fordonshälsa och körvanor – Azure | Microsoft Docs
description: Använda funktionerna i Cortana Intelligence insikter i realtid och förutsägande på fordonshälsa och vanor.
services: machine-learning
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 9f86d1d74f5139458999a8c91ac35ec76ac278c8
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300939"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Vehicle Telemetry Analytics-lösningen mall Power BI instrumentpanel, konfigurationsanvisningar

Vehicle Telemetry Analytics-lösningen presenterar använda hur bilhandlare, bilfabrikanter och försäkringsbolag kan funktionerna i Cortana Intelligence. De kan hämta realtidsdata och förutsägande insikter om fordonshälsa och driver vanor att förbättra kundupplevelsen, forskning och utveckling och marknadsföring. Dessa stegvisa anvisningar visar hur du konfigurerar Power BI-rapporter och instrumentpanel när du distribuerar lösningen i din prenumeration. 

Sammanfattande beskrivning av den här lösningen finns i [Vehicle Telemetry Analytics-lösning, playbook](cortana-analytics-playbook-vehicle-telemetry.md).
Om du vill utforska ytterligare i den här lösningen, se [Vehicle Telemetry Analytics-lösning, playbook: fördjupa dig i lösningen](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

## <a name="prerequisites"></a>Förutsättningar
* Distribuera den [analys av Fordonstelemetri](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) lösning. 
* [Installera Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331).
* Hämta en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/). Om du inte har en Azure-prenumeration kan komma igång med Azure-kostnadsfria prenumerationen.
* Öppna en Power BI-konto.

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence suite-komponenter
Som en del av lösningsmallen Vehicle Telemetry Analytics distribueras följande Cortana Intelligence-tjänster i din prenumeration:

* **Azure Event Hubs** mata in miljontals händelser för fordonstelemetri i Azure.
* **Azure Stream Analytics** ger insikter i realtid om fordonshälsa och kvarstår dessa data till långsiktig lagring för mer omfattande batchanalyser.
* **Azure Machine Learning** identifierar avvikelser i realtid och använder batch-bearbetning för att lämna förutsägelser.
* **Azure HDInsight** transformerar data i stor skala.
* **Azure Data Factory** hanterar orkestrering, schemaläggning, resurshantering samt övervakning av din pipeline för batchbearbetning.

**Power BI** ger den här lösningen en omfattande instrumentpanel för data och visualisering för förutsägelseanalys. 

Lösningen använder två olika datakällor:

* Simulerade vehicle signaler och diagnostiska datauppsättningar
* Vehicle catalog

En vehicle telematics simulator ingår som en del av den här lösningen. Den genererar diagnostisk information och signaler som motsvarar tillståndet för vehicle och utveckla mönster vid en viss tidpunkt. 

Vehicle katalogen är en referensdatauppsättning som mappar VINs till modeller.

## <a name="power-bi-dashboard-preparation"></a>Förberedelse av Power BI-instrumentpanel
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Konfigurera Power BI-instrumentpanelen i realtid

#### <a name="start-the-real-time-dashboard-application"></a>Starta programmet realtidsinstrumentpanel
När distributionen är klar följer du anvisningarna för manuell åtgärd.

1. Ladda ned programmet realtidsinstrumentpanel RealtimeDashboardApp.zip och packa upp den.

1.  Öppna app config-filen RealtimeDashboardApp.exe.config i mappen uppzippade. Ersätt appSettings för Event Hubs, Azure Blob storage och Azure Machine Learning Tjänstanslutningar med värdena i anvisningarna för manuell åtgärd. Spara ändringarna.

1. Kör programmet RealtimeDashboardApp.exe. Ange giltig Power BI-autentiseringsuppgifter på fönstret inloggning. 

   ![Power BI inloggningsfönstret](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
1. Välj **Acceptera**. Appen startar.

   ![Behörigheter för Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

1. Logga in på Power BI-webbplatsen och skapa en realtidsinstrumentpanel.

Nu är du redo att konfigurera Power BI-instrumentpanelen.  

### <a name="configure-power-bi-reports"></a>Konfigurera Power BI-rapporter
Ta ungefär 30 till 45 minuter för att slutföra i realtid rapporter och instrumentpanelen. 

1. Bläddra till den [Power BI](http://powerbi.com) webbsidan, och logga in.

    ![Power BI-inloggningssida](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

1. En ny datauppsättning skapas i Power BI. Välj den **ConnectedCarsRealtime** datauppsättning.

    ![ConnectedCarsRealtime datauppsättning](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

1. Tryck på Ctrl + S för att spara tom rapport.

    ![Tom rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

1. Ange rapportens namn **Vehicle Telemetry Analytics i realtid – rapporter**.

    ![Rapportnamn](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Rapporter i realtid
Tre i realtid rapporter finns i den här lösningen:

* Fordon i åtgärden
* Fordon som kräver Underhåll
* Vehicle hälsostatistik

Du kan konfigurera alla tre rapporterna eller du kan stoppa efter alla stadier. Du kan sedan fortsätta till nästa avsnitt om hur du konfigurerar batch-rapporter. Vi rekommenderar att du skapar alla tre rapporter för att visualisera de fullständiga insikterna i realtid sökvägens av lösningen.  

### <a name="vehicles-in-operation-report"></a>Fordon i åtgärden rapport
1. Dubbelklicka på **sida 1**, och Byt namn på den **fordon i åtgärden**.

    ![Fordon i åtgärden](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

1. På den **fält** fliken **vin**. På den **visualiseringar** fliken den **kort** visualisering.  

    Den **kort** visuellt objekt skapas enligt följande bild:

    ![Välj vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

1. På den **fält** fliken **Stad** och **vin**. På den **visualiseringar** fliken den **kartan** visualisering. Dra **vin** till den **värden** området. Dra **Stad** till den **förklaring** området. 

    ![Kortvisualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

1. På den **visualiseringar** fliken den **Format** avsnittet. Välj **rubrik**, och ändra **Text** till **fordon i åtgärd efter ort**.

    ![Fordon i åtgärd efter stad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Den slutliga visualiseringen ser ut som i följande exempel:

    ![Sista visualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

1. På den **fält** fliken **Stad** och **vin**. På den **visualiseringar** fliken den **grupperat stående stapeldiagram** visualisering. Dra **Stad** till den **axel** området. Dra **vin** till den **värdet** området.

1. Sortera diagrammet efter **antal vin**.

    ![Antal vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

1. Ändra diagrammet **rubrik** till **fordon i åtgärd efter ort**. 

1. Välj den **Format** avsnittet och välj sedan **Datafärger**. Ändra **visa alla** till **på**.

    ![Datafärger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

1. Ändra färg på en enskild stad genom att välja symbolen färg.

    ![Ändra färg](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

1. På den **visualiseringar** fliken den **grupperat stående stapeldiagram** visualisering. På den **fält** fliken genom att dra **Stad** till den **axel** området. Dra **modellen** till den **förklaring** området. Dra **vin** till den **värdet** området.

    ![Grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Diagrammet ser ut som på följande bild:

    ![Rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

1. Ordna alla visualiseringar så att sidan ser ut som i följande exempel:

    ![Instrumentpanel med visualiseringar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Du har konfigurerat ”fordon i åtgärden” rapporten. Du kan skapa nästa i realtid rapport eller du kan avbryta här och konfigurera instrumentpanelen. 

### <a name="vehicles-requiring-maintenance-report"></a>Fordon kräver Underhåll rapport

1. Välj ![Lägg till](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) att lägga till en ny rapport. Byt namn på den **fordon kräver Underhåll**.

    ![Fordon som kräver Underhåll](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

1. På den **fält** fliken **vin**. På den **visualiseringar** fliken den **kort** visualisering.

    ![Vin kortvisualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Datauppsättningen innehåller ett fält med namnet **MaintenanceLabel**. Det här fältet kan ha värdet ”0” eller ”1”. Värdet anges av machine learning-modell som tillhandahålls som en del av lösningen. Det är integrerat med sökvägen i realtid. Värdet ”1” anger att en vehicle kräver underhåll. 

1. Att lägga till en **nivå sidfilter** att visa data för de fordon som kräver Underhåll: 

   a. Dra den **MaintenanceLabel** fältet **på sidnivå**.
  
      ![Sidnivåfilter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Längst ned i **sidan nivå filter MaintenanceLabel**väljer **Basfiltrering**.

      ![Basfiltrering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Ange filtervärdet **1**.

      ![Filtervärde](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

1. På den **visualiseringar** fliken den **grupperat stående stapeldiagram** visualisering. 

    ![Vin kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

1. På den **fält** fliken genom att dra **modellen** till den **axel** området. Dra **vin** till den **värdet** området. Sortera visualiseringen enligt **antal vin**. Ändra diagrammet **rubrik** till **fordon kräver underhåll av modell**. 

1. På den **fält** ![fält-image](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) delen av den **visualiseringar** fliken genom att dra **vin** till **färgmättnad**.

    ![Färgmättnad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

1. På den **Format** ändrar **Datafärger** i visualiseringen: 

    a. Ändra den **minsta** färg till **F2C812**.

    b. Ändra den **maximala** färg till **FF6300**.

    ![Ny datafärger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Ny visualiseringsfärger se ut som i följande exempel:

    ![Ny visualiseringsfärger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

1. På den **visualiseringar** fliken **grupperat stående stapeldiagram**. Dra **vin** till den **värdet** området. Dra **Stad** till den **axel** området. Sortera diagrammet efter **antal vin**. Ändra diagrammet **rubrik** till **fordon kräver Underhåll efter ort**.

    ![Fordon som kräver Underhåll efter stad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

1. På den **visualiseringar** fliken den **Flerradskort** visualisering. Dra **modellen** och **vin** till den **fält** området.

    ![Flerradskort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

1. Ordna alla visualiseringar så att slutgiltiga ser ut som i följande exempel: 

    ![Ordningen har ändrats slutrapporten](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Du har konfigurerat rapporten ”fordon kräver Underhåll” i realtid. Du kan skapa nästa i realtid rapport eller du kan avbryta här och konfigurera instrumentpanelen. 

### <a name="vehicle-health-statistics-report"></a>Vehicle hälsostatistik rapport

1. Välj ![Lägg till](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) att lägga till en ny rapport. Byt namn på den **fordon hälsostatistik**. 

1. På den **visualiseringar** fliken den **mätare** visualisering. Dra **hastighet** till den **värdet**, **minimivärde**, och **maxvärde** områden.

   ![Fordon hälsostatistik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

1. I den **värdet** området ändra standard-aggregering av **hastighet** till **genomsnittlig**.

1. I den **minimivärde** området ändra standard-aggregering av **hastighet** till **minsta**.

1. I den **maxvärde** området ändra standard-aggregering av **hastighet** till **maximala**.

   ![Värden för hastighet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

1. Byt namn på den **mätare rubrik** till **genomsnittlig hastighet**.

   ![Mätare](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

1. Välj det tomma utrymmet för att lägga till en ny visualisering.  

    Lägg också till en **mätare** för **genomsnittlig motorolja**, **genomsnittlig bränsle**, och **genomsnittlig motorn temperatur**.  

1. Ändra standard-aggregering av fälten i varje mätare som du gjorde i föregående steg i den **genomsnittlig hastighet** mätare.

    ![Ytterligare mätare](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.

1. På den **visualiseringar** fliken den **rad och grupperat stående stapeldiagram** visualisering. Dra **Stad** till **delad axel**. Dra **tirepressure**, **engineoil**, och **hastighet** till den **kolumnvärdena** området. Ändra sina sammansättningstyp till **genomsnittlig**. 

1. Dra **engineTemperature** till den **radvärden** området. Ändra aggregeringstypen till **genomsnittlig**. 

    ![Kolumnen och radvärden](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

1. Ändra diagrammet **rubrik** till **genomsnittlig hastighet, däck tryck, motorolja och motor temperatur**.  

    ![Rubrik för rad- och grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.

1. På den **visualiseringar** fliken den **Trädkarta** visualisering. Dra **modellen** till den **grupp** området. Dra **MaintenanceProbability** till den **värden** området.

1. Ändra diagrammet **rubrik** till **Vehicle modeller som kräver Underhåll**.

    ![Trädkarta rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.

1. På den **visualiseringar** fliken den **100% staplad liggande diagram** visualisering. Dra **Stad** till den **axel** området. Dra **MaintenanceProbability** och **RecallProbability** till den **värdet** området.

    ![Axel-och värde](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

1. På den **Format** väljer **Datafärger**. Ange den **MaintenanceProbability** färg till värdet **F2C80F**.

1. Ändra diagrammet **rubrik** till **sannolikheten för Vehicle underhåll och återkallande efter ort**.

    ![100% liggande stapeldiagram rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.

1. På den **visualiseringar** fliken den **ytdiagram** visualisering. Dra **modellen** till den **axel** området. Dra **engineOil**, **tirepressure**, **hastighet**, och **MaintenanceProbability** till den **värden** området. Ändra sina sammansättningstyp till **genomsnittlig**. 

    ![Sammansättningstyp](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

1. Ändra diagrammet **rubrik** till **genomsnittlig motorolja, däck hög belastning, hastighet och sannolikheten för underhåll av modell**.

    ![Området diagramrubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

1. Välj det tomma utrymmet för att lägga till en ny visualisering.

1. På den **visualiseringar** fliken den **punktdiagram** visualisering. Dra **modellen** till den **information** och **förklaring** områden. Dra **bränsle** till den **x-axeln** området. Ändra aggregering till **genomsnittlig**. Dra **engineTemperature** till den **y-axeln** området. Ändra aggregering till **genomsnittlig**. Dra **vin** till den **storlek** området.

    ![Information, förklaring, axel och storlek områden](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

1. Ändra diagrammet **rubrik** till **medelvärde för bränsle engineTemperature, genomsnitt och antal vin modellen**.

    ![Punktdiagram diagramrubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Slutgiltiga ser ut som i följande exempel:

    ![Slutrapporten](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Fästa visualiseringar från rapporterna på instrumentpanelen i realtid
1. Skapa en tom instrumentpanel genom att välja på plustecknet bredvid **instrumentpaneler**. Ange namnet **Vehicle Telemetry Analytics-instrumentpanelen**.

    ![Instrumentpanelen plus symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

1. Fästa visualiseringar från de tidigare rapporterna på instrumentpanelen. 

    ![Symbol för instrumentpanelen PIN-kod](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    När alla tre rapporter är fäst på instrumentpanelen, ska det se ut som i följande exempel. Om du inte har skapat alla rapporter, kan du se annorlunda ut din instrumentpanel. 

    ![Instrumentpanel med rapporter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Du har skapat i realtid instrumentpanelen. När du fortsätter att köra CarEventGenerator.exe och RealtimeDashboardApp.exe Se live uppdateringar på instrumentpanelen. Följande steg ta ungefär 10 – 15 minuter för att slutföra.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Konfigurera Power BI-instrumentpanel batch-bearbetning
> [!NOTE]
> Det tar ungefär två timmar (från distributionen slutförs) för slutpunkt till slutpunkt-pipeline för att avsluta körningen och bearbeta ett år skapas för batchbearbetning. Vänta på att bearbetningen ska slutföras innan du fortsätter med följande steg:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Ladda ned Power BI designer-filen

1. En förkonfigurerade Power BI designer fil ingår som en del av distributionsanvisningarna för manuell åtgärd. Leta efter ”2. Ställ in PowerBI-instrumentpanel batch-bearbetning ”.

1. Ladda ned Power BI-mall för batchbearbetning instrumentpanel som här kallas **ConnectedCarsPbiReport.pbix**.

1. Spara den lokalt.

### <a name="configure-power-bi-reports"></a>Konfigurera Power BI-rapporter

1. Öppna filen designer **ConnectedCarsPbiReport.pbix** med hjälp av Power BI Desktop. Om du inte redan har det kan du installera Power BI Desktop från den [Power BI Desktop-installationen](https://www.microsoft.com/download/details.aspx?id=45331) webbplats.

1. Välj **redigera frågor**.

    ![Redigera frågor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

1. Dubbelklicka på **källa**.

    ![Källa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

1. Uppdatera server-anslutningssträngen med Azure SQL-server som har etablerats som en del av distributionen. Titta i anvisningarna för manuell åtgärd under Azure SQL-databas:

    * Server: somethingsrv.database.windows.net
    * Databas: connectedcar
    * Användarnamn: användarnamn
    * Lösenord: Du kan hantera dina SQL Server-lösenord från Azure-portalen.

1. Lämna **databasen** som **connectedcar**.

    ![Databas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

1. Välj **OK**.

1. Den **Windows-autentiseringsuppgifter** är markerad som standard. Ändra det till **databasen autentiseringsuppgifter** genom att välja den **databasen** fliken till höger.

1. Ange den **användarnamn** och **lösenord** för din Azure SQL-databas som angavs under installationen av dess distribution.

    ![Databasautentiseringsuppgifter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

1. Välj **Anslut**.

1. Upprepa föregående steg för var och en av de tre återstående frågorna finns i den högra rutan. Uppdatera informationen om datakällan anslutning.

1. Välj **stänga och läsa in**. Datauppsättningar för Power BI Desktop-fil är ansluten till SQL database-tabeller.

1. Välj **Stäng** att Stäng Power BI Desktop-filen.

    ![Stäng](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

1. Välj **spara** att spara ändringarna. 

Du har nu konfigurerat alla rapporter som motsvarar sökvägen i lösningen för batchbearbetning. 

## <a name="upload-to-powerbicom"></a>Ladda upp till powerbi.com
1. Gå till den [webbportalen för Power BI](http://powerbi.com), och logga in.

1. Välj **Hämta data**.

1. Överför Power BI Desktop-filen. Välj **hämta Data** > **filer hämta** > **lokal fil**.

1. Gå till **ConnectedCarsPbiReport.pbix**.

1. När filen har överförts kan du gå tillbaka till din Power BI-arbetsyta. En datauppsättning, en rapport och en tom instrumentpanel skapas åt dig.  

1. Fäst diagram för en ny instrumentpanel kallas **Vehicle Telemetry Analytics-instrumentpanelen** i Power BI. Välj tom instrumentpanelen som skapades tidigare och gå sedan till den **rapporter** avsnittet. Välj den nyligen uppladdade rapporten.  

    ![Ny Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Rapporten har sex sidor:

    Sida 1: Vehicle densitet  
    Sidan 2: I realtid fordonshälsa  
    Sidan 3: Aggressivt driven fordon   
    Sida 4: Återkallade fordon  
    Sida 5: Bränsle effektivt driven fordon  
    Sidan 6: Contoso motorer-logotyp  

    ![Power BI-rapport med sex sidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

1. Från **sida 3**, fästa följande innehåll:  

    a. **Antal vin**  

   ![Sidan 3 antal vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Aggressivt drivs fordon av modellen – vattenfallsdiagram** 

   ![Sidan 3 diagram 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

1. Från **sida 5**, fästa följande innehåll: 

    a. **Antal vin**

   ![Sida 5 diagram 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Bränsleeffektiva fordon modellen: grupperat stående stapeldiagram**

   ![Sida 5 diagrammet 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

1. Från **sida 4**, fästa följande innehåll:  

    a. **Antal vin** 

   ![Sida 4 diagram 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Återkallade fordon per stad, modellera: Trädkarta**

   ![Sida 4 diagrammet 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

1. Från **sidan 6**, fästa följande innehåll:  

    * **Contoso motorer-logotyp**

    ![Contoso motorer-logotyp](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organisera instrumentpanelen  

1. Gå till instrumentpanelen.

1. Hovra över varje diagram. Byt namn på varje diagram baserat på namngivningen i exemplet nedan färdig instrumentpanel:

   ![Instrumentpanelen organisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
1. Flytta diagram cirka ska se ut som i följande exempel för instrumentpanelen:

    ![Ordningen har ändrats instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

1. När du har skapat alla rapporter som nämns i det här dokumentet sista färdig instrumentpanel ser ut som i följande exempel: 

   ![Sista instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Du skapat har i rapporter och instrumentpanelen för att få i realtid, förutsägande och batch insikter om fordonshälsa och körvanor.  
