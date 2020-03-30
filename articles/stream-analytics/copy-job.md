---
title: Kopiera eller säkerhetskopiera Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du kopierar eller säkerhetskopierar ett Azure Stream Analytics-jobb.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771503"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopiera eller säkerhetskopiera Azure Stream Analytics-jobb

Du kan kopiera eller säkerhetskopiera dina distribuerade Azure Stream Analytics-jobb med Visual Studio-kod eller Visual Studio. 

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure-portalen](https://portal.azure.com/).

* Installera [Azure Stream Analytics-tillägg för Visual Studio-kod](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) eller [Azure Stream Analytics-verktyg för Visual Studio](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio-koden

1. Klicka på **Azure-ikonen** i Aktivitetsfältet för Visual Studio-kod och expandera sedan Stream Analytics-noden. **Stream Analytics** Dina jobb ska visas under dina prenumerationer.

   ![Öppna Utforskaren för Utforskaren för Öppna Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Om du vill exportera ett jobb till ett lokalt projekt letar du reda på det jobb som du vill exportera i **Stream Analytics Explorer** i Visual Studio-kod. Välj sedan en mapp för projektet.

    ![Exportera ASA-jobb i Visual Studio-kod](./media/vscode-explore-jobs/export-job.png)

    Projektet exporteras till den mapp du väljer och läggs till på den aktuella arbetsytan.

    ![Exportera ASA-jobb i Visual Studio-kod](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Om du vill publicera jobbet i en annan region eller säkerhetskopia med ett annat namn väljer du **Välj från dina prenumerationer som ska publiceras** i frågeredigeraren (\*.asaql) och följer instruktionerna.

    ![Publicera till Azure i Visual Studio-kod](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Följ [exportera ett distribuerat Azure Stream Analytics-jobb till en projektinstruktion .](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)

2. Öppna \*ASAQL-filen i Frågeredigeraren, välj **Skicka till Azure** i skriptredigeraren och följ instruktionerna för att publicera jobbet till en annan region eller säkerhetskopia med ett nytt namn.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Visual Studio-kod](quick-create-vs-code.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Distribuera ett Azure Stream Analytics-jobb med CI/CD med hjälp av Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
