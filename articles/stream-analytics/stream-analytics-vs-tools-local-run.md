---
title: Testa Azure Stream Analytics-frågor lokalt i Visual Studio
description: I den här artikeln beskrivs hur du testar frågor lokalt med Azure Stream Analytics Tools för Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834917"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testa Stream Analytics-frågor lokalt med Visual Studio

Du kan använda Azure Stream Analytics-verktyg för Visual Studio för att testa dina Stream Analytics-jobb lokalt med exempeldata eller [livedata](stream-analytics-live-data-local-testing.md). 

Använd den här [snabbstarten](stream-analytics-quick-create-vs.md) om du vill lära dig hur du skapar ett Stream Analytics-jobb med Visual Studio.

## <a name="test-your-query"></a>Testa frågan

I ditt Azure Stream Analytics-projekt dubbelklickar du på **Script.asaql** för att öppna skriptet i redigeraren. Du kan kompilera frågan för att se om det finns några syntaxfel. Frågeredigeraren stöder IntelliSense, syntaxfärgning och en felmarkör.

![Frågeredigeraren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Lägga till lokal inmatning

Om du vill validera frågan mot lokala statiska data högerklickar du på indata och väljer **Lägg till lokal indata**.
   
![Lägga till lokal inmatning](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
I popup-fönstret väljer du exempeldata från den lokala sökvägen och **Spara**.
   
![Lägga till lokal inmatning](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
En fil med namnet **local_EntryStream.json** läggs till automatiskt i mappen ingångar.
   
![Fillista för lokal inmatningsmapp](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Välj **Kör lokalt** i frågeredigeraren. Eller så kan du trycka på F5.
   
![Kör lokalt](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Utdata kan visas i ett tabellformat direkt från Visual Studio.

![Utdata i tabellformat](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Du hittar utdatasökvägen från konsolutgången. Tryck på valfri tangent för att öppna resultatmappen.
   
![Lokal körning](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Kontrollera resultaten i den lokala mappen.
   
![Resultat för lokal mapp](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Exempelinmatning
Du kan också samla in exempeldata från indatakällorna till en lokal fil. Högerklicka på indatakonfigurationsfilen och välj **Exempeldata**. 

![Exempeldata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Du kan bara ta prov på dataströmning från eventhubbar eller IoT-hubbar. Andra indatakällor stöds inte. I popup-dialogrutan fyller du i den lokala sökvägen för att spara exempeldata och väljer **Exempel**.

![Exempel på datakonfiguration](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Du kan se förloppet i **utdatafönstret.** 

![Exempel på datautdata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Testa live-data lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)](stream-analytics-live-data-local-testing.md)
* [Självstudiekurs: Distribuera ett Azure Stream Analytics-jobb med CI/CD med Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)
