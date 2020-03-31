---
title: Konfigurera geo-replikering för Azure Cache för Redis | Microsoft-dokument
description: Lär dig hur du replikerar dina Azure-cache för Redis-instanser över geografiska regioner.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129425"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Konfigurera geo-replikering för Azure Cache för Redis

Geo-replikering är en mekanism för att länka två Azure-cache på Premium-nivå för Redis-instanser. En cache väljs som den primära länkade cachen och den andra som den sekundära länkade cachen. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera en cache över Azure-regioner. Den här artikeln innehåller en guide för att konfigurera geo-replikering för dina Azure-nivå azure-cache för Redis-instanser på Premium-nivån.

## <a name="geo-replication-prerequisites"></a>Förutsättningar för georeplikering

För att konfigurera geo-replikering mellan två cacheminnen måste följande förutsättningar uppfyllas:

- Båda cacheminnena är [Cacheminnen på Premium-nivån.](cache-premium-tier-intro.md)
- Båda cacheminnena finns i samma Azure-prenumeration.
- Den sekundära länkade cachen har antingen samma cachestorlek eller en större cachestorlek än den primära länkade cachen.
- Båda cacheminnena skapas och i körläge.

Vissa funktioner stöds inte med geo-replikering:

- Persistens stöds inte med geo-replikering.
- Klustring stöds om båda cacheminnena har kluster aktiverat och har samma antal shards.
- Cacheminnen i samma virtuella nätverk stöds.
- Cacheminnen i olika virtuella nätverk stöds med varningar. Mer information [finns i Kan jag använda georeplikering med mina cacheminnen i ett VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

När geo-replikering har konfigurerats gäller följande begränsningar för det länkade cacheparet:

- Den sekundära länkade cachen är skrivskyddad. Du kan läsa från den, men du kan inte skriva några data till den. 
- Alla data som fanns i den sekundära länkade cachen innan länken lades till tas bort. Om geo-replikering senare tas dock bort kvar i den sekundära länkade cachen.
- Du kan inte [skala](cache-how-to-scale.md) någon av cacheminnena medan cacheminnena är länkade.
- Du kan inte [ändra antalet shards](cache-how-to-premium-clustering.md) om cachen har kluster aktiverat.
- Du kan inte aktivera beständighet på någon av cacheminnet.
- Du kan [exportera](cache-how-to-import-export-data.md#export) från någon av cacheminnena.
- Du kan inte [importera](cache-how-to-import-export-data.md#import) till den sekundära länkade cachen.
- Du kan inte ta bort antingen länkad cache eller resursgruppen som innehåller dem förrän du tar bort länken till cacheminnena. Mer information finns i [Varför misslyckades åtgärden när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Om cacheminnena finns i olika regioner gäller nätverksutgående kostnader för data som flyttas mellan regioner. Mer information finns i [Hur mycket kostar det att replikera mina data i Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatisk redundans sker inte mellan den primära och sekundära länkade cachen. Mer information och information om hur du redundans över ett klientprogram finns i [Hur fungerar det inte att gå över till den sekundära länkade cachen?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Lägga till en geo-replikeringslänk

1. Om du vill länka samman två cacheminnen för georeplikering klickar du på **Geo-replikering** från resursmenyn i cacheminnet som du tänker vara den primära länkade cachen. Klicka sedan på **Lägg till cachereplikeringslänk** från **bladet Geo-replikering.**

    ![Lägg till länk](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicka på namnet på den avsedda sekundära cachen från listan **Kompatibla cacheminnen.** Om den sekundära cachen inte visas i listan kontrollerar du att [förutsättningar för georeplikering](#geo-replication-prerequisites) för den sekundära cachen uppfylls. Om du vill filtrera cacheminnena efter region klickar du på området på kartan för att bara visa cacheminnen i listan **Kompatibla cacheminnen.**

    ![Geo-replikeringskompatibla cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Du kan också starta länkningsprocessen eller visa information om den sekundära cachen med hjälp av snabbmenyn.

    ![Snabbmeny för georeplikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicka på **Länk** om du vill länka ihop de två cacheminnena och påbörja replikeringsprocessen.

    ![Länka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Du kan visa förloppet för replikeringsprocessen på **geo-replikeringsbladet.**

    ![Länka status](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Du kan också visa länkningsstatus på **bladet Översikt** för både primära och sekundära cacheminnen.

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    När replikeringsprocessen är klar ändras **länkstatusen** till **Lyckades**.

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Den primära länkade cachen är fortfarande tillgänglig för användning under länkningsprocessen. Den sekundära länkade cachen är inte tillgänglig förrän länkningsprocessen är klar.

## <a name="remove-a-geo-replication-link"></a>Ta bort en geo-replikeringslänk

1. Om du vill ta bort länken mellan två cacheminnen och stoppa georeplikering klickar du på **Ta bort länkar** från **bladet Geo-replikering.**
    
    ![Ta bort länken till cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    När processen för att koppla av är den sekundära cachen tillgänglig för både läsningar och skrivningar.

>[!NOTE]
>När geo-replikeringslänken tas bort finns de replikerade data från den primära länkade cachen kvar i den sekundära cachen.
>
>

## <a name="geo-replication-faq"></a>Vanliga frågor om geo-replikering

- [Kan jag använda georeplikering med en cache för standard- eller basic-nivå?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Är min cache tillgänglig för användning under länknings- eller kopplingsprocessen?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan jag länka mer än två cacheminnen tillsammans?](#can-i-link-more-than-two-caches-together)
- [Kan jag länka två cacheminnen från olika Azure-prenumerationer?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan jag länka två cacheminnen med olika storlekar?](#can-i-link-two-caches-with-different-sizes)
- [Kan jag använda georeplikering med klustring aktiverat?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan jag använda georeplikering med mina cacheminnen i ett VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Vad är replikeringsschemat för Redis geo-replication?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hur lång tid tar det med georeplikering?](#how-long-does-geo-replication-replication-take)
- [Är replikeringsåterställningspunkten garanterad?](#is-the-replication-recovery-point-guaranteed)
- [Kan jag använda PowerShell eller Azure CLI för att hantera geo-replikering?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Hur mycket kostar det att replikera mina data i Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Varför misslyckades åtgärden när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Vilken region ska jag använda för min sekundära länkade cache?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hur fungerar det att misslyckas med den sekundära länkade cachen?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan jag använda georeplikering med en cache för standard- eller basic-nivå?

Nej, geo-replikering är endast tillgängligt för Cacheminnen på Premium-nivån.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Är min cache tillgänglig för användning under länknings- eller kopplingsprocessen?

- När du länkar förblir den primära länkade cachen tillgänglig medan länkningsprocessen är klar.
- När du länkar är den sekundära länkade cachen inte tillgänglig förrän länkningsprocessen är klar.
- När du tar bort länken förblir båda cacheminnena tillgängliga medan processen för att ta bort länken är klar.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan jag länka mer än två cacheminnen tillsammans?

Nej, du kan bara länka samman två cacheminnen.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan jag länka två cacheminnen från olika Azure-prenumerationer?

Nej, båda cacheminnena måste finnas i samma Azure-prenumeration.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan jag länka två cacheminnen med olika storlekar?

Ja, så länge den sekundära länkade cachen är större än den primära länkade cachen.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan jag använda georeplikering med klustring aktiverat?

Ja, så länge båda cacheminnena har samma antal shards.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan jag använda georeplikering med mina cacheminnen i ett VNET?

Ja, georeplikering av cacheminnen i VNETs stöds med varningar:

- Geo-replikering mellan cacheminnen i samma virtuella nätverk stöds.
- Geo-replikering mellan cacheminnen i olika virtuella datorer stöds också.
  - Om VNET:erna finns i samma region kan du ansluta dem med [VNET-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) eller en [VPN Gateway VNET-till-VNET-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Om VNET:erna finns i olika regioner stöds inte georeplikering med VNET-peering på grund av ett villkor med grundläggande interna belastningsutjämnare. Mer information om VNET-peering-begränsningar finns i [Virtuellt nätverk - Peering - Krav och begränsningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Den rekommenderade lösningen är att använda en VPN Gateway VNET-to-VNET-anslutning.

Med [den här Azure-mallen](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)kan du snabbt distribuera två geo-replikerade cacheminnen till ett VNET som är anslutet till en VPN Gateway VNET-till-VNET-anslutning.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Vad är replikeringsschemat för Redis geo-replication?

Replikering är kontinuerligt och asynkront och sker inte enligt ett visst schema. Alla skrivningar som görs till den primära replikeras omedelbart och asynkront på sekundär.

### <a name="how-long-does-geo-replication-replication-take"></a>Hur lång tid tar det med georeplikering?

Replikering är inkrementell, asynkron och kontinuerlig och den tid det tar skiljer sig inte mycket från svarstiden mellan regioner. Under vissa omständigheter kan den sekundära cachen krävas för att göra en fullständig synkronisering av data från den primära. Replikeringstiden i det här fallet är beroende av antalet faktorer som: belastning på den primära cachen, tillgänglig nätverksbandbredd och svarstid mellan regioner. Vi har hittat replikeringstid för ett fullständigt 53 GB georepet replikerat par kan vara någonstans mellan 5 och 10 minuter.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Är replikeringsåterställningspunkten garanterad?

För cacheminnen i ett georenadsläge är persistens inaktiverad. Om ett georepetraterat par inte är länkat, till exempel en kundinitierad redundans, håller den sekundära länkade cachen sina synkroniserade data fram till den tidpunkten. Ingen återvinningspunkt garanteras i sådana situationer.

Exportera från någon av cacheminnena [för](cache-how-to-import-export-data.md#export) att hämta en återställningspunkt. Du kan senare [importera](cache-how-to-import-export-data.md#import) till den primära länkade cachen.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan jag använda PowerShell eller Azure CLI för att hantera geo-replikering?

Ja, geo-replikering kan hanteras med Azure-portalen, PowerShell eller Azure CLI. Mer information finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) eller [Azure CLI-dokumenten](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hur mycket kostar det att replikera mina data i Azure-regioner?

När du använder georeplikering replikeras data från den primära länkade cachen till den sekundära länkade cachen. Det finns ingen avgift för dataöverföringen om de två länkade cacheminnena finns i samma region. Om de två länkade cacheminnena finns i olika regioner är dataöverföringsavgiften nätverksutgående kostnad för data som rör sig över någon av regionerna. Mer information finns i [Information om bandbreddsprissättning](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Varför misslyckades åtgärden när jag försökte ta bort min länkade cache?

Geo-replikerade cacheminnen och deras resursgrupper kan inte tas bort när de länkas förrän du tar bort geo-replikeringslänken. Om du försöker ta bort resursgruppen som innehåller en eller båda av de länkade cacheminnena tas de `deleting` andra resurserna i resursgruppen bort, men `running` resursgruppen förblir i tillståndet och alla länkade cacheminnen i resursgruppen förblir i tillståndet. Om du vill ta bort resursgruppen och de länkade cacheminnena i resursen helt tar du bort länken till cacheminnena enligt beskrivningen i [Ta bort en georeplikeringslänk](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Vilken region ska jag använda för min sekundära länkade cache?

I allmänhet rekommenderas att cachen finns i samma Azure-region som programmet som kommer åt den. För program med separata primära regioner och reservregioner rekommenderas att dina primära och sekundära cacheminnen finns i samma regioner. Mer information om parade regioner finns i [Metodtips – Azure Paired-regioner](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hur fungerar det att misslyckas med den sekundära länkade cachen?

Automatisk redundans över Azure-regioner stöds inte för geo-replikerade cacheminnen. I ett katastrofåterställningsscenario bör kunder ta upp hela programstacken på ett samordnat sätt i sin säkerhetskopieringsregion. Om enskilda programkomponenter bestämmer när de ska växla till sina säkerhetskopior på egen hand kan prestanda påverkas negativt. En av de viktigaste fördelarna med Redis är att det är en mycket låg latens butik. Om kundens huvudprogram finns i en annan region än cacheminnet, skulle den tillagda tur-och-retur-tiden ha en märkbar inverkan på prestanda. Därför undviker vi att misslyckas automatiskt på grund av tillfälliga tillgänglighetsproblem.

Om du vill starta en kundinitierad redundans tar du först bort länken till cacheminnena. Ändra sedan Redis-klienten så att anslutningsslutpunkten för den (tidigare länkade) sekundära cachen används. När de två cacheminnena inte är länkade blir den sekundära cachen en vanlig läs-skriv-cache igen och accepterar begäranden direkt från Redis-klienter.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md).
