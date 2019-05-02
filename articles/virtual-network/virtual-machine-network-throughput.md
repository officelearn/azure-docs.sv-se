---
title: Nätverksdataflöde för virtuella Azure-datorer | Microsoft Docs
description: Läs mer om Azure-dator nätverkets genomflöde.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: kumud
ms.openlocfilehash: 182b3b7dad828e67d006391e00986406729c959d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689259"
---
# <a name="virtual-machine-network-bandwidth"></a>Nätverksbandbredd för virtuell dator

Azure erbjuder en mängd olika storlekar på Virtuella datorer och typer, var och en med en blandning av prestandafunktioner. En funktion är nätverket dataflöde (eller bandbredd), mätt i megabit per sekund (Mbps). Eftersom virtuella datorer finns på delad maskinvara, måste nätverkskapacitet delas ganska av de virtuella datorer som delar samma maskinvara. Större virtuella datorer allokeras relativt mer bandbredd än mindre virtuella datorer.
 
Den nätverksbandbredd som allokeras till varje virtuell dator är debiteras baserat på utgående (utgående) trafik från den virtuella datorn. All nätverkstrafik som lämnar den virtuella datorn räknas mot den allokerade begränsning, oavsett mål. Till exempel gäller en virtuell dator har en gräns för 1 000 Mbit/s, denna gräns om utgående trafik är avsedd för en annan virtuell dator i samma virtuella nätverk eller utanför Azure.
 
Inkommande mäts inte eller begränsad direkt. Det finns dock andra faktorer, till exempel processor- och gränser som kan påverka en virtuell dator möjligheten att ta emot inkommande data.

Snabbare nätverksfunktioner är en funktion som utformats för att förbättra nätverksprestanda, inklusive svarstid, dataflöde och CPU-användning. Medan accelererat nätverk kan förbättra dataflödet för en virtuell dator, kan det göra det bara upp till den virtuella datorn allokerade bandbredd. Läs mer om Accelererat nätverk i Accelererat nätverk för [Windows](create-vm-accelerated-networking-powershell.md) eller [Linux](create-vm-accelerated-networking-cli.md) virtuella datorer.
 
Azure-datorer måste ha en, men kan ha flera, nätverksgränssnitt som är anslutna till dem. Bandbredden som allokeras till en virtuell dator är summan av all utgående trafik för alla nätverksgränssnitt som är kopplade till en virtuell dator. Med andra ord är tilldelad bandbredd per virtuell dator, oavsett hur många nätverksgränssnitt är kopplade till den virtuella datorn. Läs hur många nätverksgränssnitt olika stöd för Azure VM-storlekar i Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. 

## <a name="expected-network-throughput"></a>Förväntade nätverkets dataflöde

Förväntade utgående dataflöde och antalet nätverksgränssnitt som stöds av varje VM-storlek beskrivs i Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. Välj en typ, till exempel generell användning, och välj sedan en storlek-serien på den resulterande sidan, t.ex Dv2-serien. Varje serie har en tabell med nätverk specifikationer i den sista kolumnen med rubriken **maximalt antal nätverkskort / förväntade nätverksprestanda (Mbit/s)**. 

Dataflöde gränsen gäller för den virtuella datorn. Dataflödet påverkas inte av följande faktorer:
- **Antalet nätverksgränssnitt**: Bandbreddsbegränsningen beräknas kumulativt för all utgående trafik från den virtuella datorn.
- **Accelerated networking**: Även om funktionen kan vara användbart för att uppnå den publicerade gränsen, ändras inte gränsen.
- **Trafikmål**: Alla mål räknas utgående.
- **Protokoll**: All utgående trafik över alla protokoll räknas mot gränsen.

## <a name="next-steps"></a>Nästa steg

- [Optimera nätverkets dataflöde för en virtuell dators operativsystem](virtual-network-optimize-network-bandwidth.md)
- [Testa nätverkets genomflöde](virtual-network-bandwidth-testing.md) för en virtuell dator.
