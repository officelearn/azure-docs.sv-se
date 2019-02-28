---
title: Skala Azure Data Explorer kluster ändrade behov
description: Den här artikeln beskriver steg för att skala upp och ned ett Datautforskaren i Azure-kluster utifrån ändrade begäran.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: a74c529fc3543d5cbdcf009a5b7736309e15569e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961711"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Hantera klustret skala upp för att hantera ändrade behov

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med absolut noggrannhet. En statisk klusterstorlek kan leda till underutnyttjande eller overutilization, inte är idealiskt.

En bättre metod är att *skala* ett kluster, att lägga till och ta bort Kapacitets- och CPU-resurser med ändringen av begäran. Det finns två arbetsflöden för att skala: skala upp och skala ut. Den här artikeln visar hur du hanterar klusteruppskalningen.

1. Gå till ditt kluster. Under **inställningar**väljer **skala upp**.

    Visas en lista över tillgängliga SKU: er. I följande bild är till exempel bara fyra SKU: er tillgängliga.

    ![Skala upp](media/manage-cluster-scale-up/scale-up.png)

    SKU: er har inaktiverats eftersom de är aktuella SKU: N eller de inte är tillgängliga i den region där klustret finns.

1. Om du vill ändra din SKU, Välj den SKU och välj den **Välj** knappen.

> [!NOTE]
> Skala upp processen kan ta några minuter och under den tiden klustret kommer att inaktiveras. Observera att skala ned kan skada din klusterprestanda.

Nu har du gjort en skala upp eller skala ned åtgärd för ditt Azure Data Explorer-kluster. Du kan också [hantera klustret skalbar](manage-cluster-scale-out.md) dynamiskt skala ut instansantalet baserat på mått som du anger.

Om du behöver hjälp med skalning av klustret problem [öppna en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure-portalen.
