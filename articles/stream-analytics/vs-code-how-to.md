---
title: Utforska Azure Stream Analytics med Visual Studio Code (förhandsversion)
description: Den här artikeln visar hur du exporterar Azure Stream Analytics-jobb till ett lokalt projekt lista jobb och visa jobb entiteter, och konfigurera en CI/CD-pipeline för ditt Stream Analytics-jobb.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079217"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Utforska Azure Stream Analytics med Visual Studio Code (förhandsversion)

Azure Stream Analytics för Visual Studio Code-tillägget ger utvecklare en förenklad upplevelse för att hantera sina Stream Analytics-jobb. Med Azure Stream Analytics-tillägget kan du:

- [Skapa](quick-create-vs-code.md), starta och stoppa jobb
- Exportera befintliga jobb till ett lokalt projekt
- Köra frågor lokalt
- Konfigurera en CI/CD-pipeline

## <a name="export-a-job-to-a-local-project"></a>Exportera ett jobb till ett lokalt projekt

Exportera ett jobb till ett lokalt projekt genom att hitta jobbet som du vill exportera i den **Stream Analytics Explorer** i Visual Studio code. Välj sedan en mapp för ditt projekt. Projektet exporteras till den mapp som du väljer, och du kan fortsätta att hantera jobbet från Visual Studio Code. Mer information om hur du använder Visual Studio Code för att hantera Stream Analytics-jobb finns i Visual Studio Code [snabbstarten](quick-create-vs-code.md).

![Exportera ASA-jobb i Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Köra frågor lokalt

Du kan använda Azure Stream Analytics-tillägget för Visual Studio Code för att testa din Stream Analytics-jobb lokalt med exempeldata.

1. När du har skapat ditt Stream Analytics-jobb kan du använda **Ctrl + Skift + P** att öppna utbud för kommandot. Skriv sedan och välj **ASA: Lägg till indata**.

    ![Lägg till ASA indata i Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Välj **lokala indata**.

    ![Lägg till ASA lokala indata i Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Välj **+ nya lokala indata**.

    ![Lägg till ett nytt ASA som lokala indata i Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Ange samma inmatat alias som du använde i din fråga.

    ![Lägg till en ny lokal inmatat alias för ASA](./media/vs-code-how-to/new-local-input-alias.png)

5. I den **LocalInput_DefaultLocalStream.json** fil, ange sökvägen till filen där dina lokala data-filen finns.

    ![Ange lokal filsökväg i Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Gå tillbaka till din frågeredigeraren och välj **kör lokalt**.

    ![Välj Kör lokalt i frågeredigeraren](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Konfigurera en CI/CD-pipeline

Du kan aktivera kontinuerlig integrering och distribution för Azure Stream Analytics-jobb med hjälp av den **asa-cicd verktyg** NPM-paket. NPM-paketet innehåller verktyg för automatisk distribution av Stream Analytics Visual Studio Code-projekt. Det kan användas i Windows, macOS och Linux utan att installera Visual Studio Code.

När du har [hämtade paketet](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), använder du följande kommando för att mata ut Azure Resource Manager-mallar. Om den **outputPath** inte anges mallarna kommer att placeras i den **distribuera** mapp under projektets **bin** mapp.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Nästa steg

* [Skapa cloud Azure Stream Analytics-jobb i Visual Studio Code (förhandsversion)](quick-create-vs-code.md)