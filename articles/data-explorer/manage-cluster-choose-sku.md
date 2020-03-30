---
title: Välj rätt VM SKU för Azure Data Explorer-klustret
description: I den här artikeln beskrivs hur du väljer den optimala SKU-storleken för Azure Data Explorer-kluster.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561858"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Välj rätt VM SKU för Azure Data Explorer-klustret 

När du skapar ett nytt kluster eller optimerar ett kluster för en föränderlig arbetsbelastning erbjuder Azure Data Explorer flera virtuella datorer att välja mellan. De virtuella datorerna har valts ut noggrant för att ge dig den mest optimala kostnaden för alla arbetsbelastningar. 

Storleken och VM SKU för datahanteringsklustret hanteras helt av Azure Data Explorer-tjänsten. De bestäms av faktorer som motorns VM-storlek och inmatningsarbetsbelastningen. 

Du kan ändra VM SKU för motorklustret när som helst genom [att skala upp klustret](manage-cluster-vertical-scaling.md). Det är bäst att börja med den minsta SKU-storleken som passar det ursprungliga scenariot. Tänk på att skala upp klustret resulterar i en driftstopp på upp till 30 minuter medan klustret återskapas med den nya VM SKU.

> [!TIP]
> [Beräkningsreserverade instanser (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) gäller för Azure Data Explorer-klustret.  

I den här artikeln beskrivs olika VM SKU-alternativ och de tekniska detaljer som kan hjälpa dig att göra det bästa valet.

## <a name="select-a-cluster-type"></a>Välj en klustertyp

Azure Data Explorer erbjuder två typer av kluster:

* **Produktion**: Produktionskluster innehåller två noder för motor- och datahanteringskluster och drivs under Azure Data Explorer [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/).

* **Utveckling/test (ingen SLA)**: Kluster för utveckling/test har en enda D11 v2-nod för motorklustret och en enda D1-nod för datahanteringsklustret. Den här klustertypen är den lägsta kostnadskonfigurationen på grund av dess låga instansantal och ingen motormarkeringsavgift. Det finns inget serviceavtal för den här klusterkonfigurationen eftersom det saknar redundans.

## <a name="sku-types"></a>SKU-typer

När du skapar ett Azure Data Explorer-kluster väljer du den *optimala* VM SKU för den planerade arbetsbelastningen. Du kan välja mellan följande två SKU-familjer i Azure Data Explorer:

* **D v2**: D SKU är beräkningsoptimerad och finns i två smaker:
    * Själva den virtuella datorn
    * Den virtuella datorn levereras med premium lagringsdiskar

* **LS**: L SKU är lagringsoptimerad. Den har en mycket större SSD storlek än liknande prissatta D SKU.

De viktigaste skillnaderna mellan de tillgängliga SKU-typerna beskrivs i följande tabell:
 
| Attribut | D SKU | L SKU |
|---|---|---
|**Små SKU:er**|Minimal storlek är D11 med två kärnor|Minimal storlek är L4 med fyra kärnor |
|**Tillgänglighet**|Tillgänglig i alla regioner (DS+PS-versionen har mer begränsad tillgänglighet)|Finns i några regioner |
|**Kostnad&nbsp;per GB-cache per kärna**|Hög med D SKU, låg med DS + PS-versionen|Lägsta med alternativet Betala per dun |
|**Prissättning av reserverade instanser (RI)**|Hög rabatt (över&nbsp;55 procent för ett treårigt åtagande)|Lägre rabatt (20&nbsp;procent för ett treårigt åtagande) |  

## <a name="select-your-cluster-vm"></a>Välj kluster-VM 

Om du vill välja kluster-VM [konfigurerar du lodrät skalning](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Med olika VM SKU-alternativ att välja mellan kan du optimera kostnaderna för prestanda- och hot-cache-kraven för ditt scenario. 
* Om du behöver den mest optimala prestanda för en hög frågevolym bör den idealiska SKU vara beräkningsoptimerad. 
* Om du behöver fråga stora mängder data med relativt lägre frågebelastning kan den lagringsoptimerade SKU bidra till att minska kostnaderna och ändå ge utmärkt prestanda.

Eftersom antalet instanser per kluster för de små SKU:erna är begränsat är det bättre att använda större virtuella datorer som har större RAM-minne. Mer RAM-minne behövs för vissa frågetyper som ställer större efterfrågan `joins`på RAM-resursen, till exempel frågor som använder . När du funderar på skalningsalternativ rekommenderar vi därför att du skalar upp till en större SKU i stället för att skala ut genom att lägga till fler instanser.

## <a name="vm-options"></a>Alternativ för virtuell dator

De tekniska specifikationerna för virtuella azure data explorer-kluster-datorer beskrivs i följande tabell:

|**Namn**| **Kategori** | **SSD-storlek** | **Kärnor** | **Ram** | **Premium-lagringsdiskar&nbsp;(1 TB)**| **Minsta antal instanser per kluster** | **Maximalt antal instanser per kluster**
|---|---|---|---|---|---|---|---
|D11 v2| beräkningsoptimerad | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (med undantag för dev/test SKU, som är 1)
|D12 v2| beräkningsoptimerad | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| beräkningsoptimerad | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| beräkningsoptimerad | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| lagringsoptimerad | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| lagringsoptimerad | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| lagringsoptimerad | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| lagringsoptimerad | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| lagringsoptimerad | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| lagringsoptimerad | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| lagringsoptimerad | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Du kan visa den uppdaterade VM SKU-listan per region med hjälp av Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Läs mer om de [olika SKU:erna](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Nästa steg

* Du kan [skala upp eller skala ned](manage-cluster-vertical-scaling.md) motorklustret när som helst genom att ändra VM SKU, beroende på olika behov. 

* Du kan [skala in eller skala ut](manage-cluster-horizontal-scaling.md) motorklustrets storlek för att ändra kapaciteten, beroende på ändrade krav.

