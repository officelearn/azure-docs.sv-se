---
title: Testa Azure Stream Analytics-frågor lokalt med Visual Studio
description: Den här artikeln beskriver hur du testar lokalt med Azure Stream Analytics Tools för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: db26dd12d7c44f6079ee38364a4e9e240d2e7bc8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717020"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testa Stream Analytics-frågor lokalt med Visual Studio

Du kan använda Azure Stream Analytics-verktyg för Visual Studio för att testa din Stream Analytics-jobb lokalt med exempeldata.

Använd det här [snabbstarten](stream-analytics-quick-create-vs.md) och lär dig att skapa ett Stream Analytics-jobb med hjälp av Visual Studio.

## <a name="test-your-query"></a>Testa frågan

I projektet Azure Stream Analytics, dubbelklickar du på **Script.asaql** att öppna skriptet i redigeraren. Du kan sammanställa fråga för att se om det finns några syntaxfel. Frågeredigeraren stöder IntelliSense och syntaxfärgning en fel-markör.

![Frågeredigeraren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Lägg till lokal indata

För att verifiera din fråga mot lokala statiska data, högerklicka på indata och väljer **Lägg till lokal indata**.
   
![Lägg till lokal indata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
I popup-fönstret väljer du exempeldata från din lokala sökvägen och **spara**.
   
![Lägg till lokal indata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
En fil med namnet **local_EntryStream.json** läggs automatiskt till mappen indata.
   
![Lokala Indatamappen fillista](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Välj **kör lokalt** i frågeredigeraren. Eller du kan trycka på F5.
   
![Kör lokalt](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Utdata kan visas i tabellformat direkt från Visual Studio.

![Utdata i tabellformat](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Du kan hitta sökvägen för utdata från konsolens utdata. Tryck på valfri tangent för att öppna mappen resultatet.
   
![Lokal körning](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Kontrollera resultaten i den lokala mappen.
   
![Lokal mapp resultat](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Exempelindata
Du kan också samla in exempel på inmatade data från din inmatningskällor till en lokal fil. Högerklicka på den inkommande configuration-filen och välj **exempeldata**. 

![Exempeldata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Du kan bara exempeldata strömmas från Event Hubs eller IoT-hubbar. Andra indatakällor stöds inte. I popup-dialogrutan fyller du i den lokala sökvägen till spara exempeldata och välj **exempel**.

![Exempelkonfiguration för data](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Du kan se förloppet i den **utdata** fönster. 

![Exempel på utdata från data](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Nästa steg

* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Självstudie: Distribuera Azure Stream Analytics-jobb med CI/CD med VSTS](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)