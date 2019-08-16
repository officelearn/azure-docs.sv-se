---
title: Välj rätt VM-SKU för ditt Azure Datautforskaren-kluster
description: I den här artikeln beskrivs hur du väljer den optimala SKU-storleken för Azure Datautforskaren-klustret.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2eb23a65196ac4f6456f50dbbbfd9e4b484ad171
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515720"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Välj rätt VM-SKU för ditt Azure Datautforskaren-kluster 

När du skapar ett nytt kluster eller optimerar ett kluster för en ändring av arbets belastningen erbjuder Azure Datautforskaren flera SKU: er för virtuell dator (VM) att välja mellan. De virtuella datorerna har marker ATS noggrant för att ge dig den optimala kostnaden för alla arbets belastningar. 

Data hanterings klustrets storlek och VM-SKU hanteras fullständigt av Azure Datautforskaren-tjänsten. De bestäms av sådana faktorer som motorns storlek på den virtuella datorn och lagrings belastningen. 

Du kan ändra VM-SKU: n för motor klustret när du vill genom att [skala upp klustret](manage-cluster-vertical-scaling.md). Det är bäst att börja med den minsta SKU-storlek som passar det första scenariot. Tänk på att skala upp klustret resulterar i ett drift stopp på upp till 30 minuter medan klustret återskapas med den nya SKU: n för virtuella datorer.

> [!TIP]
> Compute reserverad [instances (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) är tillämplig för Azure datautforskaren-klustret.  

Den här artikeln beskriver olika alternativ för VM SKU och ger teknisk information som kan hjälpa dig att göra det bästa valet.

## <a name="select-a-cluster-type"></a>Välj en klustertyp

Azure Datautforskaren erbjuder två typer av kluster:

* **Produktion**: Produktions kluster innehåller två noder för motor-och data Hanterings kluster och hanteras i [service avtalet](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)för Azure datautforskaren.

* **Utveckling/testning (inget service avtal)** : Dev/test-kluster har en enda D11 v2-nod för motor klustret och en enkel D1-nod för data hanterings klustret. Den här kluster typen är den lägsta kostnads konfigurationen på grund av det låga antalet instanser och ingen avgift för motor pålägg. Det finns inget service avtal för den här kluster konfigurationen eftersom det saknar redundans.

## <a name="sku-types"></a>SKU-typer

När du skapar ett Azure Datautforskaren-kluster väljer du den optimala VM-SKU: n för den planerade arbets belastningen. Du kan välja mellan följande två SKU-familjer för Azure Datautforskaren:

* **D V2**: D-SKU: n är Compute-optimerad och ingår i två varianter:
    * Själva datorn
    * Den virtuella dator som är kopplad till Premium Storage-diskar

* **LS**: L-SKU: n är optimerad för lagring. Den har en mycket större SSD-storlek än den pris värda D SKU: n.

De viktigaste skillnaderna mellan de tillgängliga SKU-typerna beskrivs i följande tabell:
 
| Attribut | D SKU | L SKU |
|---|---|---
|**Små SKU: er**|Minimal storlek är D11 med två kärnor|Minimal storlek är L4 med fyra kärnor |
|**Tillgänglighet**|Tillgängligt i alla regioner (DS + PS-versionen har mer begränsad tillgänglighet)|Tillgängligt i några regioner |
|**Kostnad per&nbsp;GB cache per kärna**|Hög med D-SKU: n med en låg version av DS + PS-versionen|Lägsta med alternativet betala per användning |
|**Priser för reserverade instanser (RI)**|Hög rabatt (över 55&nbsp;procent för ett arbete på tre år)|Lägre rabatt (20&nbsp;procent för ett åtagande på tre år) |  

## <a name="select-your-cluster-vm"></a>Välj din virtuella kluster dator 

[Konfigurera](manage-cluster-vertical-scaling.md#configure-vertical-scaling)den lodräta skalningen för att välja din virtuella kluster dator. 

Med olika alternativ för VM-SKU för att välja bland kan du optimera kostnaderna för prestanda-och frekvent cache-krav för ditt scenario. 
* Om du behöver den optimala prestandan för en hög fråga-volym bör den idealiska SKU: n vara Compute-optimerad. 
* Om du behöver fråga stora mängder data med relativt lägre inläsning av frågor, kan den lagrings optimerade SKU: n minska kostnaderna och ändå ge utmärkta prestanda.

Eftersom antalet instanser per kluster för de små SKU: erna är begränsat är det bättre att använda större virtuella datorer som har större RAM-minne. Det krävs mer RAM-minne för vissa frågetyper som lagrar mer efter frågan på RAM-resursen, till exempel frågor `joins`som använder. När du överväger skalnings alternativ rekommenderar vi därför att du skalar upp till en större SKU i stället för att skala ut genom att lägga till fler instanser.

## <a name="vm-options"></a>Alternativ för virtuell dator

De tekniska specifikationerna för de virtuella Azure-Datautforskaren-klustren beskrivs i följande tabell:

|**Namn**| **Kategori** | **SSD-storlek** | **Kärnor** | **MYCKET** | **Premium Storage-diskar (&nbsp;1 TB)**| **Minsta antal instanser per kluster** | **Maximalt antal instanser per kluster**
|---|---|---|---|---|---|---|---
|D11 v2| Compute-optimerad | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (förutom för dev/test-SKU, som är 1)
|D12 v2| Compute-optimerad | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| Compute-optimerad | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| Compute-optimerad | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| optimerad för lagring | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| optimerad för lagring | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| optimerad för lagring | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| optimerad för lagring | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| optimerad för lagring | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| optimerad för lagring | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| optimerad för lagring | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Du kan visa den uppdaterade listan med virtuella dator-SKU [: er](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)per region med hjälp av Azure datautforskaren ListSkus-API: et. 
* Läs mer om de [olika beräknings SKU: erna](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Nästa steg

* Du kan [skala upp eller ned](manage-cluster-vertical-scaling.md) motor klustret när som helst genom att ändra den virtuella datorns SKU, beroende på olika behov. 

* Du kan [skala in eller skala ut](manage-cluster-horizontal-scaling.md) motor klustrets storlek för att ändra kapacitet, beroende på ändrade krav.

