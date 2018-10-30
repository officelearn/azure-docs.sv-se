---
title: Kontrollera hälsotillståndet för ett kluster i Azure Data Explorer
description: Den här artikeln beskriver steg för att fastställa om din Azure Data Explorer-klustret är felfritt.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d07873b34a41ff20b5007a88743f6b150d4d8a3d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212836"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Kontrollera hälsotillståndet för ett kluster i Azure Data Explorer

Det finns flera faktorer som påverkar hälsotillståndet för en Azure Data Explorer-klustret, inklusive processor, minne och diskens delsystem. Den här artikeln visar några grundläggande steg som du kan vidta för att mäta hälsotillståndet för ett kluster.

1. Logga in på [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. I den vänstra rutan, Välj ditt kluster och kör följande kommando.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Utdata för 1 är felfri; utdata för 0 är felfri.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och navigera till ditt kluster.

1. Under **övervakning**väljer **mått**och välj sedan **Keep Alive**, enligt följande bild. Utdata nära 1 innebär ett felfritt kluster.

    ![Klustret Keep Alive mått](media/check-cluster-health/portal-metrics.png)

1. Det är möjligt att lägga till andra mått i diagrammet. Välj diagrammet sedan **Lägg till mått**. Välj ett annat mått – det här exemplet visar **CPU**.

    ![Lägga till mått](media/check-cluster-health/add-metric.png)

1. Om du behöver hjälp att diagnostisera problem med hälsotillståndet för ett kluster kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).