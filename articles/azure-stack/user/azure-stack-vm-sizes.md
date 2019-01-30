---
title: Storlekar för virtuella datorer stöds i Azure Stack | Microsoft Docs
description: Referens för storlekarna som stöds i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 6f3b4632674217133f4af4c95143d2df51ecde6a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247467"
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Storlekar för virtuella datorer stöds i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Den här artikeln innehåller de storlekar för virtuella datorer (VM) som är tillgängliga i Azure Stack.

Disken IOPS (antal Input/Output åtgärder Per sekund) på Azure Stack är en funktion av VM-storlek i stället för typ av disk. Det innebär att IOPS-gränsen för en enda ytterligare datadisk är 2300 IOPS för en serie Standard_Fs VM, oavsett om du väljer SSD och HDD för typ av disk. IOPS-begränsningar finns ett tak (högsta möjliga) för att förhindra bort störande grannar. Det är inte en garanti för IOPS som uppstår på en specifik VM-storlek.

## <a name="general-purpose"></a>Generellt syfte

Allmänna storlekar för Virtuella datorer ger ett balanserat förhållande för CPU och minne. De används för testning och utveckling, små till mellanstora databaser och låg till medelhög trafik webbservrar. Varje datadisk är 2300 IOPS för premium VM-storlekar, förutom grundläggande A-serien. För grundläggande A är datadiskstorleken 500 IOPS.

### <a name="basic-a"></a>Basic A

> [!NOTE]
> *Basic A* storlekar för virtuella datorer är inte längre tillgängligt för [skapar VM-skalningsuppsättningar](../azure-stack-compute-add-scalesets.md) (VMSS) via portalen. Använd PowerShell eller en mall för att skapa en VMSS med den här storleken.

|Storlek – storlek\namn |Virtuell processor     |Minne | Högsta temporär diskstorlek | Max OS diskdataflöde: (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt diskgenomflöde (IOPS) | Maximalt antal nätverkskort |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1 x 300  |1   |
|**A1\Basic_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2 x 300  |1   |
|**A2\Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4 x 300  |1   |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8 x 300  |1   |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16 X 300 |1   |

### <a name="standard-a"></a>Standard A 
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0.768 |20  |500 |500 |1 × 500  |1 |
|**Standard_A1** |1 |1.75  |70  |500 |500 |2 × 500  |1 |
|**Standard_A2** |2 |3.5   |135 |500 |500 |4 × 500  |1 |
|**Standard_A3** |4 |7     |285 |500 |500 |8 × 500  |2 |
|**Standard_A4** |8 |14    |605 |500 |500 |16 × 500 |4 |
|**Standard_A5** |2 |14    |135 |500 |500 |4 × 500  |2 |
|**Standard_A6** |4 |28    |285 |500 |500 |8 × 500  |2 |
|**Standard_A7** |8 |56    |605 |500 |500 |16 × 500 |4 |

### <a name="av2-series"></a>Av2-serien
*Kräver Azure Stack-version 1804 eller senare*

|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1000  |2 / 2 x 500   |2 |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4 x 500   |2 |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8 x 500   |4 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16 x 500 |8 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4 x 500   |2 |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8 x 500   |4 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16 x 500 |8 |

### <a name="d-series"></a>D-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3.5 |50   |500 |3000  |4 / 4 x 500   |1 |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 |


### <a name="ds-series"></a>DS-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3.5 |7    |1000 |4000  |4 / 4 x 2300   |1 |
|**Standard_DS2** |2   |7   |14   |1000 |8000  |8 / 8 x 2300   |2 |
|**Standard_DS3** |4   |14  |28   |1000 |16000 |16 / 16 x 2300 |4 |
|**Standard_DS4** |8   |28  |56   |1000 |32000 |32 / 32 x 2300 |8 |

### <a name="dv2-series"></a>Dv2-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4 / 4 x 500   |1 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64 x 500 |8 |

### <a name="dsv2-series"></a>DSv2-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4 / 4 x 2300   |1 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8 x 2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16 x 2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32 x 2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64 x 2300 |8 |


## <a name="compute-optimized"></a>Beräkningsoptimerad
### <a name="f-series"></a>F-serien
*Kräver Azure Stack-version 1804 eller senare*

|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4 x 500   |2 |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8 x 500   |2 |
|**Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16 x 500 |4 |
|**Standard_F8**  |8   |16  |128  |500 |24000 |32 / 32 x 500 |8 |
|**Standard_F16** |16  |32  |256  |500 |48000 |64 / 64 x 500 |8 |


### <a name="fs-series"></a>Fs-serien
*Kräver Azure Stack-version 1804 eller senare*  

|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1000 |4000  |4 / 4 x 2300   |2 |
|**Standard_F2s**  |2   |4   |8   |1000 |8000  |8 / 8 x 2300   |2 |
|**Standard_F4s**  |4   |8   |16  |1000 |16000 |16 / 16 x 2300 |4 |
|**Standard_F8s**  |8   |16  |32  |1000 |32000 |32 / 32 x 2300 |8 |
|**Standard_F16s** |16  |32  |64  |1000 |64000 |64 / 64 x 2300 |8 |


### <a name="fsv2-series"></a>Fsv2-serien
*Kräver Azure Stack-version 1804 eller senare* 

|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_F2s_v2**  |2   |4   |16   |1000 |4000   |4 / 4 x 2300    |
|**Standard_F4s_v2**  |4   |8   |32   |1000 |8000   |8 / 8 x 2300    |
|**Standard_F8s_v2**  |8   |16  |64   |1000 |16000  |16 / 16 x 2300  |
|**Standard_F16s_v2** |16  |32  |128  |1000 |32000  |32 / 32 x 2300  |
|**Standard_F32s_v2** |32  |64  |256  |1000 |64000  |32 / 32 x 2300  |
|**Standard_F64s_v2** |64  |128 |512  |1000 |128000 |32 / 32 x 2300  |


## <a name="memory-optimized"></a>Minnesoptimerad

Minne som är optimerad storlekar för Virtuella datorer ger högt minne att CPU-förhållande som har utformats för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.

### <a name="mo-d"></a>D-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8 x 500   |2 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16 x 500 |4 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32 x 500 |8 |
|**Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64 x 500 |8 |

### <a name="mo-ds"></a>DS-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1000    |8000  |8 / 8 x 2300   |2 |
|**Standard_DS12**  |4  |28  |56  |1000    |12000 |16 / 16 x 2300 |4 |
|**Standard_DS13**  |8  |56  |112 |1000    |32000 |32 / 32 x 2300 |8 |
|**Standard_DS14**  |16 |112 |224 |1000    |64000 |64 / 64 x 2300 |8 |

### <a name="mo-dv2"></a>Dv2-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16 x 500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32 x 500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64 x 500  |8 |


### <a name="mo-dsv2"></a>DSv2-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Temporär lagring (GiB)  | Max OS diskgenomflödet (IOPS) | Maximalt genomflöde för temporär lagring (IOPS) | Maximalt antal datadiskar / dataflöde (IOPS) | Maximalt antal nätverkskort |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |8 / 8 x 2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |16 / 16 x 2300  |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |32 / 32 x 2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |64 / 64 x 2300  |8 |


## <a name="next-steps"></a>Nästa steg

[Överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md)
