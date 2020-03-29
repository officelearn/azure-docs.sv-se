---
title: Azure Stream Analytics Edge-jobb i Visual Studio
description: I den här artikeln beskrivs hur du skapar, felsöker och skapar dina Stream Analytics-jobb på IoT Edge-jobb med hjälp av Stream Analytics-verktygen för Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354556"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Utveckla Stream Analytics Edge-jobb med Visual Studio-verktyg

I den här självstudien får du lära dig hur du använder Stream Analytics-verktyg för Visual Studio. Du lär dig hur du skapar, felsöker och skapar dina Stream Analytics Edge-jobb. När du har skapat och testat jobbet kan du gå till Azure-portalen för att distribuera det till dina enheter. 

## <a name="prerequisites"></a>Krav

Du behöver följande förutsättningar för att slutföra den här självstudien:

* Installera [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)eller [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Express-utgåvan stöds inte.  

* Installera Stream Analytics-verktyg för Visual Studio genom att följa [installationsanvisningarna](stream-analytics-tools-for-visual-studio-edge-jobs.md) för Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Skapa ett Stream Analytics Edge-projekt 

Välj Arkiv**nytt** > **projekt** **i** > Visual Studio . Navigera till **listan Mallar** till vänster > expandera **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge Application**. Ange ett namn, en plats och ett lösningsnamn för projektet och välj **OK**.

![Nytt Stream Analytics Edge-projekt i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

När projektet har skapats navigerar du till **Lösningsutforskaren** för att visa mapphierarkin.

![Vyn Lösningsutforskare för Stream Analytics Edge-jobb](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Välj rätt prenumeration

1. Välj **Server Explorer**på Visual Studio **View-menyn** .  

2. Högerklicka på **Azure** > Välj **Anslut till Microsoft Azure-prenumeration** > och logga sedan in med ditt Azure-konto.

## <a name="define-inputs"></a>Definiera indata

1. Expandera noden Ingångar i **Lösningsutforskaren**som du bör se en indata med namnet **EdgeInput.json**. **Inputs** Dubbelklicka för att visa dess inställningar.  

2. Ange källtyp till **dataström**. Ange sedan Source till **Edge Hub**, Event Serialization Format till **Json**och Kodning till **UTF8**. Du kan också byta namn på **indataaliaset**, låt oss lämna det som det är för det här exemplet. Om du byter namn på indataaliaset använder du det namn som du angav när du definierade frågan. Spara inställningarna genom att klicka på **Spara**.  
   ![Stream Analytics jobbinmatningskonfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiera utmatningar

1. Expandera noden Utdata i **Lösningsutforskaren**som du bör se en utdata med namnet **EdgeOutput.json**. **Outputs** Dubbelklicka för att visa dess inställningar.  

2. Se till att ange Sink för att välja **Edge Hub**, ange Event Serialization Format till **Json**, ange kodning till **UTF8**och ange Format **Array**. Du kan också byta namn på **utdataaliaset**, låt oss lämna det som det är för det här exemplet. Om du byter namn på utdataaliaset använder du det namn som du angav när du definierade frågan. Spara inställningarna genom att klicka på **Spara**. 
   ![Konfiguration av utdata för Stream Analytics-jobb](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Stream Analytics-jobb som distribueras i IoT Edge-miljöer i Stream Analytics stöder de flesta av referensen för [Query Language i Stream Analytics.](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396) Följande åtgärder stöds dock ännu inte för Stream Analytics Edge-jobb: 


|**Kategori**  | **Kommandot**  |
|---------|---------|
|Andra operatörer | <ul><li>PARTITION AV</li><li>TIDSSTÄMPEL AV ÖVER</li><li>JavaScript UDF</li><li>Användardefinierade aggregat (UDA)</li><li>GetMetadataPropertyValue</li><li>Använda mer än 14 aggregat i ett enda steg</li></ul>   |

När du skapar ett Stream Analytics Edge-jobb i portalen varnar kompilatorn dig automatiskt om du inte använder en operator som stöds.

I Visual Studio definierar du följande omvandlingsfråga i frågeredigeraren (**script.asaql-fil**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testa jobbet lokalt

Om du vill testa frågan lokalt bör du ladda upp exempeldata. Du kan hämta exempeldata genom att hämta registreringsdata från [GitHub-databasen](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) och spara dem på din lokala dator. 

1. Om du vill ladda upp exempeldata högerklickar du på **filen EdgeInput.json** och väljer **Lägg till lokal inmatning**  

2. I popup-fönstret > Bläddra **bland** exempeldata från din lokala sökväg > Välj **Spara**.
   ![Lokal indatakonfiguration i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. En fil med namnet **local_EdgeInput.json** läggs till automatiskt i mappen ingångar.  
4. Du kan antingen köra den lokalt eller skicka till Azure. Om du vill testa frågan väljer du **Kör lokalt**.  
   ![Alternativ för Stream Analytics-jobbkörning i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Kommandotolksfönstret visar jobbets status. När jobbet körs Den skapar en mapp som ser ut som "2018-02-23-11-31-42" i projektmappsökvägen "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Navigera till mappsökvägen för att visa resultaten i den lokala mappen:

   Du kan också logga in på Azure-portalen och verifiera att jobbet har skapats. 

   ![Flödesanalysjobbresultatmapp](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Skicka jobbet till Azure

1. Innan du skickar jobbet till Azure måste du ansluta till din Azure-prenumeration. Öppna **Server Explorer** > högerklicka på **Azure** > **Connect till Microsoft Azure-prenumeration** > logga in på din Azure-prenumeration.  

2. Om du vill skicka jobbet till Azure navigerar du till frågeredigeraren > väljer **Skicka till Azure**.  

3. Ett popup-fönster öppnas. Välj att uppdatera ett befintligt Stream Analytics Edge-jobb eller skapa ett nytt. När du uppdaterar ett befintligt jobb ersätter det all jobbkonfiguration, i det här scenariot publicerar du ett nytt jobb. Välj **Skapa ett nytt Azure Stream Analytics-jobb** > ange ett namn för jobbet, till exempel **MyASAEdgeJob** > välja önskad **prenumeration,** **resursgrupp**och **plats** > Välj **Skicka**.

   ![Skicka Stream Analytics-jobb till Azure från Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Nu har ditt Stream Analytics Edge-jobb skapats. Du kan referera till [körjobben på IoT Edge-självstudien](stream-analytics-edge.md) för att lära dig hur du distribuerar den till dina enheter. 

## <a name="manage-the-job"></a>Hantera jobbet 

Du kan visa status för jobbet och jobbdiagrammet från Serverutforskaren. Expandera prenumerationen och resursgruppen där du distribuerade Stream Analytics Edge-jobbet från **Stream Analytics** i **Server Explorer.** Du kan visa MyASAEdgejob med status **Skapad**. Expandera jobbnoden och dubbelklicka på den för att öppna jobbvyn.

![Jobbhanteringsalternativ för serverutforskare](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
I jobbvyfönstret får du åtgärder som att uppdatera jobbet, ta bort jobbet och öppna jobbet från Azure-portalen.

![Jobbdiagram och andra alternativ i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA på IoT Edge handledning](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Skicka feedback till teamet med hjälp av den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
