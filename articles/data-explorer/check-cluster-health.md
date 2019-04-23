---
title: Kontrollera hälsotillståndet för ett kluster i Azure Data Explorer
description: Den här artikeln beskriver steg för att övervaka hälsotillståndet för din Azure Data Explorer-klustret.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794669"
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