---
title: Beräkning för kapacitetsplanering för Azure Stack | Microsoft Docs
description: Läs mer om compute kapacitetsplanering för Azure Stack-distributioner.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 09/18/2018
ms.custom: mvc
ms.openlocfilehash: ca0a6569dda89586c629cc46909862de4f27cf04
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56160919"
---
# <a name="azure-stack-compute-capacity-planning"></a>Azure Stack compute-kapacitetsplanering
Den [VM-storlekar som stöds på Azure Stack](./user/azure-stack-vm-sizes.md) är en delmängd av de som stöds på Azure. Azure tillämpar resursbegränsningar längs många vektorer att undvika överförbrukning av resurser (server lokalt och tjänstnivå). Klient-upplevelser blir lidande när andra klienter overconsume resurser utan att införa vissa begränsningar på klientförbrukningen. Det finns bandbredd caps på plats på Azure Stack som matchar Azure begränsningar för nätverk utgående data från den virtuella datorn. För lagringsresurser, har IOPs Lagringsgränser implementerats på Azure Stack för att undvika grundläggande överförbrukning av resurser av klienter för lagringsåtkomst.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Placering av virtuella datorer och skaffa stora resurser till fysisk kärna
I Azure Stack finns det inget sätt för en klient att ange en specifik server ska användas för placering av virtuella datorer. Den enda faktor vid placerar VM: ar är om det finns tillräckligt med minne på värden för den typ av virtuell dator. Azure Stack överanstränga inte minne. men tillåts en overcommit av antalet kärnor. Eftersom placering algoritmer inte kan hitta på den befintliga virtuella till fysiska kärnor överetablering förhållande som en faktor, kan varje värd ha ett annat förhållande. 

I Azure, för att uppnå hög tillgänglighet i ett system för produktion av flera virtuella datorer, virtuella datorer är placerade i en tillgänglighetsuppsättning sprids via flera feldomäner. I Azure Stack definieras en feldomän i en tillgänglighetsuppsättning som en nod i skalningsenheten.

Infrastrukturen i Azure Stack är elastisk vid fel, medför den underliggande tekniken (redundanskluster) fortfarande vissa avbrott för virtuella datorer på en fysisk server som påverkas i händelse av ett maskinvarufel. För närvarande stöder Azure Stack med en tillgänglighetsuppsättning med högst tre feldomäner för att överensstämma med Azure. Virtuella datorer placeras i en tillgänglighetsuppsättning kommer att fysiskt isolerade från varandra genom att sprida dem så jämnt som möjligt via flera feldomäner (Azure Stack-noder). Om det finns ett maskinvarufel, kommer virtuella datorer från den misslyckade feldomänen att startas om på andra noder, men om möjligt sparas i separata feldomäner från andra virtuella datorer i samma tillgänglighetsuppsättning. När maskinvaran som är online igen, kommer du genomförs virtuella datorer för att upprätthålla hög tillgänglighet.

En annan begrepp som används av Azure för att ge hög tillgänglighet är i form av uppdateringsdomäner i tillgänglighetsuppsättningar. En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas eller startas om samtidigt. I Azure Stack, virtuella datorer är live migreras på andra online värdar i klustret innan deras underliggande värden har uppdaterats. Eftersom det inte finns inget klient-avbrott under en värduppdatering av, finns bara funktionen domän i Azure Stack mall kompatibilitet med Azure.

## <a name="azure-stack-resiliency-resources"></a>Azure Stack återhämtning-resurser
För korrigeringar och uppdateringar av en Azure Stack-integrerat system och för att vara motståndskraftiga mot fysiska maskinvarufel, är en del av det totala antalet minnet reserveras och är inte tillgänglig för placering av virtuella datorer (VM) för klientorganisationen.

Om en server inte startas virtuella datorer som finns på servern som inte på återstående, tillgängliga servrar som tillhandahåller för VM-tillgänglighet. På samma sätt under korrigeringar och uppdateringar, kommer alla virtuella datorer som körs på en server levande migrerade till andra tillgängliga, servern. Den här hanteringen av virtuella datorer eller en flytt kan bara ske om det är reserverad kapacitet så att starta om eller migrering ska ske.

Följande beräkning resulterar i totalt, tillgängligt RAM-minne som kan användas för placering av virtuella datorer för innehavare. Den här minneskapacitet är för hela Skalningsenheten för Azure Stack.

  Tillgängligt minne för placering av virtuella datorer = totalt Server – Reservresurs återhämtning – Azure Stack-infrastruktur minnesanvändningen <sup>1</sup>

  Återhämtning reservera = H + R * (N-1) + V * (N-2)

> Där:
> - H = storleken på enskild serverminne
> - N = storlek för Skalningsenheten (antal servrar)
> - R = operativsystemet reserverad för OS-omkostnader<sup>2</sup>
> - V = största virtuella datorn i skalningsenheten

  <sup>1</sup> azure Stack-infrastruktur Overhead = 208 GB

  <sup>2</sup> operativsystemet reserverad för omkostnader = 15% av nod minne. Reservvärdet operativsystemet är en uppskattning och varierar beroende på kapaciteten fysiskt minne på servern och allmän operativsystemet kräver.

Värdet V största virtuella datorn i skalningsenheten, baserat dynamiskt på största minnesstorlek för VM-klienten. Det största värdet för virtuell dator kan exempelvis vara 7 GB eller 112 GB eller några andra stöds VM minnesstorleken i Azure Stack-lösning.

Beräkningen ovan är en uppskattning och komma att ändras baserat på den aktuella versionen av Azure Stack. Möjligheten att distribuera virtuella klientdatorer och tjänster baseras på ärendets natur den distribuerade lösningen. Det här exemplet beräkningen är bara en guide och inte absolut svaret på möjligheten att distribuera virtuella datorer.



## <a name="next-steps"></a>Nästa steg
[Kapacitetsplanering för lagring](capacity-planning-storage.md)
