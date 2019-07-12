---
title: Skala upp ett Azure Data Explorer-kluster för att tillgodose ändrade behov
description: Den här artikeln beskriver steg för att skala upp och ned ett Datautforskaren i Azure-kluster utifrån ändrade begäran.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571535"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Hantera klustret skala upp för att hantera ändrade behov

Det finns två arbetsflöden för att skala ett Azure Data Explorer-kluster:
1. [Horisontell skalning](manage-cluster-horizontal-scaling.md), kallas även skala ut och in.
2. Vertikal skalning, kallas även skala upp och ned.

Den här artikeln visar hur du hanterar lodrät skalning av klustret.

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med absolut noggrannhet. En statisk klusterstorlek kan leda till underutnyttjande eller overutilization, inte är idealiskt. En bättre metod är att *skala* ett kluster, att lägga till och ta bort Kapacitets- och CPU-resurser med ändringen av begäran. 

## <a name="steps-to-configure-vertical-scaling"></a>Steg för att konfigurera vertikal skalning

1. Gå till ditt kluster. Under **inställningar**väljer **skala upp**.

    Visas en lista över tillgängliga SKU: er. I följande bild är till exempel bara fyra SKU: er tillgängliga.

    ![Skala upp](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU: er har inaktiverats eftersom de är aktuella SKU: N eller de inte är tillgängliga i den region där klustret finns.

1. Om du vill ändra din SKU, Välj den SKU och välj den **Välj** knappen.

> [!NOTE]
> Lodrät skalning processen kan ta några minuter och under den tiden klustret kommer att inaktiveras. Observera att skala ned kan skada din klusterprestanda.

Nu har du gjort en skala upp eller skala ned åtgärd för ditt Azure Data Explorer-kluster.

Om du behöver hjälp med skalning av klustret problem [öppna en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

* [Hantera kluster horisontell skalning](manage-cluster-horizontal-scaling.md) dynamiskt skala ut instansantalet baserat på mått som du anger.

* Övervaka din Resursanvändning genom att följa den här artikeln: [Övervaka prestanda, hälsotillstånd och användning med mått i Azure Data Explorer](using-metrics.md).

