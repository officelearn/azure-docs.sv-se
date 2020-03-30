---
title: Testa Azure Stream Analytics-frågor lokalt mot livestreamindata med hjälp av Visual Studio-kod
description: I den här artikeln beskrivs hur du testar frågor lokalt mot livestreamindata med hjälp av Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660859"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testa Stream Analytics-frågor lokalt mot livestreamindata med hjälp av Visual Studio-kod

Du kan använda Azure Stream Analytics Tools för Visual Studio-kod för att testa dina Stream Analytics-jobb lokalt mot livestreamindata. Indata kan komma från en källa som Azure Event Hubs eller Azure IoT Hub. Utdataresultaten skickas som JSON-filer till en mapp i projektet **LocalRunOutputs**.

## <a name="prerequisites"></a>Krav

* Installera [.NET Core SDK](https://dotnet.microsoft.com/download) och starta om Visual Studio-koden.

* Använd [den här snabbstarten](quick-create-vs-code.md) om du vill lära dig hur du skapar ett Stream Analytics-jobb med hjälp av Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definiera en live stream-inmatning

1. Högerklicka på mappen **Ingångar** i Stream Analytics-projektet. Välj sedan **ASA: Lägg till indata** på snabbmenyn.

   ![Lägga till indata från mappen Ingångar](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Du kan också välja **Ctrl+Skift+P** för att öppna kommandopaletten och ange **ASA: Lägg till inmatning**.

   ![Lägga till Stream Analytics-indata i Visual Studio-kod](./media/quick-create-vs-code/add-input.png)

2. Välj en indatakällatyp i listrutan.

   ![Välj IoT-hubb som indataalternativ](./media/quick-create-vs-code/iot-hub.png)

3. Om du har lagt till indata från kommandopaletten väljer du det Stream Analytics-frågeskript som ska använda indata. Det bör fyllas i automatiskt med filsökvägen till **myASAproj.asaql**.

   ![Välja ett Stream Analytics-skript i Visual Studio-kod](./media/quick-create-vs-code/asa-script.png)

4. Välj **Välj bland dina Azure-prenumerationer** på den nedrullningsbara menyn.

    ![Välj bland prenumerationer](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Konfigurera den nyligen genererade JSON-filen. Du kan använda funktionen CodeLens för att ange en sträng, välja från en listruta eller ändra texten direkt i filen. Följande skärmbild visar **Välj bland dina prenumerationer** som ett exempel.

   ![Konfigurera indata i Visual Studio-kod](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Förhandsgranska indata

Om du vill vara säker på att indata kommer väljer du **Förhandsgranska data** i konfigurationsfilen för liveindata från den översta raden. Vissa indata kommer från en IoT-hubb och visas i förhandsgranskningsfönstret. Förhandsgranskningen kan ta några sekunder att visas.

 ![Förhandsgranska liveinmatning](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Kör frågor lokalt

Gå tillbaka till frågeredigeraren och välj **Kör lokalt**. Välj sedan **Använd direktinmatning** i listrutan.

![Välj "Kör lokalt" i frågeredigeraren](./media/vscode-local-run/run-locally.png)

![Välj "Använd liveinmatning"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Resultatet visas i det högra fönstret och uppdateras var tredje sekund. Du kan välja **Kör** för att testa igen. Du kan också välja **Öppna i mappen** om du vill visa resultatfilerna i Utforskaren och öppna dem med Visual Studio-kod eller ett verktyg som Excel. Observera att resultatfilerna endast är tillgängliga i JSON-format.

Standardtiden för jobbet att börja skapa utdata är inställd **på Nu**. Du kan anpassa tiden genom att välja knappen **Utdatastarttid** i resultatfönstret.

![Visa lokalt körningsresultat](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Nästa steg

* [Utforska Azure Stream Analytics-jobb med Visual Studio-kod (förhandsversion)](visual-studio-code-explore-jobs.md)

* [Konfigurera CI/CD-pipelines med hjälp av npm-paketet](setup-cicd-vs-code.md)
