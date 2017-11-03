---
title: "Power BI-instrumentpanelen för hälsotillstånd vehicle och andra vanor - Azure | Microsoft Docs"
description: "Använda funktionerna i Cortana Intelligence och få insikter om i realtid och förutsägbara på vehicle hälsa och köra vanor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
ms.openlocfilehash: 39be936520d62cb1c1c28de9bd72f8f489166082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Vehicle telemetri analytics lösning mallen Power BI-instrumentpanel instruktioner
Detta **menyn** länkar till kapitlen i den här playbook. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Vehicle telemetri Analytics lösningen visar hur bil hos återförsäljarna, bil tillverkare och försäkringsbolag kan utnyttja funktionerna i Cortana Intelligence och insyn i realtid och förutsägbara på vehicle hälsa och intresseväckande vanor enhet förbättringar i området för kunden får, R & D och marknadsföringskampanjer. Det här dokumentet innehåller steg-för-steg-anvisningar om hur du kan konfigurera en Power BI-rapporter och instrumentpanelen när lösningen har distribuerats i din prenumeration. 

## <a name="prerequisites"></a>Krav
1. Distribuera den [telemetri Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) lösning  
2. [Installera Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3. En [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/). Om du inte har en Azure-prenumeration, komma igång med Azure kostnadsfri prenumeration
4. Microsoft Power BI-konto

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence Suite-komponenter
Som en del av lösningsmall Vehicle telemetri Analytics distribueras följande Cortana Intelligence-tjänster i din prenumeration.

* **Event Hub** för att föra in miljontals vehicle telemetriska händelser i Azure.
* **Strömma Analytics** för att få realtidsinsikter på vehicle hälsa och kvarstår dessa data till långsiktig lagring för bättre batch analytics.
* **Machine Learning** för identifiering av avvikelse i realtid och batchbearbetning och få förutsägande insikter.
* **HDInsight** utnyttjas för att omvandla data i skala
* **Data Factory** hanterar orchestration, schemaläggning, resurshantering och övervakning av batch-bearbetning-pipeline.

**Power BI** ger den här lösningen en omfattande instrumentpanel för data i realtid och förutsägelseanalys visualiseringar. 

Lösningen använder två olika datakällor: **simulerade vehicle signaler och diagnostik dataset** och **vehicle katalogen**.

Vehicle telematik simulator ingår som en del av den här lösningen. Den genererar diagnostisk information och signalerar till motsvarande tillstånd för programuppdatering och andra mönster vid en viss tidpunkt. 

Vehicle katalogen är referens dataset som innehåller Registreringsnumret för Modellmappning

## <a name="power-bi-dashboard-preparation"></a>Förberedelse av Power BI-instrumentpanel
### <a name="setup-power-bi-real-time-dashboard"></a>Konfigurera realtid Power BI-instrumentpanel

**Starta programmet realtid instrumentpanelen** när distributionen är klar bör du följa anvisningarna för manuell åtgärd

* Hämta realtid instrumentpanelen programmet RealtimeDashboardApp.zip och packa upp den.
*  Öppna appen konfigurationsfilen RealtimeDashboardApp.exe.config, Ersätt appSettings för Eventhub, Blob Storage och ML service-anslutningar med värden i manuell åtgärd instruktioner och spara ändringarna i den uppackade mappen.
* Kör program RealtimeDashboardApp.exe. Ett inloggningsfönster kommer popup-, ange din giltiga PowerBI-autentiseringsuppgifter och klicka på den **acceptera** knappen. Sedan startar appen.

   ![Logga in till Powerbi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Power BI-instrumentpanel behörigheter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* Inloggning till PowerBI-webbplatsen och skapa instrumentpanel i realtid.

Nu är du redo att konfigurera Power BI-instrumentpanel med omfattande visualiseringar att få realtid och förutsägbara insikter om vehicle hälsa och köra vanor. Det tar cirka 45 minuter till en timme att skapa alla rapporter och konfigurera instrumentpanelen. 

### <a name="configure-power-bi-reports"></a>Konfigurera Power BI-rapporter
Realtid rapporter och instrumentpanelen ta ungefär 30-45 minuter för att slutföra. Bläddra till [http://powerbi.com](http://powerbi.com) och logga in.

![Logga in till Powerbi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

En ny datamängd skapas i Power BI. Klicka på den **ConnectedCarsRealtime** dataset.

![Markerad anslutna bilar realtid dataset](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Spara en tom rapport med hjälp av **Ctrl + s**.

![Spara tom rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Ange rapportens namn *Vehicle telemetri Analytics realtid - rapporter*.

![Ange rapportens namn](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Realtid rapporter
Det finns tre realtid rapporter i den här lösningen:

1. Fordon i åtgärden
2. Fordon kräver Underhåll
3. Fordon hälsostatistik

Du kan välja att konfigurera tre realtid rapporter eller stoppa efter någon gång och fortsätta till nästa avsnitt av batch-rapporter. Vi rekommenderar att du kan skapa tre rapporter att visualisera fullständig insikter i realtid sökvägen för lösningen.  

### <a name="1-vehicles-in-operation"></a>1. Fordon i åtgärden
Dubbelklicka på **sida 1** och Byt till ”fordon i åtgärden”  
    ![Anslutna bilar - fordon i åtgärd](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Välj **vin** från **fält** och välj typ av visualiseringen som **”kort”**.  

Kort visualiseringen skapas som visas i bild.  
    ![Anslutna bilar - väljer vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Välj **Stad** och **vin** från fält. Ändra visualiseringen för **”karta”**. Dra **vin** i värdeområdet. Dra **Stad** från fält till **förklaring** område.   
    ![Ansluten bilar - kort visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

Välj **format** avsnittet från **visualiseringar**, klickar du på **rubrik** och ändra den **Text** till **”fordon i åtgärd efter ort”**.  
    ![Anslutna bilar - fordon i åtgärd efter ort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

Sista visualiseringen ser ut som visas i bild.    
    ![Anslutna bilar - slutliga visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Välj **Stad** och **vin**, ändra typen av visualisering till **grupperat stående stapeldiagram**. Se till att **Stad** i **axel området** och **vin** i **värdet område**  

Sortera diagram av **”antal vin”**  
    ![Anslutna bilar - antal vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Ändra diagramtyp **rubrik** till **”fordon i åtgärd efter ort”**  

Klicka på den **Format** avsnittet och väljer sedan **Data färger**, klickar du på den **”på”** till **visa alla**  
    ![Anslutna bilar – visa alla Data färger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Ändra färgen på enskilda ort genom att klicka på ikonen färg.  
    ![Ansluten bilar - ändra färger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Välj **grupperat stående stapeldiagram** visualiseringen från visualiseringar, dra **Stad** i **axel** området **modellen** i **förklaring** området och **vin** i **värdet** område.  
    ![Anslutna bilar - grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Anslutna bilar - återgivning](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

Ordna om alla visualiseringen på den här sidan som visas i bild.  
    ![Anslutna bilar - visualiseringar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Du har konfigurerat ”fordon i åtgärden” realtid rapporten. Du kan fortsätta att skapa nästa realtid rapport eller stoppa här och konfigurera instrumentpanelen. 

### <a name="2-vehicles-requiring-maintenance"></a>2. Fordon kräver Underhåll
Klicka på ![Lägg till](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) om du vill lägga till en ny rapport, byta namn på den till **”fordon kräver Underhåll”**

![Anslutna bilar - fordon kräver Underhåll](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Välj **vin** fältet och ändra typen av visualisering till **kort**.  
    ![Anslutna bilar - Vin kort visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Vi har ett fält med namnet ”MaintenanceLabel” i datauppsättningen. Det här fältet kan ha värdet ”0” eller ”1” ”. Den anges av Azure Machine Learning-modell etablerats som en del av lösningen och integrerats med realtid sökvägen. Värdet ”1” anger fordon kräver underhåll. 

Att lägga till en **sidnivå** filter för att visa fordon data, vilket kräver Underhåll: 

1. Dra den **”MaintenanceLabel”** omvandlas **nivå sidfilter**.  
   ![Anslutna bilar - sidnivå filter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. Klicka på **grundläggande filtrering** menyn som finns längst ned i MaintenanceLabel sidfilter nivå.  
   ![Ansluten bilar - grundläggande filtrering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. Ange filtervärdet **”1”**    
   ![Anslutna bilar - filtervärdet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Välj **grupperat stående stapeldiagram** från visualiseringar  
![Anslutna bilar - Vind kort visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Anslutna bilar - grupperat stående stapeldiagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Dra fältet **modellen** till **axel** området **Vin** till **värdet** område. Sortera visualisering av **antal vin**.  Ändra diagramtyp **rubrik** till **”fordon kräver underhåll av modell”**  

Dra **vin** fält i **färgmättnad** finns på **fält** ![fält](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) avsnitt i **visualiseringen** fliken  
![Anslutna bilar - färgmättnad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Ändra **Data färger** i visualiseringar från **Format** avsnitt  
Ändra minimifärg till: **F2C812**  
Ändra maxfärg till: **FF6300**  
![Ansluten bilar - ändringar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Ansluten bilar - nya Visualiseringsfärger](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Välj **klustrade stapeldiagram** dra från visualiseringar, **vin** fältet i **värdet** område, dra **Stad** omvandlas **axel** område. Sortera diagram av **”antal vin”**. Ändra diagramtyp **rubrik** till **”fordon kräver underhåll av ort”**   
![Anslutna bilar - fordon kräver underhåll av ort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Välj **flerradiga kort** visualiseringen från visualiseringar, dra **modellen** och **vin** till den **fält** område.  
![Anslutna bilar - flerradiga-kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

Flytta alla visualiseringen den slutgiltiga rapporten ser ut som följer:  
![Anslutna bilar - flerradiga-kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Du har konfigurerat rapporten ”fordon kräver Underhåll” realtid. Du kan fortsätta att skapa nästa realtid rapport eller stoppa här och konfigurera instrumentpanelen. 

### <a name="3-vehicles-health-statistics"></a>3. Fordon hälsostatistik
Klicka på ![Lägg till](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) om du vill lägga till ny rapport, byta namn på den till **”fordon hälsostatistik”**  

Välj **mätaren** visualiseringen från visualiseringar, drar den **hastighet** omvandlas **värde, minimalt värde maxvärdet** områden.  
![Anslutna bilar - flerradiga-kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Ändra standard sammanställning **hastighet** i **värdet området** till **Genomsnittlig** 

Ändra standard sammanställning **hastighet** i **minsta område** till **minsta**

Ändra standard sammanställning **hastighet** i **maximalt området** till **maximala**

![Anslutna bilar - flerradiga-kort](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Byt namn på den **mätaren rubrik** till **”genomsnittlig hastighet”** 

![Anslutna bilar - mätare](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.  

Lägga till en **mätaren** för **genomsnittlig motorolja**, **genomsnittlig bränsle**, och **genomsnittlig motorn tempererade**.  

Ändra standard aggregering av fälten i varje mätaren enligt ovan stegen i **”genomsnittlig hastighet”** mätare.

![Anslutna bilar - mätare](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.

Välj **linjediagram och grupperat stående stapeldiagram** från visualiseringar, dra **Stad** omvandlas **delade axel**, dra **hastighet**, **fälten tirepressure och engineoil** till **kolumnvärdena** området ändra sina sammansättningstyp till **genomsnittlig**. 

Dra den **engineTemperature** omvandlas **radvärden** område, ändra Aggregeringstyp till **genomsnittlig**. 

![Anslutna bilar - visualiseringar fält](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Ändra diagrammet **rubrik** till **”genomsnittlig hastighet, däck tryck, motorolja och motorn temperatur”**.  

![Anslutna bilar - visualiseringar fält](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.

Välj **Treemap** visualiseringen från visualiseringar, drar den **modellen** omvandlas den **grupp** området och dra fältet **MaintenanceProbability** i den **värden** område.

Ändra diagrammet **rubrik** till **”Vehicle modeller som kräver Underhåll”**.

![Ansluten bilar - ändra diagrammets rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.

Välj **100% staplad liggande diagram** dra från visualisering, den **Stad** omvandlas den **axel** området och dra den **MaintenanceProbability**, **RecallProbability** fält i den **värdet** område.

![Anslutna bilar - Lägg till nya visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Klicka på **Format**väljer **Data färger**, och ange den **MaintenanceProbability** färg till värdet **”F2C80F”**.

Ändra den **rubrik** diagrammet genom att **”sannolikheten för Vehicle underhåll och återkalla av ort”**.

![Anslutna bilar - Lägg till nya visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen.

Välj **ytdiagram** dra från visualiseringen från visualiseringar i **modellen** omvandlas den **axel** området och dra den **engineOil tirepressure, hastighet och MaintenanceProbability** fält i den **värden** område. Ändra sina sammansättningstyp till **”genomsnittliga”**. 

![Ansluten bilar - ändra sammansättningstyp](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Ändra titeln på diagrammet på **”genomsnittlig motorolja, tröttnar sannolikheten för hög belastning, hastighet och underhåll av modell”**.

![Ansluten bilar - ändra diagrammets rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Klicka på det tomma utrymmet för att lägga till nya visualiseringen:

1. Välj **punktdiagram** visualiseringen från visualiseringar.
2. Dra den **modellen** omvandlas den **information** och **förklaring** område.
3. Dra den **bränsle** omvandlas den **x-axeln** område, ändra aggregering till **genomsnittlig**.
4. Dra **engineTemparature** till **y-axeln området**, ändra aggregering till **Genomsnittlig**
5. Dra den **vin** omvandlas den **storlek** område.

![Anslutna bilar - Lägg till nya visualiseringen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Ändra diagrammet **rubrik** till **”medelvärden bränsle, motorn temperatur av modell”**.

![Ansluten bilar - ändra diagrammets rubrik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Den slutliga rapporten ser ut som nedan.

![Ansluten bilar-slutgiltig rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>PIN-kod visualiseringar från rapporterna i realtid instrumentpanel
Skapa en tom instrumentpanel genom att klicka på plusikonen bredvid instrumentpaneler. Du kan kalla den ”Vehicle telemetri instrumentpanelen”

![Anslutna bilar-instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Fäst visualiseringen från ovan rapporterna på instrumentpanelen. 

![Anslutna bilar-instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Instrumentpanelen ska se ut så här när alla tre rapporter skapas och motsvarande visualiseringar är fäst på instrumentpanelen. Om du inte har skapat alla rapporter kan instrumentpanelen se annorlunda ut. 

![Anslutna bilar-instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Grattis! Du har skapat realtid instrumentpanelen. När du fortsätter att köra CarEventGenerator.exe och RealtimeDashboardApp.exe bör du se live uppdateringar på instrumentpanelen. Det bör ta ungefär 10 – 15 minuter för att slutföra följande steg.

## <a name="setup-power-bi-batch-processing-dashboard"></a>Installera Power BI batch-bearbetning instrumentpanel
> [!NOTE]
> Det tar ca två timmar (från distributionen lyckades) för slutpunkt till slutpunkt batchbearbetning pipelinen Slutför körning och bearbetar ett års värt att skapas. Så vänta tills bearbetningen ska slutföras innan du fortsätter med nästa steg. 
> 
> 

**Hämta filen Power BI designer**

* En förkonfigurerad Power BI designer fil ingår som en del av distributionen manuell åtgärd instruktioner
* Leta efter 2. Installationsprogrammet PowerBI batch bearbetning instrumentpanelen som du kan hämta PowerBI-mallen för batchbearbetning instrumentpanelen namnet **ConnectedCarsPbiReport.pbix**.
* Spara lokalt

**Konfigurera Power BI-rapporter**

* Öppna filen designer '**ConnectedCarsPbiReport.pbix**' med hjälp av Power BI Desktop. Om du inte redan har, installera Power BI Desktop från [Power BI Desktop installera](http://www.microsoft.com/download/details.aspx?id=45331). 
* Klicka på den **redigera frågor**.

![Redigera Power BI-fråga](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* Dubbelklicka på den **källa**.

![Ange Power BI-källa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* Uppdatera Server-anslutningssträngen med Azure SQL-server som har etablerats som en del av distributionen.  Leta i anvisningarna under manuell åtgärd 

    4. Azure SQL Database
    
    * Server: somethingsrv.database.windows.net
    * Databas: connectedcar
    * Användarnamn: användarnamn
    * Lösenord: Du kan hantera ditt lösenord för SQL server från Azure-portalen

* Lämna **databasen** som *connectedcar*.

![Ange Power BI-databasen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* Klicka på **OK**.
* Du ser **Windows-autentiseringsuppgifter** fliken markerad som standard kan ändra det till **databasen autentiseringsuppgifter** genom att klicka på **databasen** fliken längst till höger.
* Ange den **användarnamn** och **lösenord** i din Azure SQL Database som angavs under installationen för distribution.

![Ange Databasautentiseringsuppgifter för](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* Klicka på **ansluta**
* Upprepa stegen ovan för alla tre återstående frågor finns i högra fönstret och uppdatera information om datakälla anslutning.
* Klicka på **Stäng och läsa in**. Power BI Desktop filen datauppsättningar ansluts till SQL Azure databastabeller.
* **Stäng** Power BI Desktop-fil.

![Stäng Power BI desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* Klicka på **spara** för att spara ändringarna. 

Du har nu konfigurerat alla rapporter som motsvarar sökvägen för batch-bearbetning i lösningen. 

## <a name="upload-to-powerbicom"></a>Överför till *powerbi.com*
1. Gå till Power BI-webbportalen på http://powerbi.com och logga in.
2. Klicka på **hämta Data**  
3. Ladda upp filen Power BI Desktop.  
4. Om du vill ladda upp, klickar du på **hämta Data -> hämta filer -> lokal fil**  
5. Navigera till den **”**ConnectedCarsPbiReport.pbix**”**  
6. När filen har överförts dirigeras till arbetsplatsen Power BI.  

En datamängd, rapporten och en tom instrumentpanel skapas för dig.  

Diagram för PIN-kod till en ny instrumentpanel kallas **Vehicle telemetri instrumentpanelen** i **Power BI**. Klicka på den tomma instrumentpanelen skapade ovan och gå sedan till den **rapporter** avsnitt på rapporten nyligen uppladdade.  

![Vehicle telemetri Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**Observera att rapporten har sex sidor:**  
Sidan 1: Vehicle densitet  
Sidan 2: Realtid vehicle hälsa  
Sidan 3: Aggressivt drivs fordon   
Sidan 4: Återkallas fordon  
Sidan 5: Bränsle effektivt drivs fordon  
Sidan 6: Contoso-logotyp  

![Anslutna bilar Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**Från sidan 3**, fästa följande:  

1. Antal VIN  
   ![Anslutna bilar Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. Aggressivt drivs fordon av modellen – vattenfallet diagram  
   ![Vehicle telemetri - PIN-kod diagram 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Från sidan 5**, fästa följande: 

1. Antal vin    
   ![Vehicle telemetri - PIN-kod diagram 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. Bränsle effektivt fordon av modell: grupperat stående stapeldiagram  
   ![Vehicle telemetri - PIN-kod diagram 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Från sidan 4**, fästa följande:  

1. Antal vin  
   ![Vehicle telemetri - PIN-kod diagram 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. Återkallade fordon efter ort, modell: Treemap  
   ![Vehicle telemetri - PIN-kod diagram 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**Från sidan 6**, fästa följande:  

1. Contoso motorer-logotyp  
   ![Vehicle telemetri - PIN-kod diagram 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Ordna instrumentpanelen**  

1. Gå till instrumentpanelen
2. Hovra över varje diagram och Byt namn på den baserad på namngivningen i fullständig instrumentpanelen bilden nedan. Även flytta diagrammen ska se ut som nedan instrumentpanelen.  
   ![Vehicle telemetri - ordna instrumentpanelen 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Vehicle telemetri Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Om du har skapat alla rapporter som nämns i detta dokument, sista slutförda instrumentpanelen bör se ut som följande bild. 

![Vehicle telemetri - ordna instrumentpanelen 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Grattis! Du har skapat rapporterna och instrumentpanelen för att få realtid, förutsägbara och batch-insikter om vehicle hälsa och köra vanor.  
