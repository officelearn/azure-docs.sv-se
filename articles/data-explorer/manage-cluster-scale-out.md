---
title: Skala Azure Data Explorer kluster ändrade behov
description: Den här artikeln beskriver steg för att skala ut och skala i ett Azure Data Explorer-kluster utifrån ändrade begäran.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 15ef5282e0a073e870f2ac12b5fc442407535770
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408450"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Hantera kluster skalbar för att hantera ändrade behov

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med 100% noggrannhet. En statisk klusterstorlek kan leda till under användning eller Överskriden användning, inte är idealiskt. En bättre metod är att *skala* ett kluster, att lägga till och ta bort kapaciteten med ändringen av begäran. Det finns två arbetsflöden för skalning, skala upp och skala ut. Den här artikeln förklarar skalbar arbetsflödet.

Den här artikeln visar hur du hanterar klustret utskalning, även känt som automatisk skalning. Automatisk skalning kan du skala ut automatiskt baserat på fördefinierade regler och scheman antal instanser. Avgör dina inställningar för automatisk skalning för ditt kluster i Azure-portalen, enligt beskrivningen nedan.

Navigera till ditt kluster och under **inställningar** Välj **skala ut**. Under **konfigurera**väljer **aktivera autoskalning**.

![Aktivera autoskalning](media/manage-cluster-scaling/enable-autoscale.png)

Följande bild visar flödet av de kommande stegen. Vi tillhandahåller mer information finns nedan på bilden.

![Skalningsregel](media/manage-cluster-scaling/scale-rule.png)

1. Under **namn på Autoskalningsinställning**, ange ett namn, till exempel *skalbar: cachelagra användning*.

1. Under **Skalningsläge**väljer **skala baserat på ett mått**. Det här läget ger dynamisk skalning; Du kan också välja **skala till ett specifikt instansantal**.

1. Välj **lägga till en regel**.

1. I den **skalningsregeln** till höger och ange värden för varje inställning.

    **Kriterie**

    | Inställning | Beskrivning och värde |
    | --- | --- | --- |
    | **Tidsmängd** | Välj en aggregering villkor, till exempel **genomsnittlig**. |
    | **Måttnamn** | Välj det mått som du vill att åtgärden ska baseras på, till exempel **Cache användning**. |
    | **Tidsintervallstatistik** | Välj mellan **genomsnittliga**, **minsta**, **maximala**, och **summan**. |
    | **Operator** | Välj lämpligt alternativ, till exempel **större än eller lika med**. |
    | **Tröskelvärde** | Välj ett lämpligt värde. Till exempel är 80% för användning av cache, en bra utgångspunkt. |
    | **Varaktighet (i minuter)** | Välj en lämplig mängd tid för systemet att söka igen vid beräkning av mått. Börja med standardvärdet 10 minuter. |
    |  |  |

    **Åtgärd**

    | Inställning | Beskrivning och värde |
    | --- | --- | --- |
    | **Åtgärd** | Välj rätt alternativ för att skala in eller skala ut. |
    | **Instansantal** | Välj antal noder eller instanser som du vill lägga till eller ta bort när ett mått villkor är uppfyllt. |
    | **Väntetid (minuter)** | Välj en lämplig tidsintervallet mellan skalningsåtgärder. Börja med standardvärdet på fem minuter. |
    |  |  |

1. Välj **Lägg till**.

1. I den **Instansgränser** till vänster och ange värden för varje inställning.

    | Inställning | Beskrivning och värde |
    | --- | --- | --- |
    | *Minimum* | Antalet instanser som klustret inte skalas nedan, oavsett användning. |
    | *Maximalt* | Antalet instanser som klustret inte skalas ovan, oavsett användning. |
    | *Standard* | Standardantalet instanser som används om det inte går att läsa mätvärden för resurs. |
    |  |  |

1. Välj **Spara**.

Du har nu konfigurerat utskalningen för ditt Azure Data Explorer-kluster. Lägg till en annan regel för att skala in. Detta gör att skala klustret dynamiskt baserat på mått som du anger.

Du kan också göra [klustret skala upp](manage-cluster-scale-up.md) för lämplig storlek i ett kluster.

Om du behöver hjälp med klusterskalning problem, öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
