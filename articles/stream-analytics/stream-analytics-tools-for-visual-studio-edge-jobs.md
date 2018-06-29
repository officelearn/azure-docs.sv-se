---
title: Edge-jobb i Azure Stream Analytics tools för Visual Studio
description: Den här artikeln beskriver hur du skapar, felsöka och skapa Stream Analytics Edge jobb med hjälp av Stream Analytics-verktyg för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/13/2018
ms.openlocfilehash: cfdfa1493857a1731da74c52a616293b482f811a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030700"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Utveckla kant för Stream Analytics-jobb med hjälp av Visual Studio tools

Lär dig hur du använder Stream Analytics tools för Visual Studio för att redigera, felsöka och skapa Stream Analytics Edge-jobb i den här självstudiekursen. När du skapar och testar jobbet går du till Azure portal för att distribuera till dina enheter. 

## <a name="prerequisites"></a>Förutsättningar

Följande förutsättningar för att kunna slutföra den här kursen behöver du:

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), eller [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Enterprise (Ultimate/Premium), Professional och Community-versioner stöds. Express edition stöds inte.  

* Följ den [Installationsinstruktioner](stream-analytics-tools-for-visual-studio-edge-jobs.md) installera Stream Analytics-verktyg för Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Skapa ett Stream Analytics Edge-projekt 

I Visual Studio väljer **filen** > **ny** > **projekt**. Navigera till den **mallar** listan till vänster > Expandera **Azure Stream Analytics** > **Stream Analytics Edge**  >   **Azure Stream Analytics kant programmet**. Ange ett namn, plats och lösningen namn för projektet och välj **OK**.

![Nytt Edge-projekt](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

När projektet skapas, går du till den **Solution Explorer** att visa mapphierarkin.

![Solution explorer-vy](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Välj rätt prenumeration

1. Från din Visual Studio **visa** väljer du **Server Explorer**.  

2. Högerklicka på **Azure** > Välj **Anslut till Microsoft Azure-prenumeration** > och logga sedan in med ditt Azure-konto.

## <a name="define-inputs"></a>Definiera indata

1. Från den **Solution Explorer**, expandera den **indata** nod bör du se indata med namnet **EdgeInput.json**. Dubbelklicka om du vill visa dess inställningar.  

2. Kontrollera att källtypen har angetts till **dataströmmen** > anges till **kant hubb** > händelse serialiseringsformat har angetts till **Json** > och kodning är inställd på **UTF8**. Alternativt kan du byta namn på **indata Alias**, lämna den som är i det här exemplet ska vi. Om du byter namn på det inmatade aliaset använder du det namn du angav när du definierar i frågan. Spara inställningarna genom att klicka på **Spara**.  
   ![Inkommande konfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiera utdata

1. Från den **Solution Explorer**, expandera den **utdata** nod som du bör se utdata med namnet **EdgeOutput.json**. Dubbelklicka om du vill visa dess inställningar.  

2. Se till att mottagare har angetts för att välja **kant hubb** > händelse serialiseringsformat har angetts till **Json** > och kodning är inställd på **UTF8** > och Format är inställd på  **Matrisen**. Alternativt kan du byta namn på **kolumnalias**, lämna den som är i det här exemplet ska vi. Om du byter namn på utdataalias, använder du det namn du angav när du definierar i frågan. Spara inställningarna genom att klicka på **Spara**. 
   ![Konfiguration av utdata](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Stream Analytics-jobb som distribuerats i Edge-miljöer stöder de flesta av [Stream Analytics-frågespråket referens](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), men följande åtgärder inte stöds ännu för Edge jobb: 


|**Kategori**  | **Kommando**  |
|---------|---------|
|Geospatiala operatorer |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Andra operatorer | <ul><li>PARTITIONEN AV</li><li>TIDSSTÄMPEL AV ÖVER</li><li>DISTINKTA</li><li>Uttrycksparametern i operatorn antal</li><li>Mikrosekundnivå i datum- och tidsfunktioner</li><li>JavaScript UDA (den här funktionen är fortfarande under förhandsgranskning för jobb som har distribuerats i molnet)</li></ul>   |

När du skapar ett jobb för en kant i portalen varnar kompilatorn automatiskt dig om du inte använder en operator som stöds.

Från din Visual Studio definiera följande omvandling av frågan i frågeredigeraren (**script.asaql filen**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>TESTJOBBET lokalt

Om du vill testa frågan lokalt kan du ladda upp exempeldata. Du kan hämta exempeldata genom att hämta registreringsdata från den [GitHub-lagringsplatsen](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) och spara den på den lokala datorn. 

1. Att ladda upp exempeldata, > högerklickar du på **EdgeInput.json** fil > Välj **lägga till lokala indata**  

2. I popup-fönstret > **Bläddra** exempeldata från din lokala sökväg > Välj **spara**.
   ![Lokal inkommande konfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. En fil med namnet **local_EdgeInput.json** läggs automatiskt till mappen indata.  
4. Du kan köra det lokalt, eller så kan du skicka den till Azure. Att testa frågan > Välj **kör lokalt**.  
   ![Kör alternativ](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. Kommandotolkens fönster visar status för jobbet. När jobbet har körts, skapas en mapp som ser ut som ”2018-02-23-11-31-42” i ditt projekt mappsökväg ”Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42”. Navigera till mappsökvägen för att visa resultaten i den lokala mappen:

   Du kan också logga in på Azure-portalen och kontrollera att jobbet har skapats. 

   ![Resultatet mapp](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Skicka jobbet till Azure

1. Innan du skickar jobbet till Azure måste du ansluta till din Azure-prenumeration. Öppna **Server Explorer** > högerklickar du på **Azure** > **Anslut till Microsoft Azure-prenumeration** > Logga in på Azure-prenumerationen.  

2. För att skicka jobb till Azure, gå till frågeredigeraren > Välj **skicka till Azure**.  

3. Ett popup-fönster öppnas där du kan välja att uppdatera ett befintligt jobb kant eller skapa en ny. När du uppdaterar ett befintligt jobb ersätts alla jobb konfigurationen i det här scenariot, kommer du att publicera ett nytt jobb. Välj **skapa en ny Azure Stream Analytics-jobbet** > Ange ett namn för ditt jobb liknande **MyASAEdgeJob** > Välj de nödvändiga **prenumeration**, **Resursgruppen**, och **plats** > Välj **skicka**.

   ![Skicka den till Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Nu kant för Stream Analytics-jobbet har skapats kan du referera till den [köra jobb på IoT kant kursen](stream-analytics-edge.md) information om hur du distribuerar den till dina enheter. 

## <a name="manage-the-job"></a>Hantera jobbet 

Du kan visa status för jobbet och jobbet diagrammet från Server Explorer. Från den **Server Explorer** > **Stream Analytics** > Expandera prenumerationen och resursgruppen där du har distribuerat Edge jobbet > du kan visa MyASAEdgejob med statusen  **Skapa**. Expandera jobbnoden och dubbelklicka på den för att öppna vyn jobb.

![Alternativ för Server explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Visa fönstret jobb ger åtgärder, t.ex uppdatera jobb, tar bort jobb, öppna jobbet från Azure portal osv.

![Jobbet diagram och andra alternativ](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure Iot kant](../iot-edge/about-iot-edge.md)
* [ASA på IoT kant självstudiekursen](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Skicka feedback till teamet med den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
