---
title: "Beräkna benchmark resultat för virtuella Linux-datorer | Microsoft Docs"
description: "Jämföra CoreMark beräkning benchmark resultat för virtuella Azure-datorer kör Linux"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cynthn;davberg
ms.openlocfilehash: 1d3db66c54769a3efc91a4e909d9c53fc2650743
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Beräkna benchmark resultat för virtuella Linux-datorer
Följande CoreMark benchmark poäng visa compute prestanda för Azures högpresterande VM serie kör Ubuntu. Beräkning benchmark resultat finns även [virtuella Windows-datorer](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="a-series---compute-intensive"></a>A-series-beräkningsintensiva
| Storlek | vCPU:er | NUMA-noder | Processor | Körningar | Iterationer per sekund | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |179 |110,294 |554 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |189 |210,816 |2,126 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |188 |110,025 |1,045 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |188 |210,727 |2,073 |

## <a name="dv2-series"></a>Dv2-serien
| Storlek | vCPU:er | NUMA-noder | Processor | Körningar | Iterationer per sekund | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |140 |14,852 |780 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |133 |29,467 |1,863 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |139 |56,205 |1,167 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |126 |108,543 |3,446 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5 2673 v3 @ 2,4 GHz |126 |205,332 |9,998 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |125 |28,598 |1,510 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |131 |55,673 |1,418 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |140 |107,986 |3,089 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5 2673 v3 @ 2,4 GHz |140 |208,186 |8,839 |
| Standard_D15_v2 |20 |2 |Intel Xeon E5 2673 v3 @ 2,4 GHz |28 |268,560 |4,667 |

## <a name="f-series"></a>F-serien
| Storlek | vCPU:er | NUMA-noder | Processor | Körningar | Iterationer per sekund | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |154 |15,602 |787 |
| Standard_F2 |2 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |126 |29,519 |1,233 |
| Standard_F4 |4 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |147 |58,709 |1,227 |
| Standard_F8 |8 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |224 |112,772 |3,006 |
| Standard_F16 |16 |2 |Intel Xeon E5 2673 v3 @ 2,4 GHz |42 |218,571 |5,113 |

## <a name="g-series"></a>G-serien
| Storlek | vCPU:er | NUMA-noder | Processor | Körningar | Iterationer per sekund | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |83 |31,310 |2,891 |
| Standard_G2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |84 |60,112 |3,537 |
| Standard_G3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |84 |107,522 |4,537 |
| Standard_G4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |83 |195,116 |5,024 |
| Standard_G5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |84 |360,329 |14,212 |

## <a name="gs-series"></a>GS-serien
| Storlek | vCPU:er | NUMA-noder | Processor | Körningar | Iterationer per sekund | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |84 |28,613 |1,884 |
| Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |83 |54,348 |3,474 |
| Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |83 |104,564 |1,834 |
| Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |84 |194,111 |4,735 |
| Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |84 |357,396 |16,228 |

## <a name="h-series"></a>H-serien
| Storlek | vCPU:er | NUMA-noder | Processor | Körningar | Iterationer per sekund | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5 2667 v3 @ 3,2 GHz |28 |140,782 |2,512 |
| Standard_H16 |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |35 |275,289 |7,110 |
| Standard_H18m |8 |1 |Intel Xeon E5 2667 v3 @ 3,2 GHz |28 |139,071 |3,988 |
| Standard_H16m |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |28 |275,988 |6,963 |
| Standard_H16r |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |28 |273,982 |6,069 |
| Standard_H16mr |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |28 |274,523 |5,698 |

## <a name="about-coremark"></a>Om CoreMark
Linux-värdena är beräknade genom att köra [CoreMark](http://www.eembc.org/coremark/faq.php) på Ubuntu. CoreMark har konfigurerats med antalet trådar som anger att antalet virtuella processorer och samtidighet värdet PThreads. Antal upprepningar mål justerades baserat på prestanda att tillhandahålla en körning av minst 20 sekunder (vanligtvis mycket längre). Den slutliga poängen representerar antalet upprepningar som slutförts dividerat med antalet sekunder som det tog för att utföra testet. Varje test kördes minst sju gånger på varje virtuell dator. Testar (förutom för H series_ kördes i oktober 2015 på flera virtuella datorer i varje offentliga Azure-region stöd för den virtuella datorn i den dag som kör.

## <a name="next-steps"></a>Nästa steg
* Lagringskapacitet, diskinformation och ytterligare överväganden för att välja bland storlekar på VM finns [storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Kör skripten CoreMark på virtuella Linux-datorer genom att ladda ned den [CoreMark skript pack](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

