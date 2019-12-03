---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7564d40aa9344288c0368818b0b6501dc22a5a27
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705510"
---
Disk-burst är för närvarande en förhands gransknings funktion för Premium-SSD. Burst-överföring stöds på Premium SSD-disk storlekar < = 512 GiB (P20 eller lägre). Dessa disk storlekar stöder burst-överföring på bästa möjliga sätt och använder ett kredit system för att hantera burst-överföring. Krediterna ackumuleras i en burst-bucke när disk trafik unders tiger det tillhandahållna prestanda målet för disk storleken, och använder krediterna när trafiken överförs bortom målet. Disk trafiken spåras mot både IOPS och bandbredden i det etablerade målet.

Disk-burst är aktiverat som standard på nya distributioner av disk storlekarna som stöder det. Befintliga disk storlekar, om de har stöd för disk burst, kan aktivera burst via någon av följande metoder:

- Koppla från och återanslut disken.
- Stoppa och starta den virtuella datorn.

## <a name="burst-states"></a>Burst-tillstånd

Alla burst-tillämpliga disk storlekar börjar med en full burst-kredit Bucket när disken är ansluten till en virtuell dator. Max längden för burst-överföring bestäms av storleken på burst-huvudbucket. Du kan bara ackumulera oanvända krediter upp till storleken på kredit Bucket. När som helst kan din disk burst-kredit Bucket vara i något av följande tre tillstånd: 

- Påförs när disk trafiken använder mindre än det tillhandahållna prestanda målet. Du kan ackumulera kredit om disk trafiken är bortom IOPS eller bandbredds mål eller både och. Du kan fortfarande ackumulera IO-krediter när du förbrukar full disk bandbredd, vice versa.  

- Avböja, när disk trafiken använder mer än det tillhandahållna prestanda målet. Burst-trafiken förbrukar oberoende krediter från IOPS eller bandbredd. 

- Återstående konstant, när disk trafiken är exakt vid det tillhandahållna prestanda målet. 

Disk storlekarna som ger stöd för burst-överföring tillsammans med burst-specifikationer sammanfattas i tabellen nedan.

## <a name="regional-availability"></a>Regional tillgänglighet

För närvarande är disk burst endast tillgängligt i regionen västra centrala USA.

## <a name="disk-sizes"></a>Diskstorlekar

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Exempelscenarier

Här följer några exempel scenarier för att ge dig en bättre uppfattning om hur detta fungerar:

- Ett vanligt scenario som kan dra nytta av disk burst är snabbare start av virtuella datorer och program start på OS-diskar. Ta en virtuell Linux-dator med en 8 GiB OS-avbildning som exempel. Om vi använder en P2-disk som OS-disk är det etablerade målet 120 IOPS och 25 Mbit/s. När den virtuella datorn startar kommer det att finnas en Läs insamling av OS-disken som laddar upp startfilerna. Med introduktionen av bursting kan du läsa den högsta Burst-hastigheten på 3500 IOPS och 170 Mbit/s, vilket påskyndar inläsnings tiden med minst 6x. Efter VM-starten är trafik nivån på OS-disken oftast låg eftersom de flesta data åtgärder av programmet kommer att vara mot anslutna data diskar. Om trafiken är under det etablerade målet ackumuleras krediten.

- Om du är värd för en fjärran sluten virtuell dator miljö, ökar, när en aktiv användare startar ett program som AutoCAD, att Läs trafik till OS-disken ökar markant. I det här fallet kommer burst-trafik att förbruka ackumulerade krediter, vilket gör att du kan gå utöver det etablerade målet och starta programmet mycket snabbare.

- En P1-disk har ett allokerat mål på 120 IOPS och 25 MBps. Om den faktiska trafiken på disken var 100 IOPS och 20 Mbit/s under de senaste 1 sekunderna, krediteras den oanvända 20 IOs-enheten och 5 MB till diskens burst-Bucket. Krediter i burst-Bucket kan senare användas när trafiken överskrider det etablerade målet, upp till den maximala burst-gränsen. Max gränsen för burst definierar taket för disk trafik även om du har burst-krediter att använda. I detta fall, även om du har 10 000 IOs i kredit Bucket, kan en P1-disk inte utfärda mer än max burst på 3 500 IO per sekund.  
