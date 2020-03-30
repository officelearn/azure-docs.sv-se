---
title: Testa ett Azure Stream Analytics-jobb lokalt med exempeldata med Visual Studio-kod
description: I den här artikeln beskrivs hur du testar frågor lokalt med exempeldata med Hjälp av Azure Stream Analytics Tools för Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486474"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Testa Stream Analytics-frågor lokalt med exempeldata med Visual Studio-kod

Du kan använda Azure Stream Analytics-verktyg för Visual Studio-kod för att testa dina Stream Analytics-jobb lokalt med exempeldata. Du hittar frågeresultaten i JSON-filer i mappen **LocalRunOutputs** i projektet.

## <a name="prerequisites"></a>Krav

* Installera [.NET core SDK](https://dotnet.microsoft.com/download) och starta om Visual Studio Code.

* Använd den här [snabbstarten](quick-create-vs-code.md) om du vill lära dig hur du skapar ett Stream Analytics-jobb med Visual Studio-kod.

## <a name="prepare-sample-data"></a>Förbereda exempeldata

Du måste förbereda exempeldatafiler först. Om du redan har några exempeldatafiler på datorn kan du hoppa över det här steget och gå vidare till nästa.

1. Klicka på **Förhandsgranska data** i indatakonfigurationsfilen från den översta raden. Vissa indata hämtas från IoT Hub och visas i förhandsgranskningsfönstret. Observera att detta kan ta ett tag.

2. När data visas klickar du på **Spara som** för att spara data i en lokal fil.

 ![Förhandsgranska liveinmatning](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definiera en lokal indata

1. Klicka på **input.json** under mappen Ingångar i stream analytics-projektet. Välj sedan **Lägg till lokal indata** från den översta raden.

    ![Lägga till lokal inmatning från projektet](./media/quick-create-vs-code/add-input-from-project.png)

    Du kan också använda **Ctrl+Skift+P** för att öppna kommandopaletten och ange **ASA: Lägg till inmatning**.

   ![Lägga till Stream Analytics-indata i VS-kod](./media/quick-create-vs-code/add-input.png)

2. Välj **Lokal inmatning**.

    ![Lägga till ASA-lokal inmatning i Visual Studio-kod](./media/vscode-local-run/add-local-input.png)

3. Välj **+ Ny lokal inmatning**.

    ![Lägga till en ny LOKAL ASA-inmatning i Visual Studio-kod](./media/vscode-local-run/add-new-local-input.png)

4. Ange samma indataalias som du använde i frågan.

    ![Lägga till ett nytt ASA-lokalt inmatningsalias](./media/vscode-local-run/new-local-input-alias.png)

5. I den nyligen genererade **filen LocalInput_Input.json** anger du filsökvägen där den lokala datafilen finns.

    ![Ange lokal filsökväg i Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Välj **Förhandsgranska data** om du vill förhandsgranska indata. Serialiseringstypen för dina data identifieras automatiskt om dess JSON eller CSV. Använd väljaren för att visa dina data i **tabell-** eller **råformat.** Följande tabell är ett exempel på data i **tabellformat:**

     ![Förhandsgranska lokala data i tabellformat](./media/vscode-local-run/local-file-preview-table.png)

    Följande tabell är ett exempel på data i **raw-format:**

    ![Förhandsgranska lokala data i råformat](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Kör frågor lokalt

Gå tillbaka till frågeredigeraren och välj **Kör lokalt**. Välj sedan **Använd lokal indata i** listrutan.

![Välj kör lokalt i frågeredigeraren](./media/vscode-local-run/run-locally.png)

![Använd lokal inmatning](./media/vscode-local-run/run-locally-use-local-input.png)

Resultatet visas i det högra fönstret. Du kan klicka på **Kör** för att testa igen. Du kan också välja **Öppna i mappen** om du vill visa resultatfilerna i utforskaren och öppna dem ytterligare med andra verktyg. Observera att resultatfilerna endast är tillgängliga i JSON-format.

![Visa lokalt körningsresultat](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Nästa steg

* [Testa Azure Stream Analytics-jobb lokalt med liveindata med Visual Studio-kod](visual-studio-code-local-run-live-input.md)

* [Utforska Azure Stream Analytics-jobb med Visual Studio-kod (förhandsversion)](visual-studio-code-explore-jobs.md)
