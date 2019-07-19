---
title: Hantera vertikalt kluster skalning (skala upp) i Azure Datautforskaren för att hantera ändring efter frågan
description: Den här artikeln beskriver steg för steg hur du skalar upp och ned ett Azure Datautforskaren-kluster baserat på ändring av efter frågan.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 80bbdf3a5d936719b06782cd78d56088b36cb21d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985484"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Hantera vertikalt kluster skalning (skala upp) i Azure Datautforskaren för att hantera ändring efter frågan

Att ändra storlek på ett kluster är på lämpligt sätt avgörande för Azure Datautforskarens prestanda. En statisk kluster storlek kan leda till användning eller över-användning, men ingen av dem är idealisk.

Eftersom efter frågan på ett kluster inte kan förutsägas med absolut noggrannhet är en bättre metod att *skala* ett kluster, lägga till och ta bort kapacitets-och CPU-resurser med justerbar efter frågan. 

Det finns två arbets flöden för skalning av ett Azure Datautforskaren-kluster:

* [Horisontell skalning](manage-cluster-horizontal-scaling.md), även kallat skalning in och ut.
* Vertikal skalning, även kallat skalning upp och ned.

I den här artikeln förklaras arbets flödet för lodrät skalning:

## <a name="configure-vertical-scaling"></a>Konfigurera vertikal skalning

1. I Azure Portal går du till Azure Datautforskaren kluster resursen. Under **Inställningar**väljer du **skala upp**.

1. I fönstret **skala upp** visas en lista över tillgängliga SKU: er för klustret. I följande figur är till exempel endast fyra SKU: er tillgängliga.

    ![Skala upp](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU: erna är inaktiverade eftersom de är aktuella SKU: er, eller så är de inte tillgängliga i den region där klustret finns.

1. Om du vill ändra din SKU väljer du en ny SKU och klickar på **Välj**.

> [!NOTE]
> * Den vertikala skalnings processen kan ta några minuter och under den tiden kommer klustret att inaktive ras. 
> * Genom att skala ned kan du skada kluster prestanda.
> * Priset är en uppskattning av klustrets virtuella datorer och Azure Datautforskaren service-kostnader. Andra kostnader ingår inte. Se sidan för Azure Datautforskaren [Cost](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) -uppskattning för en uppskattning och [pris sidan](https://azure.microsoft.com/pricing/details/data-explorer/) för Azure datautforskaren för fullständig pris information.

Nu har du konfigurerat vertikal skalning för ditt Azure Datautforskaren-kluster. Lägg till en annan regel för en vågrät skalning. Om du behöver hjälp med problem med kluster skalning [öppnar du en support förfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure Portal.

## <a name="next-steps"></a>Nästa steg

* [Hantera vågräta kluster skalning](manage-cluster-horizontal-scaling.md) för att dynamiskt skala ut antalet instanser baserat på mått som du anger.

* Övervaka din resursanvändning genom att följa den här artikeln: [Övervaka Azure datautforskaren prestanda, hälsa och användning med mått](using-metrics.md).

