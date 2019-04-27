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
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552389"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Konfigurera Geo-replikering för Azure Cache för Redis

GEO-replikering är en mekanism för att länka två Premium-nivån Azure Cache för Redis-instanser. En cache är valt som den primära länkade cachen och andra som sekundär länkat cacheminne. Sekundär länkade cacheminnet blir skrivskyddade och data som skrivs till den primära cachen replikeras till det sekundära länkat cacheminnet. Den här funktionen kan användas för att replikera en cache i Azure-regioner. Den här artikeln beskrivs hur du konfigurerar Geo-replikering för din Premium-nivån Azure Cache för Redis-instanser.

## <a name="geo-replication-prerequisites"></a>Krav för GEO-replikering

Om du vill konfigurera Geo-replikering mellan två cacheminnen, måste följande krav uppfyllas:

- Båda cacheminnen är [premiumnivån](cache-premium-tier-intro.md) cachelagrar.
- Båda cacheminnen är i samma Azure-prenumeration.
- Sekundär länkat cacheminne är samma storlek eller större än den primära länkade cachen cache.
- Båda cache-minnen skapas och körs.

Vissa funktioner stöds inte med geo-replikering:

- Persistence stöds inte med geo-replikering.
- Klustring stöds om båda cacheminnen har-klustring aktiverat och har samma antal shards.
- Cacheminnen i samma virtuella nätverk stöds.
- Cacheminnen i olika virtuella nätverk stöds med varningar. Se [kan jag använda Geo-replikering med min cacheminnen i ett virtuellt nätverk?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) för mer information.

När Geo-replikering har konfigurerats kan gäller följande begränsningar för din länkade cache-par:

- Sekundär länkade cacheminnet är skrivskyddat. Du kan läsa från den, men du kan inte skriva data till den. 
- Alla data som fanns i det sekundära länkat cacheminnet innan länken har lagts till tas bort. Om Geo-replikering är senare tas bort men replikerade data blir kvar i det sekundära länkat cacheminnet.
- Det går inte att [skala](cache-how-to-scale.md) antingen cachen om cacheminnen är länkade.
- Det går inte att [ändra antalet shards](cache-how-to-premium-clustering.md) om cacheminnet innehåller aktiverad klustring.
- Du kan inte aktivera persistence på antingen cache.
- Du kan [exportera](cache-how-to-import-export-data.md#export) från antingen cache.
- Det går inte att [Import](cache-how-to-import-export-data.md#import) till sekundär länkade cachen.
- Du kan inte ta bort länkat cacheminne eller resursgruppen som innehåller dem, tills du Avlänka cacheminnen. Mer information finns i [varför åtgärden misslyckas när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Om cacheminnen finns i olika regioner, använder kostnader för nätverksegress för de data som flyttas mellan regioner. Mer information finns i [hur mycket kostar det för att replikera data mellan Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatisk redundans inträffar inte mellan det primära och sekundära länkat cacheminnet. Mer information och information om hur du redundans ett klientprogram finns [hur fungerar växling till det sekundära länkat cacheminnet?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Lägg till en länk för Geo-replikering

1. Första gången klickar du på för att länka ihop två cacheminnen för geo-replikering, **Geo-replikering** från resurs-menyn på cacheminnet som du tänker vara primärt länkad cache. Klicka sedan på **Lägg till cachereplikeringslänk** från den **Geo-replikering** bladet.

    ![Lägg till länk](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicka på namnet på din avsedda Sekundär cache från den **kompatibla cacheminnen** lista. Om din Sekundär cache inte visas i listan kan du kontrollera att den [Geo-replikering krav](#geo-replication-prerequisites) för det sekundära cacheminnet är uppfyllda. Om du vill filtrera cacheminnen efter region, klickar du på den region på kartan för att visa endast dessa cacheminnen i den **kompatibla cacheminnen** lista.

    ![Kompatibla cacheminnen för GEO-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Du kan också starta länkningen eller visa information om den sekundära cachen genom att använda snabbmenyn.

    ![Snabbmenyn för GEO-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicka på **länk** kopplar ihop de två cacheminnen och börja replikering.

    ![Länka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Du kan visa förloppet för replikeringen på den **Geo-replikering** bladet.

    ![Status för länkramverk](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Du kan också visa länkande status på den **översikt** bladet för både de primära och sekundära cacheminnen.

    ![Status för cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    När replikeringen är klar, den **länka status** ändras till **lyckades**.

    ![Status för cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Den primära länkade cachen fortfarande är tillgängliga för användning under länkningen. Sekundär länkat cacheminne är inte tillgängligt förrän länkningen är klar.

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

- När du länkar, är den primära länkade cachen fortfarande tillgängligt medan länkningen slutförs.
- När du länkar, sekundära länkat cacheminne är inte tillgängligt förrän länkningen är klar.
- När du tar bort länken, både cacheminnen fortfarande är tillgängliga medan avlänkar processen körs.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan jag koppla fler än två cacheminnen tillsammans?

Nej, du kan bara koppla två cacheminnen tillsammans.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan jag koppla två cacheminnen från olika Azure-prenumerationer?

Nej, båda cacheminnen måste finnas i samma Azure-prenumeration.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan jag koppla två cacheminnen med olika storlekar?

Ja, så länge som det sekundära länkat cacheminnet är större än den primära länkade cachen.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan jag använda Geo-replikering med aktiverad klustring?

Ja, så länge båda cacheminnen har samma antal shards.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan jag använda Geo-replikering med min cacheminnen i ett virtuellt nätverk?

Ja, Geo-replikering av cacheminnen i virtuella nätverk kan användas med varningar:

- GEO-replikering mellan cacheminnen i samma virtuella nätverk stöds.
- GEO-replikering mellan cache i olika virtuella nätverk stöds också.
  - Om de virtuella nätverken är i samma region kan du ansluta dem med hjälp av [VNET-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) eller en [VPN-Gateway VNET-till-VNET-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Om de virtuella nätverken finns i olika regioner, geo-replikering med hjälp av VNET-peering stöds inte på grund av en begränsning med grundläggande interna belastningsutjämnare. Läs mer om VNET peering begränsningar [Vnet - Peering - krav och begränsningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Den rekommenderade lösningen är att använda en VPN-Gateway VNET-till-VNET-anslutning.

Med hjälp av [den här Azure-mall](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), kan du snabbt distribuera två geo-replikerade cacheminnen i ett virtuellt nätverk som är anslutna med en VPN-Gateway VNET-till-VNET-anslutning.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Vad är replikeringsschemat för Redis geo-replikering?

Replikeringen är kontinuerlig och asynkrona och sker inte på ett visst schema. Alla skrivningar till primärt klar replikeras omedelbart och asynkront på sekundärt.

### <a name="how-long-does-geo-replication-replication-take"></a>Hur lång tid tar geo-replikering replikering?

Replikeringen är inkrementell, asynkrona och kontinuerlig och den tid det tar inte mycket skiljer sig från svarstiden i flera regioner. I vissa fall kanske det sekundära cacheminnet måste göra en fullständig synkronisering av data från primärt. Tid i det här fallet är beroende av flera faktorer som: belastningen på den primära cachen och tillgänglig nätverksbandbredd mellan regioner svarstid. Vi har hittat replikeringstid för ett 53 GB geo-replikerade par kan finnas var som helst mellan 5-10 minuter.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garanteras återställningspunkt replikering?

För cacheminnen i ett läge för geo-replikerade har persistence inaktiverats. Om ett par med geo-replikerade olänkade, till exempel en redundansväxling initieras av kunden, behåller det sekundära länkat cacheminnet synkroniserade data upp till den tidpunkten. Ingen återställningspunkt garanteras i sådana situationer.

Att hämta en återställningspunkt [exportera](cache-how-to-import-export-data.md#export) från antingen cache. Du kan senare [Import](cache-how-to-import-export-data.md#import) till den primära länkade cachen.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan jag använda PowerShell eller Azure CLI för att hantera Geo-replikering?

Ja, geo-replikering kan hanteras med Azure-portalen, PowerShell eller Azure CLI. Mer information finns i den [PowerShell-dokument](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) eller [Azure CLI-dokument](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hur mycket kostar det för att replikera data i Azure-regioner?

När du använder Geo-replikering replikeras data från den primära länkade cachen till det sekundära länkat cacheminnet. Det är kostnadsfritt för att överföra data om två länkade cacheminnen är i samma region. Om det finns två länkade cacheminnen i olika regioner, är dataöverföringsavgift nätverk utgående kostnaden för data som flyttas i antingen regioner. Mer information finns i [prisinformation för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Varför åtgärden misslyckas när jag försökte ta bort min länkade cache?

GEO-replikerade cacheminnen och deras resursgrupper kan inte raderas medan länkad tills du tar bort länken geo-replikering. Om du försöker ta bort resursgruppen som innehåller en eller båda av de länkade cacheminnen, andra resurser i resursgruppen tas bort, men resursgruppen kvar i den `deleting` tillstånd och eventuella länkade cacheminnen i resursgruppen finns kvar i `running`tillstånd. Avlänka cacheminnen om du vill ta bort resursgruppen och de länkade cacheminnen i den, enligt beskrivningen i [ta bort en Geo-replikeringslänken](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Vilken region ska jag använda för min sekundära länkat cacheminne?

I allmänhet rekommenderas för cacheminnet finnas i samma Azure-region som det program som har åtkomst till den. För program med olika områden för primära och återställningsplats rekommenderas dina primära och sekundära cacheminnen finns i samma regioner. Mer information om länkade regioner finns i [metodtips – Azure länkade regioner](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hur fungerar växling till det sekundära länkat cacheminnet?

Automatisk redundans i Azure-regioner stöds inte för geo-replikerade cacheminnen. I ett scenario med katastrofåterställning bör kunder ta fram hel programstack på ett samordnat sätt i deras säkerhetskopiering region. Att låta enskilt program komponenter bestämma när du vill växla till deras säkerhetskopior på egen hand kan påverka prestandan negativt. En av de främsta fördelarna med Redis är att det är en mycket låg latens butik. Om kundens huvudprogrammet finns i en annan region än dess cache, skulle den extra fram och åter tid ha en märkbar effekt på prestanda. Därför undvika växlar automatiskt på grund av problem med tillfälliga tillgänglighet.

Om du vill starta redundans initieras av kunden först Avlänka cacheminnen. Ändra Redis-klient för att använda Anslutningens slutpunkt för den sekundära cachen (tidigare länkade). När två cacheminnen är avlänkade, det sekundära cacheminnet blir en vanlig skrivskyddad cache igen och accepterar begäranden direkt från Redis-klienter.

## <a name="next-steps"></a>Nästa steg

Läs mer om den [Azure Cache för Premium-nivån Redis](cache-premium-tier-intro.md).
