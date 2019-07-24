---
title: Välj rätt VM-SKU för ditt Azure Datautforskaren-kluster
description: I den här artikeln beskrivs hur du väljer den optimala SKU-storleken för Azure Datautforskaren-klustret.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226140"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Välj rätt VM-SKU för ditt Azure Datautforskaren-kluster 

Azure Datautforskaren har flera VM-SKU: er som du kan välja när du skapar ett nytt kluster eller optimerar klustret för en ändrings arbets belastning. De virtuella datorerna väljs försiktigt för att möjliggöra den optimala kostnaden för alla arbets belastningar. 

Data hanterings klustrets storlek och VM-SKU hanteras fullständigt av Azure Datautforskaren-tjänsten. Det bestäms av faktorer som motorns storlek på den virtuella datorn och lagrings belastningen. 

VM-SKU: n för motor klustret kan ändras när som helst genom att [skala upp klustret](manage-cluster-vertical-scaling.md). Därför är det bäst att börja med den minsta SKU-storlek som passar det första scenariot. Tänk på att skala upp klustret resulterar i ett drift stopp på upp till 30 minuter medan klustret återskapas med den nya SKU: n för virtuella datorer.

> [!TIP]
> Compute [RI (reserverade instanser)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) gäller för Azure datautforskaren-kluster.  

Den här artikeln beskriver de olika alternativen för VM SKU och ger teknisk information som kan hjälpa dig att göra det bästa valet.

## <a name="select-the-cluster-type"></a>Välj kluster typ

Azure Datautforskaren erbjuder två typer av kluster:

* **Produktion**: Produktions kluster innehåller två noder för motor-och data Hanterings kluster och hanteras i [service avtalet](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)för Azure datautforskaren.

* **Utveckling/testning (inget service avtal)** : Utvecklings-och test kluster har en enda D11_v2-nod för motor klustret och en enkel D1-nod för data hanterings klustret. Den här kluster typen är den lägsta kostnads konfigurationen på grund av antalet låga instanser och ingen avgift för motor pålägg. Det finns inget service avtal för den här kluster konfigurationen eftersom det saknar redundans.

## <a name="sku-types"></a>SKU-typer

När du skapar ett Azure Datautforskaren-kluster väljer  du den optimala VM-SKU: n för den planerade arbets belastningen. Azure Datautforskaren har två SKU-familjer som du kan välja mellan:

* **D_V2**: **D** -SKU: n beräknas som optimerad och tillhandahålls i två varianter.
    * Själva datorn
    * Virtuell dator med Premium Storage-diskar

* **LS**: **L** -SKU: n är optimerad för lagring. Den har en mycket större SSD-storlek än den liknande **prisbaserade** SKU: n.

Följande tabell innehåller viktiga skillnader mellan tillgängliga SKU-typer:
 
|**Basattributet** | **D SKU** | **L SKU**
|---|---|---
|**Små SKU: er**|Minimal storlek är 11 med två kärnor|Minimal storlek är L4 med fyra kärnor
|**Tillgänglighet**|Tillgängligt i alla regioner (DS + PS-versionen har mer begränsad tillgänglighet)|Tillgängligt i några regioner
|**Kostnad per GB cache per kärna**|Hög med D-SKU: n med en låg version av DS + PS-versionen|Billigaste med alternativet *betala per* användning
|**Priser för RI (reserverad instans)**|Hög rabatt (över 55% för ett arbete på tre år)|Lägre rabatt (20% för ett åtagande på tre år)  

## <a name="select-your-cluster-vm"></a>Välj din virtuella kluster dator 

[Konfigurera](manage-cluster-vertical-scaling.md#configure-vertical-scaling)den lodräta skalningen för att välja din virtuella kluster dator. 

Med de olika alternativen för VM-SKU kan du optimera kostnaderna för nödvändiga prestanda-och frekvent cache-krav för det önskade scenariot. Om scenariot kräver mest optimala prestanda för en hög fråga-volym bör den idealiska SKU: n vara Compute-optimerad. Å andra sidan, om scenariot kräver att fråga stora mängder data med relativt låg inläsning av fråga, minskar den optimerade lagrings enheten kostnader samtidigt som de ger utmärkta prestanda.

Antalet instanser per kluster för de små SKU: erna är begränsat, så det är bättre att använda större virtuella datorer som har större RAM-minne. RAM-storleken krävs för vissa frågetyper som har mer efter frågan på RAM-resursen, till exempel de frågor som använder `joins`. När du överväger skalnings alternativ bör du därför uppmanas att skala upp till en större SKU än att skala ut genom att lägga till fler instanser.

## <a name="vm-options"></a>Alternativ för virtuell dator

Följande tabell innehåller de tekniska specifikationerna för de virtuella Azure-Datautforskaren-klustren:

|**Namn**| **Kategori** | **SSD-storlek** | **Kärnor** | **MYCKET** | **Premium Storage-diskar (1 TB)**| **Minsta antal instanser per kluster** | **Maximalt antal instanser per kluster**
|---|---|---|---|---|---|---|---
|D11_v2| optimerad data behandling | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (förutom för utveckling/test-SKU: er där det är 1)
|D12_v2| optimerad data behandling | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| optimerad data behandling | 307 GB   | 8 | 56 GB | 0 | 2 | 1,000
|D14_v2| optimerad data behandling | 614 GB   | 16| 112 GB | 0 | 2 | 1,000
|DS13_v2 + 1 TB PS| optimerad lagring | 1 TB | 8 | 56 GB | 1 | 2 | 1,000
|DS13_v2 + 2 TB PS| optimerad lagring | 2 TB | 8 | 56 GB | 2 | 2 | 1,000
|DS14_v2 + 3 TB PS| optimerad lagring | 3 TB | 16 | 112 GB | 2 | 2 | 1,000
|DS14_v2 + 4 TB PS| optimerad lagring | 4 TB | 16 | 112 GB | 4 | 2 | 1,000
|L4s_v1| optimerad lagring | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| optimerad lagring | 1,3 TB | 8 | 64 GB | 0 | 2 | 1,000
|L16s_1| optimerad lagring | 2,6 TB | 16| 128 GB | 0 | 2 | 1,000

* Visa den uppdaterade VM SKU-listan per region med hjälp av Azure Datautforskaren [ListSkus-API: et](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Läs mer om de [olika beräknings SKU: erna](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Nästa steg

* Motor klustret kan skalas [upp eller ned](manage-cluster-vertical-scaling.md) när som helst genom att ändra den virtuella datorns SKU för olika behov. 

* Motor klustrets storlek kan [skalas in och ut](manage-cluster-horizontal-scaling.md) för att ändra kapacitet med justerbara krav.

