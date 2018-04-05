---
title: Beräkna benchmark resultat för virtuella Windows-datorer | Microsoft Docs
description: Jämföra SPECint beräkning benchmark resultat för virtuella Azure-datorer med Windows Server
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.openlocfilehash: cb77a058e72b8f08cb8974d5ec5088bc498e071d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Beräkna benchmark resultat för virtuella Windows-datorer
Följande SPECInt benchmark poäng visa compute prestanda för Azures högpresterande VM serie kör Windows Server. Beräkning benchmark resultat finns även [virtuella Linux-datorer](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
> [!NOTE]
> Linux-siffror nyligen har uppdaterats och innehåller en mer omfattande uppsättning virtuella datorer.

## <a name="a-series---compute-intensive"></a>A-series-beräkningsintensiva
| Storlek | vCPU:er | NUMA-noder | CPU | Körningar | Genomsnittlig grundavgift | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2-serien
| Storlek | vCPU:er | NUMA-noder | CPU | Körningar | Genomsnittlig grundavgift | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5 2673 v3 @ 2,4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5 2673 v3 @ 2,4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5 2673 v3 @ 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G-serien, GS-serien
| Storlek | vCPU:er | NUMA-noder | CPU | Körningar | Genomsnittlig grundavgift | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz-processor |22 |762.8 |3.7 |

## <a name="h-series"></a>H-serien
| Storlek | vCPU:er | NUMA-noder | CPU | Körningar | Genomsnittlig grundavgift  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5 2667 v3 @ 3,2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5 2667 v3 @ 3,2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |5 |573.2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5 2667 v3 @ 3,2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>Om SPECint
Windows-värdena är beräknade genom att köra [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) på Windows Server. SPECint kördes med alternativet grundavgift (resultaten från SPECint_rate2006) med en kopia per vCPU. SPECint består av 12 separat tester varje körs tre gånger, tar medianvärdet från varje test och viktning dem för att bilda en sammansatt poäng. Dessa tester körs sedan över flera virtuella datorer för att tillhandahålla de genomsnittliga resultat visas.

## <a name="next-steps"></a>Nästa steg
* Lagringskapacitet, diskinformation och ytterligare överväganden för att välja bland storlekar på VM finns [storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

