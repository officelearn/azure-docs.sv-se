---
title: Utforska Azure Stream Analytics-jobb med Visual Studio Code (förhandsversion)
description: Den här artikeln visar hur du exporterar Azure Stream Analytics-jobb till ett lokalt projekt, lista jobb och visa jobb entiteter.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827807"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Utforska Azure Stream Analytics med Visual Studio Code (förhandsversion)

Azure Stream Analytics för Visual Studio Code-tillägget ger utvecklare en förenklad upplevelse för att hantera sina Stream Analytics-jobb. Den kan användas i Windows, Mac och Linux. Med Azure Stream Analytics-tillägget kan du:

- [Skapa](quick-create-vs-code.md), starta och stoppa jobb
- Exportera befintliga jobb till ett lokalt projekt
- Lista jobb och visa jobb entiteter

## <a name="export-a-job-to-a-local-project"></a>Exportera ett jobb till ett lokalt projekt

Exportera ett jobb till ett lokalt projekt genom att hitta jobbet som du vill exportera i den **Stream Analytics Explorer** i Visual Studio Code. Välj sedan en mapp för ditt projekt. Projektet exporteras till den mapp som du väljer, och du kan fortsätta att hantera jobbet från Visual Studio Code. Mer information om hur du använder Visual Studio Code för att hantera Stream Analytics-jobb finns i Visual Studio Code [snabbstarten](quick-create-vs-code.md).

![Exportera ASA-jobb i Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Lista över jobb och visa jobb entiteter

Du kan använda jobbvyn för att interagera med Azure Stream Analytics-jobb från Visual Studio.


1. Klicka på den **Azure** -ikonen i Visual Studio Code Aktivitetsfältet och expandera sedan **noden Stream Analytics**. Dina jobb bör visas under dina prenumerationer.

   ![Öppna Stream Analytics-Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Expandera jobbnoden, kan du öppna och visa jobbfråga, konfiguration, indata, utdata och funktioner. 

3. Högerklicka på jobbnoden och väljer den **öppna Jobbvy i portalen** noden för att öppna jobbvyn i Azure-portalen.

   ![Öppna jobbvy i portalen](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa cloud Azure Stream Analytics-jobb i Visual Studio Code (förhandsversion)](quick-create-vs-code.md)
