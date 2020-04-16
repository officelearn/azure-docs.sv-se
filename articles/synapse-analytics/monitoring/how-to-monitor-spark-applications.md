---
title: Övervaka Apache Spark-program
description: Använd Azure Synapse Studio för att övervaka dina Apache Spark-program.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430752"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Använda Azure Synapse Studio (förhandsversion) för att övervaka dina Apache Spark-program

Med Azure Synapse Analytics kan du använda Spark för att köra anteckningsböcker, jobb och andra typer av program på dina Spark-pooler på arbetsytan.

I den här artikeln beskrivs hur du övervakar dina Spark-program, så att du kan hålla ett öga på den senaste statusen, problemen och förloppet.

## <a name="accessing-the-list-of-spark-applications"></a>Komma åt listan över Spark-program

Om du vill se listan över Spark-program på arbetsytan öppnar du först [Azure Synapse Studio](https://web.azuresynapse.net/) och väljer arbetsyta.

  > [!div class="mx-imgBorder"]
  > ![Logga in på arbetsytan](./media/common/login-workspace.png)

När du har öppnat arbetsytan väljer du avsnittet **Bildskärm** till vänster.

  > [!div class="mx-imgBorder"]
  > ![Välj övervakarnav](./media/common/left-nav.png)

Välj **Spark-program** om du vill visa listan över Spark-program.

  > [!div class="mx-imgBorder"]
  > ![Välj Spark-program](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrera dina Spark-program

Du kan filtrera listan över Spark-program till de du är intresserad av. Med filtren högst upp på skärmen kan du ange ett fält som du vill filtrera mellan.

Du kan till exempel filtrera vyn så att endast Spark-programmen som innehåller namnet "försäljning":

  > [!div class="mx-imgBorder"]
  > ![Knappen Filter](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Exempelfilter](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visa information om ett specifikt Spark-program

Om du vill visa information om ett av dina Spark-program väljer du Spark-programmet och visar informationen. Om Spark-programmet fortfarande körs kan du övervaka förloppet.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [Övervaka pipeline kör Azure Synapse Studio.](how-to-monitor-pipeline-runs.md)  
