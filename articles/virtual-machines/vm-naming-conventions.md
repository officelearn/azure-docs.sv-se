---
title: Namn konventioner för Azure VM-storlekar
description: Förklarar namngivnings konventioner som används för virtuella Azure-storlekar
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131726"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Namngivnings konventioner för virtuella Azure-datorer

Den här sidan beskriver namngivnings konventionerna som används för virtuella Azure-datorer. Virtuella datorer använder dessa namn konventioner för att ange varierande funktioner och specifikationer.

## <a name="naming-convention-explanation"></a>Förklaring av namngivnings konvention

**[Familj]**  +  **[Under grupp *]**  +  **[antal virtuella processorer]**  +  **[Additiva funktioner]**  +  **[Accelerator-typ *]**  +  **[Version]**

|Värde | Förklaring|
|---|---|
| Standard, Basic eller experimentell | Standard är standardvärdet som tilldelas alla GA VM-storlekar | 
| Familj | Anger serien för VM-serien| 
| * Under serie | Används endast för specialiserade VM-differentieringar|
| antal virtuella processorer| Anger antalet virtuella processorer för den virtuella datorn |
| Additiva funktioner | En eller flera gemener anger additiva funktioner, till exempel: <br> a = AMD-baserad processor <br> d = disk (lokal temporär disk finns); Det här är för nya virtuella Azure-datorer, se [Ddv4 och Ddsv4-serien](./ddv4-ddsv4-series.md) <br> h = stöd för vilo läge <br> i = isolerat <br> l = ont om minne <br> m = minnes intensiv <br> t = litet minne <br> r = RDMA <br> s = Premium Storage möjligt, inklusive eventuell användning av [Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Obs! vissa nyare storlekar utan attributet för s kan fortfarande stödja Premium Storage, t. ex. M128, M64 osv.)<br> |
| * Typ av Accelerator | Anger typen av maskin varu Accelerator i specialiserade/GPU-SKU: er. Endast de nya specialiserade/GPU-SKU: er som lanserats från Q3 2020 kommer att ha maskin varu acceleratorn i namnet. |
| Version | Anger versionen för VM-serien |

## <a name="example-breakdown"></a>Exempel på detalj nivå

**[Familj]**  +  **[Under grupp *]**  +  **[antal virtuella processorer]**  +  **[Additiva funktioner]**  +  **[Accelerator-typ *]**  +  **[Version]**

### <a name="example-1-m416ms_v2"></a>Exempel 1: M416ms_v2

|Värde | Förklaring|
|---|---|
| Familj | M | 
| antal virtuella processorer | 416 |
| Additiva funktioner | m = minnes intensiv <br> s = Premium Storage kapabel |
| Version | v2 |

### <a name="example-2-nv16as_v4"></a>Exempel 2: NV16as_v4

|Värde | Förklaring|
|---|---|
| Familj | N | 
| Under grupp | V |
| antal virtuella processorer | 16 |
| Additiva funktioner | a = AMD-baserad processor <br> s = Premium Storage kapabel |
| Version | IPv4 |

### <a name="example-3-nc4as_t4_v3"></a>Exempel 3: NC4as_T4_v3

|Värde | Förklaring|
|---|---|
| Familj | N | 
| Under grupp | C |
| antal virtuella processorer | 4 |
| Additiva funktioner | a = AMD-baserad processor <br> s = Premium Storage kapabel |
| Accelerator-typ | T4 |
| Version | v3 |

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga [VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i Azure. 
