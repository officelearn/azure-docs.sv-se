---
title: Skala ut en Azure Data Explorer kluster ändrade behov
description: Den här artikeln beskriver steg för att skala ut och skala i ett Azure Data Explorer-kluster utifrån ändrade begäran.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: ab4ced6695e6066098dd5ff7348528deedfc0e1b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044242"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Hantera kluster skalbar för att hantera ändrade behov

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med absolut noggrannhet. En statisk klusterstorlek kan leda till underutnyttjande eller overutilization, inte är idealiskt.

En bättre metod är att *skala* ett kluster, att lägga till och ta bort kapaciteten med ändringen av begäran. Det finns två arbetsflöden för att skala: skala upp och skala ut. Den här artikeln förklarar skalbar arbetsflödet.

Den här artikeln visar hur du hanterar klustret utskalning, även känt som automatisk skalning. Automatisk skalning kan du skala ut instansantalet automatiskt baserat på fördefinierade regler och scheman. Ange inställningarna för automatisk skalning för ditt kluster i Azure-portalen, enligt beskrivningen i den här artikeln.

Gå till ditt kluster. Under **inställningar**väljer **skala ut**. Under **konfigurera**väljer **aktivera autoskalning**.

![Aktivera autoskalning](media/manage-cluster-scaling/enable-autoscale.png)

Följande bild visar flödet av de kommande stegen. Mer information finns under bilden.

![Skalningsregel](media/manage-cluster-scaling/scale-rule.png)

1. I den **namn på Autoskalningsinställning** ange ett namn, till exempel *skalbar: cachelagra användning*.

1. För **Skalningsläge**väljer **skala baserat på ett mått**. Det här läget ger dynamisk skalning. Du kan också välja **skala till ett specifikt instansantal**.

1. Välj **+ Lägg till en regel**.

1. I den **skalningsregeln** till höger och ange värden för varje inställning.

    **Kriterie**

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Tidsmängd** | Välj en aggregering villkor, till exempel **genomsnittlig**. |
    | **Måttnamn** | Välj det mått som du vill att åtgärden ska baseras på, till exempel **Cache användning**. |
    | **Tidsintervallstatistik** | Välj mellan **genomsnittliga**, **minsta**, **maximala**, och **summan**. |
    | **Operator** | Välj lämpligt alternativ, till exempel **större än eller lika med**. |
    | **Tröskelvärde** | Välj ett lämpligt värde. Till exempel är 80 procent för användning av cache, en bra utgångspunkt. |
    | **Varaktighet (i minuter)** | Välj en lämplig mängd tid för systemet att söka igen vid beräkning av mått. Börja med standardvärdet 10 minuter. |
    |  |  |

    **Åtgärd**

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Åtgärd** | Välj rätt alternativ för att skala in eller skala ut. |
    | **Antal instanser** | Välj antal noder eller instanser som du vill lägga till eller ta bort när ett mått villkor är uppfyllt. |
    | **Väntetid (minuter)** | Välj en lämplig tidsintervallet mellan skalningsåtgärder. Börja med standardvärdet på fem minuter. |
    |  |  |

1. Välj **Lägg till**.

1. I den **Instansgränser** till vänster och ange värden för varje inställning.

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Minimum** | Antalet instanser som klustret inte skalas nedan, oavsett användning. |
    | **Maximal** | Antalet instanser som klustret inte skalas ovan, oavsett användning. |
    | **Standard** | Standardantalet instanser. Den här inställningen används om det är problem med att läsa. |
    |  |  |

1. Välj **Spara**.

Du har nu konfigurerat utskalningen för ditt Azure Data Explorer-kluster. Lägg till en annan regel för att skala in. Den här konfigurationen kan klustret för att skala dynamiskt baserat på mått som du anger.

Du kan också [hantera klusteruppskalningen](manage-cluster-scale-up.md) för lämplig storlek i ett kluster.

Om du behöver hjälp med skalning av klustret problem [öppna en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure-portalen.
