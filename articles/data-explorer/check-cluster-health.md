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
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988435"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Kontrollera hälsotillståndet för ett kluster i Azure Data Explorer

Det finns flera faktorer som påverkar hälsotillståndet för en Azure Data Explorer-klustret, inklusive processor, minne och diskens delsystem. Den här artikeln visar några grundläggande steg som du kan vidta för att mäta hälsotillståndet för ett kluster.

1. Logga in på [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com).

1. I den vänstra rutan, Välj ditt kluster och kör följande kommando.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Utdata för 1 är felfri; utdata för 0 är felfri.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och navigera till ditt kluster.

1. Under **övervakning**väljer **mått**och välj sedan **Keep Alive**, enligt följande bild. Utdata nära 1 innebär ett felfritt kluster.

    ![Klustret Keep Alive mått](media/check-cluster-health/portal-metrics.png)

1. Lägg till andra mått som CPU och minnescachelagring för att mäta resursanvändningen för klustret.

1. Om du behöver hjälp att diagnostisera problem med hälsotillståndet för ett kluster kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com).