---
title: Kopiera eller säkerhetskopiera Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du kopierar eller säkerhetskopierar ett Azure Stream Analytics-jobb.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: ba63358347cf9722d2cafa35598b9b3b37f49dc3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129464"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopiera eller säkerhetskopiera Azure Stream Analytics-jobb

Du kan kopiera eller säkerhetskopiera de distribuerade Azure Stream Analytics jobben med Visual Studio Code eller Visual Studio. När du kopierar ett jobb till en annan region kopieras inte den senaste utdata-tiden. Därför kan du inte använda [**när det senast slutade**](./start-job.md#start-options) alternativet när du startade det kopierade jobbet.

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure-portalen](https://portal.azure.com/).

* Installera [Azure Stream Analytics-tillägget för Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) eller [Azure Stream Analytics Tools för Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visuell Studio-kod

1. Klicka på **Azure** -ikonen i aktivitets fältet i Visual Studio Code och expandera **Stream Analytics** nod. Dina jobb bör visas under prenumerationerna.

   ![Öppna Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Om du vill exportera ett jobb till ett lokalt projekt letar du reda på det jobb som du vill exportera i **Stream Analytics Explorer** i Visual Studio Code. Välj sedan en mapp för projektet.

    ![Hitta ASA-jobb i Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Projektet exporteras till den mapp som du väljer och läggs till i den aktuella arbets ytan.

    ![Exportera ASA-jobb i Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Om du vill publicera jobbet till en annan region eller säkerhets kopia med ett annat namn väljer du **Välj bland dina prenumerationer att publicera** i Frågeredigeraren ( \* . asaql) och följer instruktionerna.

    ![Publicera till Azure i Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Följ avsnittet [Exportera ett distribuerat Azure Stream Analytics till projekt instruktioner](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Öppna \* . asaql-filen i Frågeredigeraren, Välj **Skicka till Azure** i skript redigeraren och följ instruktionerna för att publicera jobbet till en annan region eller säkerhets kopia med ett nytt namn.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Stream Analytics jobb genom att använda Visual Studio Code](quick-create-visual-studio-code.md)
* [Snabb start: skapa ett Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Distribuera ett Azure Stream Analytics-jobb med CI/CD med hjälp av Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)