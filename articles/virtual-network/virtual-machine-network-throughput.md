---
title: "Dataflödet i nätverket virtuell Azure-dator | Microsoft Docs"
description: "Läs mer om virtuella Azure-datorn dataflödet i nätverket."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 536a779d7de51180aa6410911dea2b6c47780c2f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="virtual-machine-network-throughput"></a>Virtuella dataflödet i nätverket

Azure erbjuder en mängd olika storlekar för virtuella datorer och typer, var och en med en annan blandning av prestanda. Bland dessa funktioner för prestanda mäts nätverket genomströmning (eller bandbredd) i megabit per sekund (Mbps). Eftersom virtuella datorer finns på delade maskinvara, delas nätverkskapacitet ganska mellan de virtuella datorerna som delar samma maskinvara. Större virtuella datorer tilldelas relativt mer bandbredd än mindre virtuella datorer.
 
Den nätverksbandbredd som tilldelas varje virtuell dator förbrukade på utgående trafik (utgående) trafik från den virtuella datorn. All nätverkstrafik som lämnar den virtuella datorn räknas mot allokerade gränsen, oavsett mål. Till exempel gäller en virtuell dator har en 1 000 Mbit/s-begränsning, denna gräns om utgående trafik som är avsedda för en annan virtuell dator i samma virtuella nätverk eller utanför Azure.
 
Ingång är inte förbrukade eller begränsad direkt. Det finns dock andra faktorer, till exempel CPU och Lagringsgränser som kan påverka en virtuell dator möjligheten att ta emot inkommande data.

[Snabbare nätverk](virtual-network-create-vm-accelerated-networking.md) är en funktion som utformats för att förbättra nätverksprestanda, inklusive svarstid, genomflöde och CPU-användning. Medan snabbare nätverksfunktioner kan förbättra genomflöde för en virtuell dator, kan det göra det bara upp till den virtuella datorn allokerade bandbredd.
 
Virtuella Azure-datorer måste ha en men kan ha flera, nätverksgränssnitt som är kopplade till dem. Bandbredden som allokeras till en virtuell dator är summan av all utgående trafik på alla nätverksgränssnitt som är kopplad till en virtuell dator. Med andra ord är allokerad bandbredd per virtuell dator, oavsett hur många nätverkskort som är kopplade till den virtuella datorn. Information om hur många olika stöd för Azure VM-storlekar för nätverksgränssnitt finns Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. 

## <a name="expected-network-throughput"></a>Förväntade nätverksdataflöde

Förväntade utgående genomflödet och antalet nätverksgränssnitt som stöds av varje VM-storlek beskrivs i Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. Välj en typ, till exempel generella, och välj sedan en storlek serie på sidan resulterande, till exempel Dv2-serien. Varje serie har en tabell med nätverk specifikationer i den sista kolumnen med titeln, **Max nätverkskort / förväntat nätverksprestanda (Mbps)**. 

Genomströmning gränsen gäller för den virtuella datorn. Genomflöde påverkas inte av följande faktorer:
- **Antalet nätverksgränssnitt**: gränsen är kumulativa av all utgående trafik från den virtuella datorn.
- **Snabbare nätverk**: även om funktionen kan vara till hjälp för att uppnå publicerade gränsen, ändras inte gränsen.
- **Trafikmål**: alla mål räknas utgående.
- **Protokollet**: All utgående trafik över alla protokoll räknar mot gränsen.

## <a name="next-steps"></a>Nästa steg

- [Optimera dataflödet i nätverket för ett operativsystem för virtuell dator](virtual-network-optimize-network-bandwidth.md)
- [Testa nätverksdataflöde](virtual-network-bandwidth-testing.md) för en virtuell dator.