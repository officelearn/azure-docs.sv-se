---
title: Hantera klustervertikel (skala upp) för att matcha efterfrågan i Azure Data Explorer
description: I den här artikeln beskrivs steg för att skala upp och skala ned ett Azure Data Explorer-kluster baserat på förändrade behov.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560447"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Hantera klustervertikel (skala upp) i Azure Data Explorer för att tillgodose förändrade behov

Att dimensionera ett kluster på rätt sätt är avgörande för prestanda för Azure Data Explorer. En statisk klusterstorlek kan leda till underutnyttjande eller överutnyttjande, varav ingen är idealisk.

Eftersom efterfrågan på ett kluster inte kan förutsägas med absolut noggrannhet, är en bättre metod att *skala* ett kluster, lägga till och ta bort kapacitet och CPU-resurser med förändrade efterfrågan. 

Det finns två arbetsflöden för skalning av ett Azure Data Explorer-kluster:

* [Vågrät skalning](manage-cluster-horizontal-scaling.md), även kallad skalning in och ut.
* Vertikal skalning, även kallad skalning upp och ned.

I den här artikeln beskrivs arbetsflödet för lodrät skalning:

## <a name="configure-vertical-scaling"></a>Konfigurera lodrät skalning

1. Gå till din Azure Data Explorer-klusterresurs i Azure-portalen. Under **Inställningar**väljer du **Skala upp**.

1. I fönstret **Skala upp** visas en lista över tillgängliga SKU:er för klustret. I följande bild är till exempel endast fyra SKU:er tillgängliga.

    ![Skala upp](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU:erna är inaktiverade eftersom de är den aktuella SKU:n eller inte är tillgängliga i den region där klustret finns.

1. Om du vill ändra SKU:n väljer du en ny SKU och klickar på **Välj**.

> [!NOTE]
> * Den vertikala skalningsprocessen kan ta några minuter och under den tiden pausas klustret. 
> * Nedskalning kan skada klusterprestandan.
> * Priset är en uppskattning av klustrets virtuella datorer och Azure Data Explorer-tjänstkostnader. Övriga kostnader ingår inte. Se sidan [för kostnadsuppskattningstabell](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) för Azure Data Explorer för fullständig prisinformation för Azure Data [Explorer.](https://azure.microsoft.com/pricing/details/data-explorer/)

Du har nu konfigurerat vertikal skalning för ditt Azure Data Explorer-kluster. Lägg till en annan regel för en vågrät skalning. Om du behöver hjälp med problem med klusterskalning [öppnar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

* [Hantera kluster vågrät skalning](manage-cluster-horizontal-scaling.md) för att dynamiskt skala ut instansantalet baserat på mått som du anger.

* Övervaka resursanvändningen genom att följa den här artikeln: [Övervaka Prestanda, hälsa och användning i Azure Data Explorer med mått](using-metrics.md).

