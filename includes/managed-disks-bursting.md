---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 84736b7f1dcdf8b186fddbced5dd773e008c0dd2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887458"
---
Diskspricker stöds för premium-SSD-enheter. Sprängning stöds på alla premium SSD diskstorlekar <= 512 GiB (P20 eller lägre). Dessa diskstorlekar stöder sprängning på bästa sätt och använda ett kreditsystem för att hantera sprängning. Krediter ackumuleras i en burst-bucket när disktrafiken ligger under det etablerade prestandamålet för diskstorleken och förbrukar krediter när trafiken spricker utanför målet. Disktrafik spåras mot både IOPS och bandbredd i det etablerade målet. Disksprickor kommer inte att kringgå storleksbegränsningar för virtuella datorer (VM) för IOPS eller dataflöde.

Diskspricker är aktiverat som standard på nya distributioner av diskstorlekar som stöder den. Befintliga diskstorlekar, om de stöder disksprängning, kan aktivera bursting genom någon av följande metoder:

- Koppla loss och sätt tillbaka disken.
- Stoppa och starta den virtuella datorn.

## <a name="burst-states"></a>Burst-tillstånd

Alla burst tillämpliga diskstorlekar börjar med en fullständig burst kredit bucket när disken är ansluten till en virtuell dator. Den maximala varaktigheten för sprängning bestäms av storleken på burst kredit hink. Du kan bara samla oanvända krediter upp till storleken på kreditskopan. När som helst kan din disk burst kredit bucket vara i något av följande tre stater: 

- Periodisering, när disktrafiken använder mindre än det etablerade prestandamålet. Du kan samla kredit om disktrafiken ligger utanför IOPS eller bandbreddsmål eller båda. Du kan fortfarande samla IO-krediter när du förbrukar full diskbandbredd, vice versa.  

- Avböjer när disktrafiken använder mer än det etablerade prestandamålet. Burst-trafiken förbrukar oberoende krediter från IOPS eller bandbredd. 

- Återstående konstant, när disktrafiken exakt är på det etablerade prestandamålet. 

De diskstorlekar som ger bursting-stöd tillsammans med burst-specifikationerna sammanfattas i tabellen nedan.

## <a name="regional-availability"></a>Regional tillgänglighet

Diskspricker är tillgängligt i alla regioner i Det offentliga molnet.

## <a name="disk-sizes"></a>Diskstorlekar

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Exempelscenarier

För att ge dig en bättre uppfattning om hur detta fungerar, här är några exempel scenarier:

- Ett vanligt scenario som kan dra nytta av disksprängning är snabbare VM-start och programstart på OS-diskar. Ta en Virtuell Linux-dator med en 8 GiB OS-avbildning som exempel. Om vi använder en P2-disk som OS-disk är det etablerade målet 120 IOPS och 25 MiB. När den virtuella datorn startar kommer det att finnas en läspik till OS-disken som läser in startfilerna. Med införandet av sprängning kan du läsa med max burst hastighet 3500 IOPS och 170 MiB, vilket påskyndar laddningstiden med minst 6x. Efter vm-start är trafiknivån på OS-disken vanligtvis låg, eftersom de flesta dataåtgärder av programmet kommer att vara mot de anslutna datadiskarna. Om trafiken ligger under det etablerade målet kommer du att samla krediter.

- Om du är värd för en fjärrmiljö för virtuella skrivbord ökar lästrafiken till OS-disken avsevärt när en aktiv användare startar ett program som AutoCAD. I det här fallet kommer burst-trafik att förbruka ackumulerade krediter, så att du kan gå utöver det etablerade målet och starta programmet mycket snabbare.

- En P1-disk har ett etablerat mål på 120 IOPS och 25 MiB. Om den faktiska trafiken på disken var 100 IOPS och 20 MiB under det senaste 1 sekundsintervallet krediteras de oanvända 20 IOs och 5 MB till diskens burst-bucket. Krediter i burst-bucketen kan senare användas när trafiken överskrider det etablerade målet, upp till max burst-gränsen. Max burst-gränsen definierar taket för disktrafik även om du har burst-krediter att konsumera från. I det här fallet, även om du har 10.000 IOs i kredit bucket, en P1 disk kan inte utfärda mer än max burst av 3.500 IO per sekund.  
