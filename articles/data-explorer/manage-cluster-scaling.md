---
title: Skala Azure Data Explorer kluster ändrade behov
description: Den här artikeln beskriver steg för att skala ut och skala i ett Azure Data Explorer-kluster utifrån ändrade begäran.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bd3be916587ab884ea5579234a6189af54b53797
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209215"
---
# <a name="manage-cluster-scaling-to-accommodate-changing-demand"></a>Hantera skalning av klustret för att hantera ändrade behov

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med 100% noggrannhet. En statisk klusterstorlek kan leda till under användning eller Överskriden användning, inte är idealiskt. En bättre metod är att *skala* ett kluster, att lägga till och ta bort kapaciteten med ändringen av begäran. Den här artikeln visar hur du hanterar skalning av klustret.

Navigera till ditt kluster och under **inställningar** Välj **skala ut**. Under **konfigurera**väljer **aktivera autoskalning**.

![Aktivera autoskalning](media/manage-cluster-scaling/enable-autoscale.png)

Följande bild visar flödet av de kommande stegen. Vi tillhandahåller mer information finns nedan på bilden.

![Skalningsregel](media/manage-cluster-scaling/scale-rule.png)

1. Under **namn på Autoskalningsinställning**, ange ett namn, till exempel *skalbar: cachelagra användning*.

1. Under **Skalningsläge**väljer **skala baserat på ett mått**. Det här läget ger dynamisk skalning; Du kan också välja **skala till ett specifikt instansantal**.

1. Välj **lägga till en regel**.

1. I den **skalningsregeln** till höger och ange värden för varje inställning.

    | Inställning | Beskrivning och värde |
    | --- | --- | --- |
    | **Tidsmängd** | Välj en aggregering villkor, till exempel **genomsnittlig**. |
    | **Måttnamn** | Välj det mått som du vill att åtgärden ska baseras på, till exempel **Cache användning**. |
    | **Tidsintervallstatistik** | Välj mellan **genomsnittliga**, **minsta**, **maximala**, och **summan**. |
    | **Operator** | Välj lämpligt alternativ, till exempel **större än eller lika med**. |
    | **Tröskelvärde** | Välj ett lämpligt värde. Till exempel är 80% för användning av cache, en bra utgångspunkt. |
    | **Varaktighet** | Välj en lämplig mängd tid för systemet att söka igen vid beräkning av mått. Börja med standard tio minuter. |
    | **Åtgärd** | Välj rätt alternativ för att skala in eller skala ut. |
    | **Instansantal** | Välj antal noder eller instanser som du vill lägga till eller ta bort när ett mått villkor är uppfyllt. |
    | **Väntetid (minuter)** | Välj en lämplig tidsintervallet mellan skalningsåtgärder. Börja med standardvärdet på fem minuter. |
    |  |  |

1. Välj **Lägg till**.

1. I den **Instansgränser** till vänster och ange värden för varje inställning.

    | Inställning | Beskrivning och värde |
    | --- | --- | --- |
    | *Minst* | Detta är antalet instanser som klustret inte skalas nedan, oavsett användning. |
    | *Maximalt* | Detta är antalet instanser som klustret inte skalas ovan, oavsett användning. |
    | *Standard* | Standardantalet instanser som används om det inte går att läsa mätvärden för resurs. |
    |  |  |

1. Välj **Spara**.

Du har nu konfigurerat utskalningen för ditt Azure Data Explorer-kluster. Lägg till en annan regel för att skala in. Detta gör att skala klustret dynamiskt baserat på mätvärden för Resursanvändning som du anger.

Om du behöver hjälp med problem för klusterskalning öppnar du en supportförfrågan i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).