---
title: Utforska Azure Stream Analytics-jobb i Visual Studio-kod
description: Den här artikeln visar hur du exporterar ett Azure Stream Analytics-jobb till ett lokalt projekt, listar jobb och visar jobbentiteter.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479246"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Utforska Azure Stream Analytics med Visual Studio-kod (förhandsversion)

Azure Stream Analytics för Visual Studio-kodtillägg ger utvecklare en lätt upplevelse för att hantera sina Stream Analytics-jobb. Den kan användas på Windows, Mac och Linux. Med Azure Stream Analytics-tillägget kan du:

- [Skapa,](quick-create-vs-code.md)starta och stoppa jobb
- Exportera befintliga jobb till ett lokalt projekt
- Lista jobb och visa jobbentiteter

## <a name="export-a-job-to-a-local-project"></a>Exportera ett jobb till ett lokalt projekt

Om du vill exportera ett jobb till ett lokalt projekt letar du reda på det jobb som du vill exportera i **Stream Analytics Explorer** i Visual Studio-kod. Välj sedan en mapp för projektet. Projektet exporteras till den mapp du väljer och du kan fortsätta att hantera jobbet från Visual Studio Code. Mer information om hur du använder Visual Studio-kod för att hantera Stream Analytics-jobb finns i [snabbstarten](quick-create-vs-code.md)för Visual Studio-kod .

![Exportera ASA-jobb i Visual Studio-kod](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Lista jobb- och visa jobbentiteter

Du kan använda jobbvyn för att interagera med Azure Stream Analytics-jobb från Visual Studio.


1. Klicka på **Azure-ikonen** i Aktivitetsfältet för Visual Studio-kod och expandera sedan **Noden Stream Analytics**. Dina jobb ska visas under dina prenumerationer.

   ![Öppna Utforskaren för Utforskaren för Öppna Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Expandera jobbnoden, du kan öppna och visa jobbfrågan, konfiguration, indata, utdata och funktioner. 

3. Högerklicka på jobbnoden och välj **noden Öppna jobbvy i portalen** för att öppna jobbvyn i Azure-portalen.

   ![Öppna jobbvyn i portalen](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Stream Analytics-molnjobb i Visual Studio-kod (förhandsversion)](quick-create-vs-code.md)
