---
title: Testa Azure Stream Analytics-frågor lokalt med Visual Studio Code (förhandsversion)
description: Den här artikeln beskriver hur du testar lokalt med Azure Stream Analytics Tools för Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827957"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testa Stream Analytics-frågor lokalt med Visual Studio Code

Du kan använda Azure Stream Analytics-verktyg för Visual Studio Code för att testa din Stream Analytics-jobb lokalt med exempeldata.

Använd det här [snabbstarten](quick-create-vs-code.md) och lär dig att skapa ett Stream Analytics-jobb med hjälp av Visual Studio Code.

## <a name="run-queries-locally"></a>Köra frågor lokalt

Du kan använda Azure Stream Analytics-tillägget för Visual Studio Code för att testa din Stream Analytics-jobb lokalt med exempeldata.

1. När du har skapat ditt Stream Analytics-jobb kan du använda **Ctrl + Skift + P** att öppna kommandopaletten. Skriv sedan och välj **ASA: Lägg till indata**.

    ![Lägg till ASA indata i Visual Studio code](./media/vscode-local-run/add-input.png)

2. Välj **lokala indata**.

    ![Lägg till ASA lokala indata i Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Välj **+ nya lokala indata**.

    ![Lägg till ett nytt ASA som lokala indata i Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Ange samma inmatat alias som du använde i din fråga.

    ![Lägg till en ny lokal inmatat alias för ASA](./media/vscode-local-run/new-local-input-alias.png)

5. I den **LocalInput_DefaultLocalStream.json** fil, ange sökvägen till filen där dina lokala data-filen finns.

    ![Ange lokal filsökväg i Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Gå tillbaka till din frågeredigeraren och välj **kör lokalt**.

    ![Välj Kör lokalt i frågeredigeraren](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa cloud Azure Stream Analytics-jobb i Visual Studio Code (förhandsversion)](quick-create-vs-code.md)

* [Utforska Azure Stream Analytics-jobb med Visual Studio Code (förhandsversion)](vscode-explore-jobs.md)
