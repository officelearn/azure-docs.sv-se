---
title: Övervaka Apache Spark program
description: Använd Azure Synapse Studio för att övervaka dina Apache Spark-program.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 0e3b3178cd7600d21032fb8dd4b79dd41ec3f601
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194883"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Använd Azure Synapse Studio (för hands version) för att övervaka dina Apache Spark-program

Med Azure Synapse Analytics kan du använda Spark för att köra antecknings böcker, jobb och andra typer av program på dina Spark-pooler på din arbets yta.

Den här artikeln förklarar hur du övervakar Spark-program, så att du kan hålla ett öga på den senaste statusen, problemen och förloppet.

## <a name="accessing-the-list-of-spark-applications"></a>Åtkomst till listan över Spark-program

Om du vill se en lista över Spark-program i din arbets yta [öppnar du först Azure Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta.

  > [!div class="mx-imgBorder"]
  > ![Logga in på arbets ytan](./media/common/login-workspace.png)

När du har öppnat din arbets yta väljer du avsnittet **övervaka** till vänster.

  > [!div class="mx-imgBorder"]
  > ![Välj övervaka hubb](./media/common/left-nav.png)

Välj **Spark-program** om du vill visa listan över Spark-program.

  > [!div class="mx-imgBorder"]
  > ![Välj Spark-program](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrering av Spark-program

Du kan filtrera listan över Spark-program till de som du är intresse rad av. Med filtren längst upp på skärmen kan du ange ett fält som du vill filtrera.

Du kan till exempel filtrera vyn om du bara vill se de Spark-program som innehåller namnet "Sales":

  > [!div class="mx-imgBorder"]
  > ![Knappen Filtrera](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Exempel filter](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visa information om ett speciellt Spark-program

Om du vill visa information om ett Spark-program väljer du Spark-programmet och visar informationen. Om Spark-programmet fortfarande körs kan du övervaka förloppet.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipelinen kör Azure Synapse Studio](how-to-monitor-pipeline-runs.md) .  
