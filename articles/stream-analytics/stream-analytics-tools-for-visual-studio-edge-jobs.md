---
title: Edge-jobb i Azure Stream Analytics-verktyg för Visual Studio
description: Den här artikeln beskriver hur du skapar, felsöka och skapa ditt Stream Analytics Edge-jobb med hjälp av Stream Analytics-verktyg för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 684690baa63f5ccd65c69e3a1b7e310c2f809e59
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735566"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Utveckla Stream Analytics Edge-jobb med hjälp av Visual Studio-verktyg

I den här självstudien får du lära dig hur du använder Stream Analytics-verktyg för Visual Studio för att skapa, felsöka och skapa Stream Analytics Edge-jobb. När du skapar och testar jobbet går du till Azure portal för att distribuera den till dina enheter. 

## <a name="prerequisites"></a>Förutsättningar

Följande förutsättningar för att kunna slutföra den här kursen behöver du:

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), eller [Visual Studio 2013 uppdatering 4](https://www.microsoft.com/download/details.aspx?id=45326). Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Express-versionen stöds inte.  

* Följ den [Installationsinstruktioner](stream-analytics-tools-for-visual-studio-edge-jobs.md) installera Stream Analytics-verktyg för Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Skapa ett Stream Analytics Edge-projekt 

Från Visual Studio, Välj **filen** > **New** > **projekt**. Navigera till den **mallar** listan till vänster > Expandera **Azure Stream Analytics** > **Stream Analytics Edge**  >   **Azure Stream Analytics Edge-programmet**. Ange ett namn, plats och lösningen namn för projektet och välj **OK**.

![Nytt Edge-projekt i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

När projektet skapades, går du till den **Solution Explorer** att visa mapphierarkin.

![Solution explorer-vy av Stream Analytics Edge-jobb](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Välj rätt prenumeration

1. Från din Visual Studio **visa** menyn och välj **Server Explorer**.  

2. Högerklicka på **Azure** > Välj **Anslut till Microsoft Azure-prenumeration** > och logga sedan in med ditt Azure-konto.

## <a name="define-inputs"></a>Definiera indata

1. Från den **Solution Explorer**, expandera den **indata** nod bör du se indata med namnet **EdgeInput.json**. Dubbelklicka för att visa dess inställningar.  

2. Ange typ av datakälla **Data Stream**. Ange källa **Edge Hub**, Händelseserialiseringsformat till **Json**, och kodning till **UTF8**. Du kan också byta namn på **indata Alias**, vi lämnar det som det är det här exemplet. Använd det namn du angav när du definierar frågan om du byter namn på inmatat alias. Spara inställningarna genom att klicka på **Spara**.  
   ![Inkommande konfiguration för Stream Analytics-jobb](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiera utdata

1. Från den **Solution Explorer**, expandera den **utdata** nod som du bör se utdata med namnet **EdgeOutput.json**. Dubbelklicka för att visa dess inställningar.  

2. Kontrollera att mottagaren har angetts till Välj **Edge Hub** > Händelseserialiseringsformat inställd **Json** > och kodning är inställt på **UTF8** > och formatet är inställt på  **Matris**. Du kan också byta namn på **Utdataaliaset**, vi lämnar det som det är det här exemplet. Använd det namn du angav när du definierar frågan om du byter namn på utdataaliaset. Spara inställningarna genom att klicka på **Spara**. 
   ![Stream Analytics-jobbet utdata-konfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Stream Analytics-jobb som används i Edge-system stöder de flesta av [frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), men följande åtgärder inte stöds ännu för Edge-jobb: 


|**Kategori**  | **Kommando**  |
|---------|---------|
|Geospatiala operatorer |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Andra operatorer | <ul><li>PARTITION BY</li><li>TIDSSTÄMPEL AV OVER</li><li>DISTINKTA</li><li>Uttrycksparametern i operatorn antal</li><li>Databehandlingsnoder i datum- och tidsfunktioner</li><li>JavaScript UDA (den här funktionen är fortfarande i förhandsversion för jobb som distribuerats i molnet)</li></ul>   |

När du skapar ett Edge-jobb i portalen varnar kompilatorn automatiskt dig om du inte använder en operator som stöds.

Från din Visual Studio, kan du definiera följande transformationsfrågan i frågeredigeraren (**script.asaql filen**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>TESTJOBBET lokalt

Om du vill testa frågan lokalt, bör du ladda upp exempeldata. Du kan hämta exempeldata genom att ladda ned registreringsdata från den [GitHub-lagringsplatsen](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) och spara den till din lokala dator. 

1. Om du vill ladda upp exempeldata, högerklicka på **EdgeInput.json** filen och välj **Lägg till lokal indata**  

2. I popup-fönstret > **Bläddra** exempeldata från din lokala sökväg > Välj **spara**.
   ![Lokala inkommande konfigurationen i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. En fil med namnet **local_EdgeInput.json** läggs automatiskt till mappen indata.  
4. Du kan köra det lokalt, eller så kan du skicka till Azure. Att testa frågan > Välj **kör lokalt**.  
   ![Stream Analytics-jobb som kör alternativen i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Kommandotolkens fönster visar status för jobbet. När jobbet har körts, skapar en mapp som ser ut som ”2018-02-23-11-31-42” i ditt projekt mappsökväg ”Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42”. Navigera till sökvägen till mappen för att se resultaten i den lokala mappen:

   Du kan också logga in på Azure-portalen och kontrollera att jobbet har skapats. 

   ![Stream Analytics-jobbet resultatet mapp](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Skicka jobb till Azure

1. Innan du skickar jobbet till Azure måste du ansluta till din Azure-prenumeration. Öppna **Server Explorer** > Högerklicka på **Azure** > **Anslut till Microsoft Azure-prenumeration** > Logga in på Azure-prenumerationen.  

2. För att skicka jobbet till Azure, gå till frågeredigeraren > Välj **skicka till Azure**.  

3. Ett popup-fönster öppnas där du kan välja att uppdatera ett befintligt Edge-jobb eller skapa en ny. När du uppdaterar ett befintligt jobb, ersätts alla jobbkonfigurationen i det här scenariot, kommer du att publicera ett nytt jobb. Välj **skapa ett nytt Azure Stream Analytics-jobb** > Ange ett namn för ditt jobb liknande **MyASAEdgeJob** > Välj de nödvändiga **prenumeration**, **Resursgrupp**, och **plats** > Välj **skicka**.

   ![Skicka Stream Analytics-jobb till Azure från Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Nu har skapat ditt Stream Analytics Edge-jobb kan du referera till den [köra jobb på IoT Edge-självstudie](stream-analytics-edge.md) att lära dig hur du distribuerar den till dina enheter. 

## <a name="manage-the-job"></a>Hantera jobbet 

Du kan visa status för jobbet och jobbdiagrammet från Server Explorer. Från **Stream Analytics** i **Server Explorer**, expandera prenumerationen och resursgrupp där du har distribuerat Edge-jobb. Du kan visa MyASAEdgejob med status **Skapad**. Expandera jobbnoden och dubbelklicka på det för att öppna jobbvyn.

![Hantering av alternativ för Server explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Visa fönstret jobb ger dig åtgärder som uppdaterar jobbet, tar bort jobbet och öppna jobbet från Azure-portalen.

![Jobbdiagram och andra alternativ i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA på IoT Edge-självstudie](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Skicka feedback till teamet med den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
