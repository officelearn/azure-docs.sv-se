---
title: Skala Azure Data Explorer kluster ändrade behov
description: Den här artikeln beskriver steg för att skala upp och skala ned ett Datautforskaren i Azure-kluster utifrån ändrade begäran.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 9d265ec7f0ce2030874f38b99b07343f1d4a3f4d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408654"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Hantera klustret skala upp för att hantera ändrade behov

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med 100% noggrannhet. En statisk klusterstorlek kan leda till under användning eller Överskriden användning, inte är idealiskt. En bättre metod är att *skala* ett kluster, att lägga till och ta bort Kapacitets- och CPU med ändringen av begäran. Det finns två arbetsflöden för skalning, skala upp och skala ut. Den här artikeln visar hur du hanterar klusteruppskalningen.

1. Navigera till ditt kluster och under **inställningar** Välj **skala upp**.

    Du får en lista över tillgängliga SKU: er. Exempel på bilden nedan finns endast en tillgängliga SKU: D14_V2.

    ![Skala upp](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 har inaktiverats eftersom det är den aktuella SKU: N i klustret. L8 och L16 har inaktiverats eftersom de inte är tillgängliga i den region där klustret finns.

1. Om du vill ändra din SKU, Välj den SKU och tryck på den **Välj** knappen.

> [!NOTE]
> Skala upp processen kan ta några minuter. Klustret kommer att inaktiveras under den tiden.
>
> Skala ned kan skada din klusterprestanda.
>

Nu har du utfört en skala upp eller skala ned åtgärd för ditt Azure Data Explorer-kluster. Du kan också göra [kluster skalbar](manage-cluster-scale-out.md), även kallad automatisk skalning skalar dynamiskt baserat på mått som du anger.

Om du behöver hjälp med klusterskalning problem, öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
