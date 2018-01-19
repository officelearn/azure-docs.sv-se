---
title: "Hur du konfigurerar Geo-replikering för Azure Redis-Cache | Microsoft Docs"
description: "Lär dig mer om Azure Redis-Cache-instanser replikeras geografiska regioner."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Hur du konfigurerar Geo-replikering för Azure Redis-Cache

GEO-replikering tillhandahåller en mekanism för att länka två instanser i Premium-nivån Azure Redis-Cache. En cache betecknas som primär länkade cacheminnet och andra som sekundär länkade cache. Sekundär länkade cachen skrivskyddas och data som skrivs till den primära cachen replikeras till sekundär länkade cachen. Den här funktionen kan användas för att replikera en cache över Azure-regioner. Den här artikeln beskrivs hur du konfigurerar Geo-replikering för din Premium-nivån Azure Redis-Cache-instanser.

## <a name="geo-replication-prerequisites"></a>Krav för GEO-replikering

Om du vill konfigurera Geo-replikering mellan två cacheminnen måste följande krav uppfyllas:

- Båda cacheminnen måste vara [premiumnivån](cache-premium-tier-intro.md) cachelagrar.
- Båda cacheminnen måste finnas i samma Azure-prenumerationen.
- Sekundär länkade cachen måste vara samma prisnivån eller en större prisnivå än den primära länka cachen.
- Om den primära länka cachen har aktiverad klustring, måste sekundära länkade cachen ha aktiverad med samma antal delar som primär länkade cachen klustring.
- Båda cache måste skapas och körs.
- Beständiga måste inte aktiveras på antingen cachen.
- GEO-replikering mellan Cacheminnena i samma virtuella nätverk stöds. GEO-replikering mellan Cacheminnena i olika Vnet stöds också, förutsatt att två virtuella nätverk konfigureras så att resurser i Vnet ska kunna nå varandra via TCP-anslutningar.

När Geo-replikering har konfigurerats, gäller följande begränsningar för länkade cache-par:

- Sekundär länkade cacheminnet är skrivskyddade. Du kan läsa från den, men du kan inte skriva data till den. 
- Alla data som var i sekundära länkade cachen innan länken lades till tas bort. Om Geo-replikering tas därefter bort men förblir replikerade data i sekundära länkade cachen.
- Du kan inte påbörja en [skalning åtgärden](cache-how-to-scale.md) på antingen cachen eller [ändra antalet shards](cache-how-to-premium-clustering.md) om cacheminnet innehåller aktiverad klustring.
- Du kan inte aktivera persistence på antingen cachen.
- Du kan använda [exportera](cache-how-to-import-export-data.md#export) med antingen cache, men du kan bara [importera](cache-how-to-import-export-data.md#import) till primära länkade cachen.
- Du kan inte ta bort länkade cache eller resursgruppen som innehåller dem, tills du tar bort länken Geo-replikering. Mer information finns i [varför åtgärden misslyckas när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Om två Cacheminnena i olika regioner, gäller kostnader för nätverksegress för data som replikeras över regioner till sekundär länkade cachen. Mer information finns i [hur mycket kostar det för att replikera data mellan Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Det finns ingen automatisk redundans till sekundär länkade cachen om den primära cachen (och dess replik) kraschar. Du skulle behöva manuellt ta bort länken Geo-replikering och peka klientprogram i cacheminnet som tidigare var sekundär länkade cacheminnet i ordning för failover-klientprogram. Mer information finns i [hur fungerar inte körs i sekundära länkade cacheminne?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Lägga till en länk för Geo-replikering

1. Om du vill länka samman två premium cacheminnen för geo-replikering, klickar du på **Geo-replikering** resurs-menyn i cacheminnet som den primära länkade cachelagra och klicka sedan på **Lägg till cache replikeringslänk** från den **georeplikering** bladet.

    ![Lägga till en länk](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicka på namnet på önskat cacheminne om sekundära från den **kompatibel cacheminnen** lista. Om din önskade cache inte visas i listan, kontrollera att den [Geo-replikering krav](#geo-replication-prerequisites) för önskad sekundära cachen är uppfyllda. Om du vill filtrera cacheminnen efter region, klickar du på önskad region på kartan för att visa endast dessa cacheminnen i den **kompatibel cacheminnen** lista.

    ![GEO-replikering kompatibel cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Du kan också initiera länkningen eller visa information om den sekundära cachen med hjälp av snabbmenyn.

    ![Snabbmenyn för GEO-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicka på **länk** att länka samman två cacheminnen och börja replikeringen.

    ![Länken cachelagrar](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Du kan visa förloppet för replikeringen på den **georeplikering** bladet.

    ![Länka status](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Du kan också visa länkande status på den **översikt** bladet för både de primära och sekundära cacheminnen.

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    När replikeringen är klar, den **länka status** ändras till **lyckades**.

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Primär länkade cachen fortfarande är tillgänglig för användning under länkningen, men sekundära länkade cache är inte tillgängligt förrän länkningen.

## <a name="remove-a-geo-replication-link"></a>Ta bort en länk för Geo-replikering

1. Om du vill ta bort länken mellan två cacheminnen och stoppa Geo-replikering, klickar du på **Avlänka cacheminnen** från den **georeplikering** bladet.
    
    ![Avlänka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    När bryta länkar till processen är klar är sekundära cacheminnet tillgängliga för både läsningar och skrivningar.

>[!NOTE]
>När länken Geo-replikering har tagits bort, förblir replikerade data från den primära länka cachen i sekundära cache.
>
>

## <a name="geo-replication-faq"></a>Vanliga frågor om GEO-replikering

- [Kan jag använda Geo-replikering med en Standard- eller Basic-nivån cache?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Är Mina cacheminne tillgängliga för användning under länkade eller bryta länkar?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan jag koppla fler än två cacheminnen tillsammans?](#can-i-link-more-than-two-caches-together)
- [Kan jag koppla två cacheminnen från andra Azure-prenumerationer?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan jag koppla två cacheminnen med olika storlekar?](#can-i-link-two-caches-with-different-sizes)
- [Kan jag använda Geo-replikering med aktiverad klustring?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan jag använda Geo-replikering med min Cacheminnena i ett virtuellt nätverk?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Vad är replikeringsschemat för Redis geo-replikering?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hur lång tid tar geo-replikering replikering?](#how-long-does-geo-replication-replication-take)
- [Återställningspunkten replikering garanteras?](#is-the-replication-recovery-point-guaranteed)
- [Kan jag använda PowerShell eller Azure CLI för att hantera Geo-replikering?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Hur mycket kostar det för att replikera data mellan Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Varför åtgärden misslyckas när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Vilken region ska använda för min sekundära länkade cache?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hur fungerar inte körs i sekundära länkade cacheminne?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan jag använda Geo-replikering med en Standard- eller Basic-nivån cache?

Nej, Geo-replikering är endast tillgängligt för Premium-nivån.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Är Mina cacheminne tillgängliga för användning under länkade eller bryta länkar?

- När koppla ihop två cacheminnen för Geo-replikering, primära länkade cachen fortfarande är tillgänglig för användning men sekundära länkade cache är inte tillgängligt förrän länkningen.
- När du tar bort länken mellan två cacheminnen Geo-replikering, är både cacheminnen tillgängliga för användning.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan jag koppla fler än två cacheminnen tillsammans?

Nej, när du använder Geo-replikering kan du bara länka två cacheminnen tillsammans.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan jag koppla två cacheminnen från andra Azure-prenumerationer?

Ingen, både cacheminnen måste finnas i samma Azure-prenumerationen.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan jag koppla två cacheminnen med olika storlekar?

Ja, så länge som sekundärt länkade cache är större än den primära länka cachen.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan jag använda Geo-replikering med aktiverad klustring?

Ja, så länge båda cacheminnen har samma antal delar.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan jag använda Geo-replikering med min Cacheminnena i ett virtuellt nätverk?

Ja, Geo-replikering av Cacheminnena i Vnet stöds. 

- GEO-replikering mellan Cacheminnena i samma virtuella nätverk stöds.
- GEO-replikering mellan Cacheminnena i olika Vnet stöds också, förutsatt att två virtuella nätverk konfigureras så att resurser i Vnet ska kunna nå varandra via TCP-anslutningar.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Vad är replikeringsschemat för Redis geo-replikering?

Replikering inte sker på ett visst schema är kontinuerlig och asynkrona engångsfaktorautentisering alla skrivningar så att primärt replikeras asynkront omedelbart på sekundärt.

### <a name="how-long-does-geo-replication-replication-take"></a>Hur lång tid tar geo-replikering replikering?

Replikering är inkrementell, asynkron och kontinuerlig och den tid det tar är vanligtvis inte mycket skiljer sig från fördröjningen mellan regioner. I vissa fall kan kan vid vissa tidpunkter, sekundärt behöva göra en fullständig synkronisering av data från den primära servern. Replikeringstid i det här fallet är beroende av flera faktorer, t.ex.: belastningen på den primära cachen, bandbredd som är tillgänglig på cache-datorn bland region latens osv. Som exempel, baserat på vissa tester som vi har hittat ut då replikering för en fullständig 53 GB georeplikerad länka i East oss och västra USA regioner kan finnas var som helst mellan 5 till 10 minuter.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Återställningspunkten replikering garanteras?

För närvarande för cacheminnet i ett läge för geo-replikerade beständighet och importera och exportera funktionen är inaktiverad. Så om initierade redundans för en kund eller i fall där en replikeringslänk har brutits mellan par georeplikerad sekundär behåller i minnet data som den har synkroniserats från den primära servern tills den tidpunkten och tid. Det finns ingen garanti för att återställningen punkt i sådana situationer.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan jag använda PowerShell eller Azure CLI för att hantera Geo-replikering?

Du kan endast hantera Geo-replikering med Azure-portalen just nu.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hur mycket kostar det för att replikera data mellan Azure-regioner?

När du använder Geo-replikering replikeras data från primär länkade cachen till sekundär länkade cachen. Om det finns två länkade Cacheminnena i samma Azure-region är gratis för att överföra data. Om det finns två länkade Cacheminnena i olika Azure-regioner, är Geo-replikering data transfer tillägget bandbredd kostnaden för att replikera data till andra Azure-regionen. Mer information finns i [bandbredd prisinformation](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Varför åtgärden misslyckas när jag försökte ta bort min länkade cache?

När två cacheminnen är länkade till varandra, kan du ta bort cache eller resursgruppen som innehåller dem förrän du tar bort länken Geo-replikering. Om du försöker ta bort resursgruppen som innehåller en eller båda av länkade resurserna i resursgruppen tas bort, men resursgruppen förblir i det `deleting` tillstånd och eventuella länkade Cacheminnena i resursgruppen finns kvar i `running`tillstånd. För att slutföra borttagningen av resursgruppen och länkade Cacheminnena i den bryta länken Geo-replikering som beskrivs i [ta bort en länk för Geo-replikering](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Vilken region ska använda för min sekundära länkade cache?

I allmänhet rekommenderas för ditt cacheminne ska finnas i samma Azure-region som det program som har åtkomst till den. Om programmet har en primär och en återställningsplats region, ska din primära och sekundära cacheminnen finnas i samma regioner. Mer information om parad regioner finns [metodtips – parad Azure-regioner](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hur fungerar inte körs i sekundära länkade cacheminne?

I den första versionen av Geo-replikering stöder Azure Redis-Cache inte automatisk redundans över Azure-regioner. GEO-replikering används framför allt för en katastrofåterställning. I en distater återställningsscenario bör kunder visa hel programstack i en säkerhetskopiering region i ett samordnat sätt i stället för att låta enskilda programkomponenter bestämma när du vill växla till deras säkerhetskopior på egen hand. Detta är särskilt relevanta till Redis. En av de främsta fördelarna med Redis är att det är en mycket låg latens store. Om Redis som används av ett program som växlar över till en annan Azure-region men inte i beräknings-nivån, skulle tillagda tur och RETUR-tid ha en märkbar effekt på prestanda. Därför vill vi undvika Redis misslyckas över automatiskt på grund av problem med tillfälliga tillgänglighet.

För närvarande för att påbörja redundans måste du ta bort länken Geo-replikering i Azure-portalen och sedan ändra anslutning slutpunkt i Redis-klient från primära länkade cachen (tidigare länkade) sekundär cacheminne. När två cacheminnen är koppla bort, blir en vanlig skrivskyddad cache igen repliken och accepterar begäranden direkt från Redis-klienter.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md).

