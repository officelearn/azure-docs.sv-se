---
title: Utforska Azure Stream Analytics jobb i Visual Studio Code
description: Den här artikeln visar hur du exporterar ett Azure Stream Analytics jobb till ett lokalt projekt, listar jobb och Visa jobb enheter.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 4b4ddd7db4891c642ec2002bbe992314fe67a20f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891974"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Utforska Azure Stream Analytics med Visual Studio Code (för hands version)

Azure Stream Analytics för kod tillägget Visual Studio ger utvecklare en smidig upplevelse för att hantera sina Stream Analytics-jobb. Den kan användas i Windows, Mac och Linux. Med Azure Stream Analytics-tillägget kan du:

- [Skapa](quick-create-visual-studio-code.md), starta och stoppa jobb
- Exportera befintliga jobb till ett lokalt projekt
- Visa lista över jobb och Visa jobb enheter

## <a name="export-a-job-to-a-local-project"></a>Exportera ett jobb till ett lokalt projekt

Om du vill exportera ett jobb till ett lokalt projekt letar du reda på det jobb som du vill exportera i **Stream Analytics Explorer** i Visual Studio Code. Välj sedan en mapp för projektet. Projektet exporteras till den mapp du väljer och du kan fortsätta att hantera jobbet från Visual Studio Code. Mer information om hur du använder Visual Studio Code för att hantera Stream Analytics-jobb finns i [snabb](quick-create-visual-studio-code.md)starten för Visual Studio Code.

![Exportera ASA-jobb i Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>List jobb och Visa jobb enheter

Du kan använda vyn jobb för att interagera med Azure Stream Analytics-jobb från Visual Studio.


1. Klicka på **Azure** -ikonen i aktivitets fältet i Visual Studio Code och expandera **Stream Analytics Node**. Dina jobb bör visas under prenumerationerna.

   ![Öppna Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Expandera noden jobb kan du öppna och Visa jobb fråga, konfiguration, indata, utdata och funktioner. 

3. Högerklicka på noden jobb och välj **vyn öppna jobb i noden Portal** för att öppna jobb visningen i Azure Portal.

   ![Öppna jobb visning i portalen](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Stream Analytics moln jobb i Visual Studio Code (för hands version)](quick-create-visual-studio-code.md)
