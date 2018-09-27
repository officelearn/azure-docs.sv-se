---
title: Visa Azure Stream Analytics-jobb i Visual Studio
description: Den här artikeln beskriver hur du visar Stream Analytics-jobb i Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: f9bc9bf540f7f44e07bc4bb0ae8ef6910ae78476
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222394"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Använda Visual Studio för att visa Azure Stream Analytics-jobb

Azure Stream Analytics-verktyg för Visual Studio gör det enkelt för utvecklare för att hantera sina Stream Analytics-jobb direkt från IDE. Med Azure Stream Analytics-verktyg kan du:
- [Skapa nya jobb](stream-analytics-quick-create-vs.md)
- Starta, stoppa, och [övervaka jobb](stream-analytics-monitor-jobs-use-vs.md)
- Resultat av jobb
- Exportera befintliga jobb till ett projekt
- Testa inkommande och utgående anslutningar
- [Köra frågor lokalt](stream-analytics-vs-tools-local-run.md)

Lär dig hur du [installera Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Utforska jobbvyn

Du kan använda jobbvyn för att interagera med Azure Stream Analytics-jobb från Visual Studio.

### <a name="open-the-job-view"></a>Öppna jobbvyn

1. I **Server Explorer**väljer **Stream Analytics-jobb** och välj sedan **uppdatera**. Ditt jobb bör visas under **Stream Analytics-jobb**.

    ![Stream Analytics-serverlista explorer](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Expandera jobbnoden och dubbelklicka på den **Jobbvy** noden för att öppna vyn jobb.
    
   ![Utökade jobbet nod](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Starta och stoppa jobb

Azure Stream Analytics-jobb kan vara fullständigt hanterade från jobbvyn i Visual Studio. Använd kontrollerna för att starta, stoppa eller ta bort ett jobb.
    
   ![Stream Analytics-jobbet kontroller](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Resultat av jobb

Stream Analytics-verktyg för Visual Studio stöder för närvarande Förhandsgranska utdata för Azure Data Lake Storage och blob storage. Om du vill visa resultatet helt enkelt Dubbelklicka på noden utdata i jobbdiagrammet i **Jobbvy** och ange rätt autentiseringsuppgifter.

   ![Stream Analytics-jobbutdata blob](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Exportera jobb till ett projekt

Det finns två sätt som du kan exportera ett befintligt projekt till ett projekt.

1. I **Server Explorer**, högerklicka på jobbnoden under noden Stream Analytics-jobb. Välj **exportera till nytt Stream Analytics projekt**.
    
   ![Exportera jobb till projekt](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Det genererade projektet visas i **Solution Explorer**.
    
   ![Solution explorer](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Välj i jobbvyn **generera projekt**.
    
   ![Skapa projekt från jobbvy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testa anslutningar

Du kan testa inkommande och utgående anslutningar från den **Jobbvy** genom att välja ett alternativ från den **Testanslutningen** listrutan.

   ![Testa anslutningen listrutan](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Den **Testanslutningen** visas resultaten i **utdata** fönster.

   ![Testresultat för anslutning](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera Azure Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Självstudie: Distribuera Azure Stream Analytics-jobb med CI/CD med hjälp av Azure-Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)
