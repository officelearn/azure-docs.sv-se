---
title: Testa Azure Stream Analytics frågor lokalt med Visual Studio Code (för hands version)
description: Den här artikeln beskriver hur du testar frågor lokalt med Azure Stream Analytics verktyg för Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 751cdf50fccc654dfab06b4d18428531312d08e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673031"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testa Stream Analytics frågor lokalt med Visual Studio Code

Du kan använda Azure Stream Analytics verktyg för Visual Studio Code för att testa dina Stream Analytics-jobb lokalt med exempel data.

Använd den här [snabb](quick-create-vs-code.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med Visual Studio Code.

## <a name="prerequisites"></a>Förutsättningar
* Installera [.net Core SDK](https://dotnet.microsoft.com/download).
* Starta om Visual Studio Code.
 
## <a name="run-queries-locally"></a>Kör frågor lokalt

Du kan använda Azure Stream Analytics tillägget för Visual Studio Code för att testa dina Stream Analytics-jobb lokalt med exempel data.

1. När du har skapat ditt Stream Analytics jobb använder du **Ctrl + Shift + P** för att öppna kommando paletten. Skriv och välj **ASA: Lägg till inmatade @ no__t-0.

    ![Lägg till ASA-ininformation i Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Välj **lokal indatamängd**.

    ![Lägg till en lokal indatatyp i ASA i Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Välj **+ ny lokal indatamängd**.

    ![Lägga till en ny information lokalt i Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Ange samma kolumnalias som du använde i frågan.

    ![Lägg till ett nytt lokalt indataports-alias](./media/vscode-local-run/new-local-input-alias.png)

5. I filen **LocalInput_DefaultLocalStream. JSON** anger du sökvägen till filen där den lokala data filen finns.

    ![Ange lokal fil Sök väg i Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Återgå till Frågeredigeraren och välj **Kör lokalt**.

    ![Välj Kör lokalt i Frågeredigeraren](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Stream Analytics moln jobb i Visual Studio Code (för hands version)](quick-create-vs-code.md)

* [Utforska Azure Stream Analytics-jobb med Visual Studio Code (för hands version)](vscode-explore-jobs.md)
