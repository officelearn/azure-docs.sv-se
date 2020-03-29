---
title: Kontrollera hälsotillståndet för ett Azure Data Explorer-kluster
description: I den här artikeln beskrivs steg för att övervaka hälsotillståndet för azure data explorer-klustret.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861326"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Kontrollera hälsotillståndet för ett Azure Data Explorer-kluster

Det finns flera faktorer som påverkar hälsotillståndet för ett Azure Data Explorer-kluster, inklusive CPU, minne och diskundersystemet. Den här artikeln visar några grundläggande steg du kan vidta för att mäta hälsotillståndet för ett kluster.

1. Logga in [https://dataexplorer.azure.com](https://dataexplorer.azure.com)på .

1. Markera klustret i den vänstra rutan och kör följande kommando.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    En effekt på 1 är hälsosam; en utgång på 0 är ohälsosam.

1. Logga in på [Azure-portalen](https://portal.azure.com)och navigera till ditt kluster.

1. Under **Övervakning**väljer du **Mått**och väljer sedan Håll **vid liv**, som visas i följande bild. En utdata nära 1 innebär ett felfritt kluster.

    ![Mätvärden För klusterhåller vid liv](media/check-cluster-health/portal-metrics.png)

1. Det är möjligt att lägga till andra mått i diagrammet. Markera diagrammet och lägg sedan **till mått**. Välj ett annat mått - det här exemplet visar **CPU**.

    ![Lägga till mått](media/check-cluster-health/add-metric.png)

1. Om du behöver hjälp med att diagnostisera problem med hälsotillståndet för ett kluster öppnar du en supportbegäran i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).