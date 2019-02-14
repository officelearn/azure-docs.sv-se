---
title: Konfigurera Geo-replikering för Azure Cache för Redis | Microsoft Docs
description: Lär dig mer om att replikera din Azure-Cache för Redis-instanser i olika geografiska regioner.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: yegu
ms.openlocfilehash: e5e60e3370cc813685403cc979e6ef8dc043b7ac
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233276"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Konfigurera Geo-replikering för Azure Cache för Redis

GEO-replikering är en mekanism för att länka två Premium-nivån Azure Cache för Redis-instanser. En cache anges den primära länkade cachen och den andra som sekundär länkat cacheminne. Sekundär länkade cacheminnet blir skrivskyddade och data som skrivs till den primära cachen replikeras till det sekundära länkat cacheminnet. Den här funktionen kan användas för att replikera en cache i Azure-regioner. Den här artikeln beskrivs hur du konfigurerar Geo-replikering för din Premium-nivån Azure Cache för Redis-instanser.

## <a name="geo-replication-prerequisites"></a>Krav för GEO-replikering

Om du vill konfigurera Geo-replikering mellan två cacheminnen, måste följande krav uppfyllas:

- Båda cacheminnen måste vara [premiumnivån](cache-premium-tier-intro.md) cachelagrar.
- Båda cacheminnen måste finnas i samma Azure-prenumeration.
- Den länkade sekundär cachen måste vara samma prisnivå eller en större prisnivå än den primära länkade cachen.
- Om den primära länkade cachen har aktiverad klustring, måste sekundära länkat cacheminne ha-klustring aktiverat med samma antal shards som den primära länkade cachen.
- Båda cacheminnen måste skapas och körs.
- Persistence måste inte aktiveras på antingen cache.
- GEO-replikering mellan cacheminnen i samma virtuella nätverk stöds. 
- GEO-replikering mellan cacheminnen i peerkopplade virtuella nätverk inom samma region är för närvarande en förhandsversion av funktionen. De två virtuella nätverken måste konfigureras så att resurser i de virtuella nätverken är kan nå varandra via TCP-anslutningar.
- GEO-replikering mellan cacheminnen i peerkopplade virtuella nätverk i olika regioner stöds inte ännu, men kommer snart att vara i en förhandsversion.

När Geo-replikering har konfigurerats kan gäller följande begränsningar för din länkade cache-par:

- Sekundär länkade cacheminnet är skrivskyddat. Du kan läsa från den, men du kan inte skriva data till den. 
- Alla data som fanns i det sekundära länkat cacheminnet innan länken har lagts till tas bort. Om Geo-replikering tas därefter bort men förblir replikerade data i länkade Sekundär cache.
- Du kan inte initiera en [skalning åtgärden](cache-how-to-scale.md) på antingen cachen eller [ändra antalet shards](cache-how-to-premium-clustering.md) om cacheminnet innehåller aktiverad klustring.
- Du kan inte aktivera persistence på antingen cache.
- Du kan använda [exportera](cache-how-to-import-export-data.md#export) med antingen cache, men du kan bara [Import](cache-how-to-import-export-data.md#import) till den primära länkade cachen.
- Du kan inte ta bort länkat cacheminne eller resursgruppen som innehåller dem, tills du tar bort länken Geo-replikering. Mer information finns i [varför åtgärden misslyckas när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Om det finns två cacheminnen i olika regioner, gäller kostnader för nätverksegress för data som replikeras över regioner för att det sekundära länkat cacheminnet. Mer information finns i [hur mycket kostar det för att replikera data mellan Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Det finns ingen automatisk redundans till den sekundära länkade cachen om den primära cachen (och dess replik) går. Du skulle behöva manuellt ta bort länken Geo-replikering och peka klientprogrammen cacheminne som tidigare var det sekundära länkade cacheminnet i ordning för redundans-klientprogram. Mer information finns i [hur fungerar växling till det sekundära länkat cacheminnet?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Lägg till en länk för Geo-replikering

1. Om du vill länka ihop två premium-cacheminnen för geo-replikering, klickar du på **georeplikering** cachelagra cache avsett för den primära länkad resurs-menyn och klicka sedan på **Lägg till cachereplikeringslänk** från den **Geo-replikering** bladet.

    ![Lägg till länk](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicka på namnet på den önskade sekundär cachen från den **kompatibla cacheminnen** lista. Om din önskade cache inte visas i listan kan du kontrollera att den [Geo-replikering krav](#geo-replication-prerequisites) för önskad sekundära cacheminnet är uppfyllda. Om du vill filtrera cacheminnen efter region, klickar du på önskad region på kartan för att visa endast dessa cacheminnen i den **kompatibla cacheminnen** lista.

    ![Kompatibla cacheminnen för GEO-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Du kan också initiera länkningen eller visa information om den sekundära cachen genom att använda snabbmenyn.

    ![Snabbmenyn för GEO-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicka på **länk** kopplar ihop de två cacheminnen och börja replikering.

    ![Länka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Du kan visa förloppet för replikeringen på den **Geo-replikering** bladet.

    ![Status för länkramverk](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Du kan också visa länkande status på den **översikt** bladet för både de primära och sekundära cacheminnen.

    ![Status för cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    När replikeringen är klar, den **länka status** ändras till **lyckades**.

    ![Status för cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Den primära länkade cachen fortfarande tillgänglig för användning under länkningen, men sekundära länkat cacheminne är inte tillgängligt förrän länkningen har slutförts.

## <a name="remove-a-geo-replication-link"></a>Ta bort en länk för Geo-replikering

1. Om du vill ta bort länken mellan två cacheminnen och stoppa Geo-replikering, klickar du på **Avlänka cacheminnen** från den **Geo-replikering** bladet.
    
    ![Avlänka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    När avlänkar processen är klar, är sekundär cache tillgänglig för både läsningar och skrivningar.

>[!NOTE]
>När Geo-replikeringslänken tas bort, förblir replikerade data från den primära länkade cachen i sekundär cache.
>
>

## <a name="geo-replication-faq"></a>Vanliga frågor och svar GEO-replikering

- [Kan jag använda Geo-replikering med en Standard- eller Basic-nivån cache?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Är Mina cache tillgänglig för användning under länkande eller avlänkar?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan jag koppla fler än två cacheminnen tillsammans?](#can-i-link-more-than-two-caches-together)
- [Kan jag koppla två cacheminnen från olika Azure-prenumerationer?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan jag koppla två cacheminnen med olika storlekar?](#can-i-link-two-caches-with-different-sizes)
- [Kan jag använda Geo-replikering med aktiverad klustring?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan jag använda Geo-replikering med min cacheminnen i ett virtuellt nätverk?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Vad är replikeringsschemat för Redis geo-replikering?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hur lång tid tar geo-replikering replikering?](#how-long-does-geo-replication-replication-take)
- [Garanteras återställningspunkt replikering?](#is-the-replication-recovery-point-guaranteed)
- [Kan jag använda PowerShell eller Azure CLI för att hantera Geo-replikering?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Hur mycket kostar det för att replikera data i Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Varför åtgärden misslyckas när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Vilken region ska jag använda för min sekundära länkat cacheminne?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hur fungerar växling till det sekundära länkat cacheminnet?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan jag använda Geo-replikering med en Standard- eller Basic-nivån cache?

Nej, Geo-replikering är bara tillgängligt för Premium-nivån cacheminnen.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Är Mina cache tillgänglig för användning under länkande eller avlänkar?

- När du länkar två cacheminnen tillsammans för Geo-replikering, den primära länkade cachen fortfarande tillgänglig för användning men det sekundära länkat cacheminnet är inte tillgängligt förrän länkningen har slutförts.
- När du tar bort länken Geo-replikering mellan två cacheminnen, är både cacheminnen tillgängliga för användning.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan jag koppla fler än två cacheminnen tillsammans?

Nej, när med Geo-replikering kan du bara koppla två cacheminnen tillsammans.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan jag koppla två cacheminnen från olika Azure-prenumerationer?

Nej, båda cacheminnen måste finnas i samma Azure-prenumeration.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan jag koppla två cacheminnen med olika storlekar?

Ja, så länge som det sekundära länkat cacheminnet är större än den primära länkade cachen.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan jag använda Geo-replikering med aktiverad klustring?

Ja, så länge båda cacheminnen har samma antal shards.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan jag använda Geo-replikering med min cacheminnen i ett virtuellt nätverk?

Ja, Geo-replikering av cacheminnen i virtuella nätverk stöds. 

- GEO-replikering mellan cacheminnen i samma virtuella nätverk stöds.
- GEO-replikering mellan cache i olika virtuella nätverk stöds också, förutsatt att de två virtuella nätverken är konfigurerade så att resurser i de virtuella nätverken är kan nå varandra via TCP-anslutningar.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Vad är replikeringsschemat för Redis geo-replikering?

Replikeringen sker inte på ett visst schema är kontinuerliga och asynkrona dvs alla skrivningar till primärt klar replikeras omedelbart asynkront på sekundärt.

### <a name="how-long-does-geo-replication-replication-take"></a>Hur lång tid tar geo-replikering replikering?

Replikering är inkrementell, asynkrona och kontinuerlig och den tid det tar är vanligtvis inte mycket skiljer sig från svarstiden i flera regioner. I vissa fall kan kanske vid vissa tidpunkter, sekundärt måste göra en fullständig synkronisering av data från primärt. Tid i det här fallet är beroende av flera faktorer som: belastningen på den primära cachen bandbredd som är tillgänglig på cache-datorn inter region svarstid osv. Till exempel baserat på vissa tester som vi har hittat ut den replikeringstid för en fullständig 53 GB geo-replikerade koppla i östra USA och västra USA-regioner kan finnas var som helst mellan 5-10 minuter.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garanteras återställningspunkt replikering?

För närvarande för cacheminnen i ett läge för geo-replikerade persistence och import/export-funktionen är inaktiverad. Så om initierade redundans för en kund eller i fall där en replikeringslänk har brutits mellan paret geo-replikerad sekundär behåller i minnet data som den har synkroniserats från primärt fram till den tidpunkten. Det finns ingen garanti för recovery point som anges i sådana situationer.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan jag använda PowerShell eller Azure CLI för att hantera Geo-replikering?

Just nu kan du bara hantera Geo-replikering med hjälp av Azure portal.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hur mycket kostar det för att replikera data i Azure-regioner?

När du använder Geo-replikering replikeras data från den primära länkade cachen till det sekundära länkat cacheminnet. Om det finns två länkade cacheminnen i samma Azure-region, är gratis för dataöverföringen. Om det finns två länkade cacheminnen i olika Azure-regioner, är dataöverföringsavgift för Geo-replikering bandbreddskostnaden för att replikera dessa data till andra Azure-region. Mer information finns i [prisinformation för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Varför åtgärden misslyckas när jag försökte ta bort min länkade cache?

När två cacheminnen länkas samman, kan du ta bort cache eller resursgruppen som innehåller dem tills du tar bort länken Geo-replikering. Om du försöker ta bort resursgruppen som innehåller en eller båda av de länkade cacheminnen, andra resurser i resursgruppen tas bort, men resursgruppen kvar i den `deleting` tillstånd och eventuella länkade cacheminnen i resursgruppen finns kvar i `running`tillstånd. Bryta länken Geo-replikering för att slutföra borttagningen av resursgruppen och de länkade cacheminnen i den enligt beskrivningen i [ta bort en Geo-replikeringslänken](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Vilken region ska jag använda för min sekundära länkat cacheminne?

I allmänhet rekommenderas för cacheminnet finnas i samma Azure-region som det program som har åtkomst till den. Om programmet har en primär och återställningsplats region, ska dina primära och sekundära cacheminnen finnas i samma regioner. Mer information om länkade regioner finns i [metodtips – Azure länkade regioner](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hur fungerar växling till det sekundära länkat cacheminnet?

I denna första version av Geo-replikering stöder Azure Cache för Redis inte automatisk redundans i Azure-regioner. GEO-replikering används främst i ett katastrofåterställningsscenario. I scenario för en distater, bör kunderna ta fram hel programstack i en säkerhetskopiering region på ett samordnat sätt i stället för att låta enskilda programkomponenter som bestämmer när du vill växla till deras säkerhetskopior på egen hand. Detta är särskilt relevanta till Redis. En av de främsta fördelarna med Redis är att det är en mycket låg latens butik. Om Redis som används av ett program som växlar över till en annan Azure-region, men Beräkningsnivån inte, skulle har lagts till tur och RETUR-tid ha en märkbar effekt på prestanda. Därför vill vi att undvika Redis misslyckas över automatiskt på grund av problem med tillfälliga tillgänglighet.

För närvarande för att påbörja redundans måste du ta bort länken Geo-replikering i Azure-portalen och ändra sedan anslutning slutpunkten i Redis-klient från den primära länkade cachen till den sekundära cachen (tidigare länkade). När två cacheminnen är avassocieras repliken blir en vanlig skrivskyddad cache igen och accepterar begäranden direkt från Redis-klienter.


## <a name="next-steps"></a>Nästa steg

Läs mer om den [Azure Cache för Premium-nivån Redis](cache-premium-tier-intro.md).

