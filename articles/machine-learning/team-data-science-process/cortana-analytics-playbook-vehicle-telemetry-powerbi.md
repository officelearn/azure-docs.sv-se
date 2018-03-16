---
title: "Power BI-instrumentpanel för vehicle hälso- och köra vanor - Azure | Microsoft Docs"
description: "Använda funktionerna i Cortana Intelligence och få insikter om i realtid och förutsägbara på vehicle hälsa och köra vanor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 6a46a05539a7211fbc2a3a9f6ec45a28f5c64039
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Vehicle telemetri Analytics lösning mallen Power BI dashboard instruktioner
Den här menyn innehåller länkar till kapitlen i den här playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Vehicle telemetri Analytics lösning showcases använda hur bil hos återförsäljarna, bil tillverkare och försäkringsbolag kan funktionerna i Cortana Intelligence. De kan hämta i realtid och förutsägbara insikter om vehicle hälsa och köra vanor att förbättra kundupplevelsen, forskning och utveckling och marknadsföringskampanjer. Dessa stegvisa anvisningar visar hur du konfigurerar Power BI-rapporter och instrumentpanelen när du distribuerar lösningen i din prenumeration. 

## <a name="prerequisites"></a>Förutsättningar
* Distribuera den [Vehicle telemetri Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) lösning. 
* [Installera Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Hämta en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/). Om du inte har en Azure-prenumeration, komma igång med den kostnadsfria Azure-prenumerationen.
* Öppna Power BI-konto.

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence suite-komponenter
Som en del av Vehicle telemetri Analytics lösning mallen kan distribueras följande Cortana Intelligence-tjänster i din prenumeration:

* **Händelsehubbar i Azure** en miljontals vehicle telemetriska händelser i Azure.
* **Azure Stream Analytics** ger realtidsinsikter på vehicle hälsa och kvarstår dessa data till långsiktig lagring för bättre batch analytics.
* **Azure Machine Learning** identifierar avvikelser i realtid och använder batch-bearbetning för att tillhandahålla förutsägbar insikter.
* **Azure HDInsight** omvandlar data i större skala.
* **Azure Data Factory** hanterar orchestration, schemaläggning, resurshantering och övervakning av batch-bearbetning-pipeline.

**Power BI** ger den här lösningen en omfattande instrumentpanel för data och förutsägelseanalys visualiseringar. 

Lösningen använder två olika datakällor:

* Simulerade vehicle signaler och diagnostik datauppsättningar
* Vehicle katalog

Vehicle telematik simulator ingår som en del av den här lösningen. Den skickar diagnostikinformation och signalerar som motsvarar vehicle och intresseväckande mönster vid en viss tidpunkt. 

Vehicle katalogen är en referens som mappar VINs till modeller.

## <a name="power-bi-dashboard-preparation"></a>Förberedelse av Power BI-instrumentpanel
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Ställ in realtid Power BI-instrumentpanelen

#### <a name="start-the-real-time-dashboard-application"></a>Starta programmet realtid instrumentpanelen
När distributionen är klar följer du instruktionerna för manuell åtgärd.

1. Hämta programmet realtid instrumentpanelen RealtimeDashboardApp.zip och packa upp den.

2.  Öppna appen konfigurationsfilen RealtimeDashboardApp.exe.config i mappen uppackade. Ersätt appSettings för Händelsehubbar, Azure Blob storage och Azure Machine Learning service-anslutningar med värdena i de manuella åtgärden-instruktionerna. Spara ändringarna.

3. Kör programmet RealtimeDashboardApp.exe. Ange giltig Power BI autentiseringsuppgifter i fönstret inloggning. 

   ![Power BI-inloggning fönster](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Välj **acceptera**. Appen startar.

   ![Behörigheter för Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Logga in till Power BI-webbplatsen och skapa en realtid instrumentpanel.

Nu är du redo att konfigurera Power BI-instrumentpanelen.  

### <a name="configure-power-bi-reports"></a>Konfigurera Power BI-rapporter
Ta ungefär 30 till 45 minuter för att slutföra realtid rapporter och på instrumentpanelen. 

1. Bläddra till den [Power BI](http://powerbi.com) webbsidan, och logga in.

    ![Power BI-inloggningssida](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. En ny datauppsättning skapas i Power BI. Välj den **ConnectedCarsRealtime** datauppsättning.

    ![ConnectedCarsRealtime datauppsättning](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Tryck på Ctrl + S för att spara rapporten tom.

    ![Tom rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Ange rapportens namn **Vehicle telemetri Analytics realtid - rapporter**.

    ![Rapportnamn](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Realtid rapporter
Tre realtid rapporter finns i den här lösningen:

* Fordon i åtgärden
* Fordon kräver Underhåll
* Vehicle hälsostatistik

Du kan konfigurera alla tre rapporterna eller du kan stoppa efter någon gång. Du kan sedan fortsätta till nästa avsnitt om hur du konfigurerar batch-rapporter. Vi rekommenderar att du skapar alla tre rapporter att visualisera fullständig insikter i realtid sökvägen för lösningen.  

### <a name="vehicles-in-operation-report"></a>Fordon i rapporten igen
1. Dubbelklicka på **sida 1**, och byta namn på den **fordon i åtgärden**.

    ![Fordon i åtgärden](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. På den **fält** väljer **vin**. På den **visualiseringar** väljer den **kort** visualiseringen.  

    Den **kort** visualiseringen har skapats enligt följande bild:

    ![Välj vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Välj det tomma utrymmet för att lägga till en ny visualisering.  

4. På den **fält** väljer **Stad** och **vin**. På den **visualiseringar** väljer den **kartan** visualiseringen. Dra **vin** till den **värden** område. Dra **Stad** till den **förklaring** område. 

    ![Visualisering av kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. På den **visualiseringar** väljer den **Format** avsnitt. Välj **rubrik**, och ändra **Text** till **fordon i åtgärd efter ort**.

    ![Fordon i åtgärd efter ort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Sista visualiseringen ser ut som i följande exempel:

    ![Sista visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Välj det tomma utrymmet för att lägga till en ny visualisering.  

7. På den **fält** väljer **Stad** och **vin**. På den **visualiseringar** väljer den **grupperat stående stapeldiagram** visualiseringen. Dra **Stad** till den **axel** område. Dra **vin** till den **värdet** område.

8. Sortera diagram av **antal vin**.

    ![Antal vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Ändra diagrammet **rubrik** till **fordon i åtgärd efter ort**. 

10. Välj den **Format** avsnittet och väljer sedan **Data färger**. Ändra **visa alla** till **på**.

    ![Data färger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Ändra färg på en enskild ort genom att välja färg symbolen.

    ![Ändra färg](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Välj det tomma utrymmet för att lägga till en ny visualisering.  

13. På den **visualiseringar** väljer den **grupperat stående stapeldiagram** visualiseringen. På den **fält** fliken genom att dra **Stad** till den **axel** område. Dra **modellen** till den **förklaring** område. Dra **vin** till den **värdet** område.

    ![Grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Diagrammet ser ut som följande bild:

    ![Rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Flytta alla visualiseringar så att sidan ser ut som följande exempel:

    ![Instrumentpanel med grafik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Du har konfigurerat ”fordon i åtgärden” rapporten. Du kan skapa nästa realtid rapport eller du kan stoppa här och konfigurera instrumentpanelen. 

### <a name="vehicles-requiring-maintenance-report"></a>Fordon kräver Underhåll rapport

1. Välj ![Lägg till](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) att lägga till en ny rapport. Byt namn på den **fordon kräver Underhåll**.

    ![Fordon kräver Underhåll](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. På den **fält** väljer **vin**. På den **visualiseringar** väljer den **kort** visualiseringen.

    ![Visualisering av vin-kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Datamängden som innehåller ett fält med namnet **MaintenanceLabel**. Det här fältet kan ha värdet ”0” eller ”1”. Värdet som maskininlärningsmodell som har etablerats som en del av lösningen. Det är integrerat med realtid sökvägen. Värdet ”1” anger att ett fordon kräver underhåll. 

3. Att lägga till en **nivå sidfilter** att visa data för de fordon som kräver Underhåll: 

   a. Dra den **MaintenanceLabel** till **nivå sidfilter**.
  
      ![Sidnivå filter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Längst ned i **sidan nivå filter MaintenanceLabel**väljer **grundläggande filtrering**.

      ![Grundläggande filtrering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Filtervärdet **1**.

      ![Filtervärdet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Välj det tomma utrymmet för att lägga till en ny visualisering.  

5. På den **visualiseringar** väljer den **grupperat stående stapeldiagram** visualiseringen. 

    ![Vin Card](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. På den **fält** fliken genom att dra **modellen** till den **axel** område. Dra **vin** till den **värdet** område. Sortera visualisering av **antal vin**. Ändra diagrammet **rubrik** till **fordon kräver underhåll av modell**. 

7. På den **fält** ![fält avbildningen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) avsnitt i den **visualiseringar** fliken genom att dra **vin** till **färgmättnad**.

    ![Färgmättnad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. På den **Format** ändrar **Data färger** i visualiseringen: 

    a. Ändra den **minsta** färg till **F2C812**.

    b. Ändra den **maximala** färg till **FF6300**.

    ![Nya data färger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Nya visualiseringsfärger ser ut som följande exempel:

    ![Den nya visualiseringsfärger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Välj det tomma utrymmet för att lägga till en ny visualisering.  

10. På den **visualiseringar** väljer **grupperat stående stapeldiagram**. Dra **vin** till den **värdet** område. Dra **Stad** till den **axel** område. Sortera diagram av **antal vin**. Ändra diagrammet **rubrik** till **fordon kräver underhåll av ort**.

    ![Fordon kräver underhåll av ort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Välj det tomma utrymmet för att lägga till en ny visualisering.  

12. På den **visualiseringar** väljer den **flerradiga kort** visualiseringen. Dra **modellen** och **vin** till den **fält** område.

    ![Flerradiga-kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Flytta alla visualiseringar så att den slutgiltiga rapporten ser ut som följande exempel: 

    ![Ordningen har ändrats rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Du har konfigurerat rapporten ”fordon kräver Underhåll” realtid. Du kan skapa nästa realtid rapport eller du kan stoppa här och konfigurera instrumentpanelen. 

### <a name="vehicle-health-statistics-report"></a>Vehicle hälsostatistik rapport

1. Välj ![Lägg till](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) att lägga till en ny rapport. Byt namn på den **fordon hälsostatistik**. 

2. På den **visualiseringar** väljer den **mätaren** visualiseringen. Dra **hastighet** till den **värdet**, **minimalt värde**, och **maxvärdet** områden.

   ![Fordon hälsostatistik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. I den **värdet** område, ändra standard sammanställning **hastighet** till **genomsnittlig**.

4. I den **minimalt värde** område, ändra standard sammanställning **hastighet** till **minsta**.

5. I den **maxvärdet** område, ändra standard sammanställning **hastighet** till **maximala**.

   ![Värden för hastighet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Byt namn på den **mätaren rubrik** till **genomsnittlig hastighet**.

   ![Mätare](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Välj det tomma utrymmet för att lägga till en ny visualisering.  

    Lägg också till en **mätaren** för **genomsnittlig motorolja**, **genomsnittlig bränsle**, och **genomsnittlig motorn temperatur**.  

8. Ändra standard aggregering av fälten i varje mätaren som du gjorde i föregående steg i den **genomsnittlig hastighet** mätare.

    ![Ytterligare mätare](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Välj det tomma utrymmet för att lägga till en ny visualisering.

10. På den **visualiseringar** väljer den **linjediagram och grupperat stående stapeldiagram** visualiseringen. Dra **Stad** till **delade axel**. Dra **tirepressure**, **engineoil**, och **hastighet** till den **kolumnvärdena** område. Ändra sina sammansättningstyp till **genomsnittlig**. 

11. Dra **engineTemperature** till den **radvärden** område. Ändra aggregeringstypen till **genomsnittlig**. 

    ![Kolumn- och värden för en linje](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Ändra diagrammet **rubrik** till **genomsnittlig hastighet, däck tryck, motorolja och motorn temperatur**.  

    ![Linjediagram och grupperat stående stapeldiagram rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Välj det tomma utrymmet för att lägga till en ny visualisering.

14. På den **visualiseringar** väljer den **Treemap** visualiseringen. Dra **modellen** till den **grupp** område. Dra **MaintenanceProbability** till den **värden** område.

15. Ändra diagrammet **rubrik** till **Vehicle modeller som kräver Underhåll**.

    ![Treemap rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Välj det tomma utrymmet för att lägga till en ny visualisering.

17. På den **visualiseringar** väljer den **100% staplad liggande diagram** visualiseringen. Dra **Stad** till den **axel** område. Dra **MaintenanceProbability** och **RecallProbability** till den **värdet** område.

    ![Axel-och värdet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. På den **Format** väljer **Data färger**. Ange den **MaintenanceProbability** färg till värdet **F2C80F**.

19. Ändra diagrammet **rubrik** till **sannolikhet Vehicle Underhåll & återkallning efter ort**.

    ![100% staplad liggande stapeldiagram rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Välj det tomma utrymmet för att lägga till en ny visualisering.

21. På den **visualiseringar** väljer den **ytdiagram** visualiseringen. Dra **modellen** till den **axel** område. Dra **engineOil**, **tirepressure**, **hastighet**, och **MaintenanceProbability** till den **värden** området. Ändra sina sammansättningstyp till **genomsnittlig**. 

    ![Sammansättningstyp](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Ändra diagrammet **rubrik** till **genomsnittlig motorolja, däck tryck, hastighet och sannolikheten för underhåll av modell**.

    ![Området diagrammets rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Välj det tomma utrymmet för att lägga till en ny visualisering.

24. På den **visualiseringar** väljer den **punktdiagram** visualiseringen. Dra **modellen** till den **information** och **förklaring** områden. Dra **bränsle** till den **x-axeln** område. Ändra aggregering till **genomsnittlig**. Dra **engineTemperature** till den **y-axeln** område. Ändra aggregering till **genomsnittlig**. Dra **vin** till den **storlek** område.

    ![Information, förklaring och axelns storlek områden](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Ändra diagrammet **rubrik** till **medelvärde för bränsle medelvärdet av engineTemperature och antal vin av modellen och modell**.

    ![Punktdiagram diagrammets rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Den slutliga rapporten ser ut som i följande exempel:

    ![Slutgiltig rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>PIN-kod visualiseringar från rapporterna i realtid instrumentpanel
1. Skapa en tom instrumentpanelen genom att välja på plustecknet bredvid **instrumentpaneler**. Ange namnet **Vehicle telemetri instrumentpanelen**.

    ![Instrumentpanelen plus symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Fäst visualiseringar från tidigare rapporter på instrumentpanelen. 

    ![Instrumentpanelen PIN-kod symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    När alla tre rapporter är fäst på instrumentpanelen, bör det se ut som följande exempel. Om du inte har skapat alla rapporter, se instrumentpanelen annorlunda ut. 

    ![Instrumentpanel med rapporter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Du har skapat realtid instrumentpanelen. När du fortsätter att köra CarEventGenerator.exe och RealtimeDashboardApp.exe Se live uppdateringar på instrumentpanelen. Följande steg ta ungefär 10 – 15 minuter för att slutföra.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Ställ in Power BI-instrumentpanel batch bearbetning
> [!NOTE]
> Det tar ca två timmar (från distributionen lyckades) för slutpunkt till slutpunkt-pipelinen för att avsluta körningen och bearbeta ett år som skapas för batchbearbetning. Vänta tills bearbetningen måste slutföras innan du går vidare med följande steg:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Hämta filen Power BI designer

1. En förkonfigurerad Power BI designer fil ingår som en del av de manuella åtgärden anvisningarna för distribution. Sök efter ”2. Ställ in infopanelen PowerBI batch-bearbetning ”.

2. Hämta Power BI-mall för batchbearbetning instrumentpanelen namnet **ConnectedCarsPbiReport.pbix**.

3. Spara filen lokalt.

### <a name="configure-power-bi-reports"></a>Konfigurera Power BI-rapporter

1. Öppna filen designer **ConnectedCarsPbiReport.pbix** med hjälp av Power BI Desktop. Om du inte redan har det, installera Power BI Desktop från den [Power BI Desktop installation](http://www.microsoft.com/download/details.aspx?id=45331) webbplats.

2. Välj **redigera frågor**.

    ![Redigera frågor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Dubbelklicka på **källa**.

    ![Källa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Uppdatera anslutningssträngen server med Azure SQL-server som har etablerats som en del av distributionen. Leta i Manuell operation anvisningarna under Azure SQL-databas:

    * Server: somethingsrv.database.windows.net
    * Databas: connectedcar
    * Användarnamn: användarnamn
    * Lösenord: Du kan hantera ditt lösenord för SQL Server från Azure-portalen.

5. Lämna **databasen** som **connectedcar**.

    ![Databas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Välj **OK**.

7. Den **Windows-autentiseringsuppgifter** är markerad som standard. Ändra den till **databasen autentiseringsuppgifter** genom att välja den **databasen** fliken längst till höger.

8. Ange den **användarnamn** och **lösenord** för din Azure SQL-databas som angavs under installationen för distribution.

    ![Databasautentiseringsuppgifter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Välj **Anslut**.

10. Upprepa de här stegen för alla tre återstående frågor finns i den högra rutan. Uppdatera information om datakälla anslutning.

11. Välj **Stäng och läsa in**. Datauppsättningar för Power BI Desktop-filen är anslutna till SQL-databastabeller.

12. Välj **Stäng** Stäng Power BI Desktop-filen.

    ![Stäng](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Välj **spara** spara ändringarna. 

Du har nu konfigurerat alla rapporter som motsvarar batchbearbetning sökväg i lösningen. 

## <a name="upload-to-powerbicom"></a>Överför till powerbi.com
1. Gå till den [Power BI-webbportalen](http://powerbi.com), och logga in.

2. Välj **hämta Data**.

3. Ladda upp filen Power BI Desktop. Välj **hämta Data** > **filer hämta** > **lokal fil**.

4. Gå till **ConnectedCarsPbiReport.pbix**.

5. När filen har överförts kan du gå tillbaka till arbetsplatsen Power BI. En datamängd, en rapport och en tom instrumentpanel skapas för dig.  

6. Diagram för PIN-kod till en ny instrumentpanel kallas **Vehicle telemetri instrumentpanelen** i Power BI. Välj Tom instrumentpanel som skapades tidigare och gå sedan till den **rapporter** avsnitt. Välj en nyligen uppladdade rapport.  

    ![Nya Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Rapporten har sex sidor:

    Sidan 1: Vehicle densitet  
    Sidan 2: Realtid vehicle hälsa  
    Sidan 3: Aggressivt drivs fordon   
    Sidan 4: Återkallas fordon  
    Sidan 5: Bränsle effektivt drivs fordon  
    Sidan 6: Contoso motorer logotyp  

    ![Power BI-rapport med sex sidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. Från **sida 3**, fästa följande innehåll:  

    a. **Antal vin**  

   ![Sidan 3 antal vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Aggressivt drivs fordon av modellen – vattenfallet diagram** 

   ![Sidan 3 diagram 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. Från **sidan 5**, fästa följande innehåll: 

    a. **Antal vin**

   ![Sidan 5 diagram 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Bränsleeffektiva fordon av modell: grupperat stående stapeldiagram**

   ![Sidan 5 diagram 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. Från **sida 4**, fästa följande innehåll:  

    a. **Antal vin** 

   ![Sidan 4 diagram 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Återkallade fordon efter ort, modell: Treemap**

   ![Sidan 4 diagram 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. Från **sidan 6**, fästa följande innehåll:  

    * **Contoso motorer-logotyp**

    ![Contoso motorer-logotyp](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Ordna instrumentpanelen  

1. Gå till instrumentpanelen.

2. Hovra över varje diagram. Byt namn på varje diagram baserat på namn som i exemplet nedan färdig instrumentpanel:

   ![Instrumentpanelen organisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Flytta diagram runt ska se ut som i följande exempel instrumentpanelen:

    ![Instrumentpanel för ordningen har ändrats](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. När du har skapat alla rapporter som nämns i det här dokumentet slutliga klar instrumentpanelen som ser ut som i följande exempel: 

   ![Sista instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Du har skapat rapporterna och instrumentpanelen för att få realtid, förutsägbara och batch insikter om vehicle hälsa och köra vanor.  
