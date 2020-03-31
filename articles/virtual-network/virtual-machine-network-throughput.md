---
title: Azure virtuell dator nätverksdataflöde | Microsoft-dokument
description: Lär dig mer om Azure-nätverksdataflöde för virtuella datorer.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245439"
---
# <a name="virtual-machine-network-bandwidth"></a>Bandbredd för virtuell datornätverk

Azure erbjuder en mängd olika vm-storlekar och typer, var och en med en annan blandning av prestandafunktioner. En funktion är nätverksdataflöde (eller bandbredd), mätt i megabit per sekund (Mbps). Eftersom virtuella datorer finns på delad maskinvara måste nätverkskapaciteten delas rättvist mellan de virtuella datorer som delar samma maskinvara. Större virtuella datorer allokeras relativt mer bandbredd än mindre virtuella datorer.
 
Nätverksbandbredden som allokeras till varje virtuell dator mäts på utgående (utgående) trafik från den virtuella datorn. All nätverkstrafik som lämnar den virtuella datorn räknas in i den allokerade gränsen, oavsett mål. Om en virtuell dator till exempel har en gräns på 1 000 Mbit/s gäller den gränsen om den utgående trafiken är avsedd för en annan virtuell dator i samma virtuella nätverk eller utanför Azure.
 
Inträngning mäts inte eller begränsas direkt. Det finns dock andra faktorer, till exempel CPU- och lagringsgränser, som kan påverka en virtuell dators förmåga att bearbeta inkommande data.

Accelererat nätverk är en funktion som är utformad för att förbättra nätverkets prestanda, inklusive svarstid, dataflöde och CPU-användning. Även om accelererade nätverk kan förbättra en virtuell dators dataflöde, kan den bara göra det upp till den virtuella datorns allokerade bandbredd. Mer information om Accelererade nätverk finns i Accelererade nätverk för [virtuella Datorer](create-vm-accelerated-networking-powershell.md) i Windows eller [Linux.](create-vm-accelerated-networking-cli.md)
 
Virtuella Azure-datorer måste ha en, men kan ha flera nätverksgränssnitt kopplade till dem. Bandbredd som allokeras till en virtuell dator är summan av all utgående trafik i alla nätverksgränssnitt som är kopplade till en virtuell dator. Med andra ord är den allokerade bandbredden per virtuell dator, oavsett hur många nätverksgränssnitt som är kopplade till den virtuella datorn. Mer information om hur många nätverksgränssnitt som olika Azure VM-storlekar stöder finns i Storlekarna för Virtuella [Azure-](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux-datorer.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

## <a name="expected-network-throughput"></a>Förväntat nätverksflöde

Förväntat utgående dataflöde och antalet nätverksgränssnitt som stöds av varje vm-storlek beskrivs i Azure [Windows-](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. Välj en typ, till exempel Allmänt ändamål, välj sedan en storleksserie på den resulterande sidan, till exempel Dv2-serien. Varje serie har en tabell med nätverksspecifikationer i den sista kolumnen med titeln **Max NÄTVERKSKORT / Förväntad nätverksprestanda (Mbps).** 

Gränsen för dataflöde gäller för den virtuella datorn. Dataflödet påverkas inte av följande faktorer:
- **Antal nätverksgränssnitt: Bandbreddsgränsen**är kumulativ för all utgående trafik från den virtuella datorn.
- **Accelererade nätverk:** Även om funktionen kan vara till hjälp för att uppnå den publicerade gränsen, ändrar det inte gränsen.
- **Trafikdestination**: Alla destinationer räknas in mot den utgående gränsen.
- **Protokoll**: All utgående trafik över alla protokoll räknas mot gränsen.

## <a name="network-flow-limits"></a>Begränsningar för nätverksflöde

Förutom bandbredd kan antalet nätverksanslutningar som finns på en virtuell dator vid en given tidpunkt påverka dess nätverksprestanda. Azure-nätverksstacken upprätthåller tillståndet för varje riktning för en TCP/UDP-anslutning i datastrukturer som kallas "flöden". En typisk TCP/UDP-anslutning kommer att ha 2 flöden skapade, en för inkommande och en annan för utgående riktning. 

Dataöverföring mellan slutpunkter kräver att flera flöden skapas utöver de som utför dataöverföringen. Några exempel är flöden som skapats för DNS-upplösning och flöden som skapats för belastningsutjämnare hälsoavsökningar. Observera också att virtuella nätverksenheter (NVAs) såsom gateways, proxyservrar, brandväggar, kommer att se flöden skapas för anslutningar som avslutas vid installationen och har sitt ursprung i installationen. 

![Flödesantal för TCP-konversation via en vidarebefordringsapparat](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Flödesgränser och rekommendationer

Idag stöder Azure-nätverksstacken 250 000 nätverksflöden med bra prestanda för virtuella datorer med större än 8 CPU-kärnor och 100 k totala flöden med bra prestanda för virtuella datorer med färre än 8 CPU-kärnor. Förbi den här gränsen nätverksprestanda försämras graciöst för ytterligare flöden upp till en hård gräns på 500K totala flöden, 250K inkommande och 250K utgående, varefter ytterligare flöden släpps.

||Virtuella datorer med <8 CPU-kärnor|Virtuella datorer med 8+ CPU-kärnor|
|---|---|---|
|<b>Bra prestanda</b>|100k flöden |250k flöden|
|<b>Försämrad prestanda</b>|Över 100k flöden|Över 250k flöden|
|<b>Flödesgräns</b>|500k flöden|500k flöden|

Mått är tillgängliga i [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) för att spåra antalet nätverksflöden och flödesgenereringshastigheten på din virtuella eller VMSS-instanser.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Anslutningsetablering och avslutningshastigheter kan också påverka nätverksprestanda som anslutningsetablering och avslutningsresurser CPU med paketbearbetningsrutiner. Vi rekommenderar att du jämför arbetsbelastningar mot förväntade trafikmönster och skalar ut arbetsbelastningar på lämpligt sätt för att matcha dina prestandabehov. 

## <a name="next-steps"></a>Nästa steg

- [Optimera nätverkets dataflöde för en virtuell dators operativsystem](virtual-network-optimize-network-bandwidth.md)
- [Testa nätverksdataflödet](virtual-network-bandwidth-testing.md) för en virtuell dator.
