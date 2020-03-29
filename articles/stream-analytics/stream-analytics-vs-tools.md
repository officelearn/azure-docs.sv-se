---
title: Visa Azure Stream Analytics-jobb i Visual Studio
description: Lär dig hur du visar, startar och stoppar, testar anslutningar, kontrollerar resultat och exporterar dina Azure Stream Analytics-jobb med Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ad35ed342dfd40b98d61919749479ec5612cdd4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369634"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Använda Visual Studio för att visa Azure Stream Analytics-jobb

Azure Stream Analytics-verktyg för Visual Studio gör det enkelt för utvecklare att hantera sina Stream Analytics-jobb direkt från IDE. Med Azure Stream Analytics-verktyg kan du:
- [Skapa nya jobb](stream-analytics-quick-create-vs.md)
- Starta, stoppa och [övervaka jobb](stream-analytics-monitor-jobs-use-vs.md)
- Kontrollera jobbresultat
- Exportera befintliga jobb till ett projekt
- Testa anslutningar för indata och utdata
- [Kör frågor lokalt](stream-analytics-vs-tools-local-run.md)

Lär dig hur du [installerar Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Utforska jobbvyn

Du kan använda jobbvyn för att interagera med Azure Stream Analytics-jobb från Visual Studio.

### <a name="open-the-job-view"></a>Öppna jobbvyn

1. I **Server Explorer**väljer du Stream **Analytics-jobb** och väljer sedan **Uppdatera**. Jobbet ska visas under **Stream Analytics-jobb**.

    ![Strömma analytics-serverutforskaren](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Expandera jobbnoden och dubbelklicka på noden **Jobbvy** för att öppna en jobbvy.
    
   ![Utökad jobbnod](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Starta och stoppa jobb

Azure Stream Analytics-jobb kan hanteras fullständigt från jobbvyn i Visual Studio. Använd kontrollerna för att starta, stoppa eller ta bort ett jobb.
    
   ![Jobbkontroller för Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Kontrollera jobbresultat

Stream Analytics-verktyg för Visual Studio stöder för närvarande förhandsversionen av utdata för Azure Data Lake Storage och blob storage. Om du vill visa resultatet dubbelklickar du bara på utdatanoden för jobbdiagrammet i **jobbvyn** och anger lämpliga autentiseringsuppgifter.

   ![Utdata för Stream Analytics-jobbblob](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Exportera jobb till ett projekt

Du kan exportera ett befintligt jobb till ett projekt på två sätt.

1. Högerklicka på jobbnoden under noden Stream Analytics-jobb i **Server Explorer.** Välj **Exportera till New Stream Analytics Project**.
    
   ![Exportera jobb till projekt](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Det genererade projektet visas i **Solution Explorer**.
    
   ![Solution Explorer](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Välj **Generera projekt**i jobbvyn .
    
   ![Generera projekt från jobbvyn](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testa anslutningar

In- och utdataanslutningar kan testas från **jobbvyn** genom att välja ett alternativ i listrutan **Testanslutning.**

   ![Testanslutningsrullgardering](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**Testanslutningsresultaten** visas i **utdatafönstret.**

   ![Testanslutningsresultat](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera Azure Stream Analytics-jobb med Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Självstudie: Distribuera ett Azure Stream Analytics-jobb med CI/CD med hjälp av Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)
