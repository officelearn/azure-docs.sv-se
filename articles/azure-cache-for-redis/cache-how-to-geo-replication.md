---
title: Så här konfigurerar du geo-replikering för Azure cache för Redis | Microsoft Docs
description: Lär dig hur du replikerar Azure-cachen för Redis-instanser i olika geografiska regioner.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74129425"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Så här konfigurerar du geo-replikering för Azure cache för Redis

Geo-replikering är en mekanism för att länka två Premium-nivåer i Azure-cache för Redis-instanser. Ett cacheminne har valts som primärt länkat cache och den andra som sekundär länkad cache. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera en cache i Azure-regioner. Den här artikeln innehåller en guide för hur du konfigurerar geo-replikering för Azure-cache på Premium nivå för Redis-instanser.

## <a name="geo-replication-prerequisites"></a>Krav för geo-replikering

Om du vill konfigurera geo-replikering mellan två cacheminnen måste följande krav uppfyllas:

- Båda cacheminnena är cacheminnen på [Premium-nivå](cache-premium-tier-intro.md) .
- Båda cacheminnena finns i samma Azure-prenumeration.
- Den sekundära länkade cachen har antingen samma cachestorlek eller större cachestorlek än den primära länkade cachen.
- Båda cacheminnena skapas och körs i ett körnings läge.

Vissa funktioner stöds inte med geo-replikering:

- Persistence stöds inte med geo-replikering.
- Klustring stöds om båda cacheminnena har aktive rad kluster och har samma antal Shards.
- Det finns stöd för cacheminnen i samma VNET.
- Cacheminnen i olika virtuella nätverk stöds med varningar. I [kan jag använda geo-replikering med mina cacheminnen i ett VNet?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) för mer information.

När geo-replikering har kon figurer ATS gäller följande begränsningar för det länkade cache-paret:

- Den sekundära länkade cachen är skrivskyddad. Du kan läsa från den, men du kan inte skriva några data till den. 
- Alla data som fanns i den sekundära länkade cachen innan länken lades till tas bort. Om geo-replikeringen senare tas bort, förblir replikerade data i den sekundära länkade cachen.
- Du kan inte [skala](cache-how-to-scale.md) antingen cache när cacheminnet är länkade.
- Du kan inte [ändra antalet Shards](cache-how-to-premium-clustering.md) om cacheminnet har Aktiver ATS för klustret.
- Du kan inte Aktivera persistence i båda cacheminnet.
- Du kan [Exportera](cache-how-to-import-export-data.md#export) från något av cacheminnet.
- Det går inte att [Importera](cache-how-to-import-export-data.md#import) till den sekundära länkade cachen.
- Du kan inte ta bort antingen länkat cacheminne eller resurs gruppen som innehåller dem förrän du tar bort kopplingen mellan cacheminnena. Mer information finns i [Varför gick det inte att utföra åtgärden när jag försökte ta bort mitt länkade cacheminne?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Om cacheminnena finns i olika regioner, gäller utgående kostnader för de data som flyttas mellan regioner. Mer information finns i [hur mycket kostar det att replikera mina data i Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatisk redundans sker inte mellan den primära och sekundära länkade cachen. Mer information och information om hur du aktiverar redundans för ett klient program finns i [Hur växlar sig över till det sekundära länkade cacheminnet?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Lägg till en länk till geo-replikering

1. Om du vill länka två cacheminnen för geo-replikering, Fist klickar du på **geo-replikering** på resurs-menyn i cachen som du vill ska vara den primära länkade cachen. Klicka sedan på **länken Lägg till cache-replikering** på bladet **geo-replikering** .

    ![Lägg till länk](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicka på namnet på den avsedda sekundära cachen från listan **kompatibla cacheminnen** . Om den sekundära cachen inte visas i listan kontrollerar du att [kraven för geo-replikering](#geo-replication-prerequisites) för den sekundära cachen är uppfyllda. Om du vill filtrera cacheminnena efter region klickar du på regionen på kartan för att endast visa de cacheminnen i listan **kompatibla cacheminnen** .

    ![Kompatibla cacheminnen för geo-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Du kan också starta länknings processen eller Visa information om den sekundära cachen med hjälp av snabb menyn.

    ![Snabb meny för geo-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicka på **Länka** för att länka de två cacheminnena och påbörja replikeringen.

    ![Länka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Du kan visa förloppet för replikeringen på bladet **geo-replikering** .

    ![Länk status](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Du kan också Visa länk status på bladet **Översikt** för både den primära och sekundära cachen.

    ![Cache-status](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    När replikeringen har slutförts ändras **länkens status** till **slutförd**.

    ![Cache-status](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Den primära länkade cachen är fortfarande tillgänglig för användning under länknings processen. Den sekundära länkade cachen är inte tillgänglig förrän länk processen har slutförts.

## <a name="remove-a-geo-replication-link"></a>Ta bort en länk till geo-replikering

1. Om du vill ta bort länken mellan två cacheminnen och stoppa geo-replikering klickar du på **ta bort länkning av cacheminnen** från bladet **geo-replikering** .
    
    ![Avlänka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    När avlänkningen är klar är den sekundära cachen tillgänglig för både läsning och skrivning.

>[!NOTE]
>När länken geo-Replication tas bort finns replikerade data från den primära länkade cachen kvar i den sekundära cachen.
>
>

## <a name="geo-replication-faq"></a>Vanliga frågor och svar om geo-replikering

- [Kan jag använda geo-replikering med en standard-eller Basic-nivå i cacheminnet?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Är mitt cacheminne tillgängligt för användning under länkningen eller borttagningen av länken?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan jag länka fler än två cacheminnen tillsammans?](#can-i-link-more-than-two-caches-together)
- [Kan jag länka två cacheminnen från olika Azure-prenumerationer?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan jag länka två cacheminnen med olika storlekar?](#can-i-link-two-caches-with-different-sizes)
- [Kan jag använda geo-replikering med klustring aktiverat?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan jag använda geo-replikering med mina cacheminnen i ett virtuellt nätverk?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Vad är replikeringsschema för Redis geo-replikering?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hur länge tar geo-replikeringen att replikera?](#how-long-does-geo-replication-replication-take)
- [Garanteras återställnings punkten för replikering?](#is-the-replication-recovery-point-guaranteed)
- [Kan jag använda PowerShell eller Azure CLI för att hantera geo-replikering?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Hur mycket kostar det att replikera mina data i Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Varför misslyckades åtgärden när jag försökte ta bort mitt länkade cacheminne?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Vilken region ska jag använda för min sekundära länkade cache?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hur fungerar växling vid fel i det sekundära länkade cacheminnet?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan jag använda geo-replikering med en standard-eller Basic-nivå i cacheminnet?

Nej, geo-replikering är bara tillgängligt för cacheminnen på Premium-nivån.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Är mitt cacheminne tillgängligt för användning under länkningen eller borttagningen av länken?

- Vid länkning förblir det primära länkade cacheminnet tillgängligt när länkningen är klar.
- Vid länkning är den sekundära länkade cachen inte tillgänglig förrän länkningen har slutförts.
- När du tar bort länkar är båda cacheminnena tillgängliga medan borttagnings processen slutförs.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan jag länka fler än två cacheminnen tillsammans?

Nej, du kan bara länka två cacheminnen tillsammans.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan jag länka två cacheminnen från olika Azure-prenumerationer?

Nej, båda cacheminnena måste finnas i samma Azure-prenumeration.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan jag länka två cacheminnen med olika storlekar?

Ja, så länge den sekundära länkade cachen är större än det primära länkade cacheminnet.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan jag använda geo-replikering med klustring aktiverat?

Ja, så länge båda cacheminnena har samma antal Shards.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan jag använda geo-replikering med mina cacheminnen i ett virtuellt nätverk?

Ja, geo-replikering av cacheminnen i virtuella nätverk stöds med varningar:

- Geo-replikering mellan cacheminnen i samma VNET stöds.
- Geo-replikering mellan cacheminnen i olika virtuella nätverk stöds också.
  - Om virtuella nätverk finns i samma region kan du ansluta dem med [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) eller en [VPN gateway VNet-till-VNET-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Om virtuella nätverk finns i olika regioner stöds inte geo-replikering med VNET-peering på grund av en begränsning med Basic Internal belastningsutjämnare. Mer information om begränsningar för VNET-peering finns i [Virtual Network-peering-krav och begränsningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Den rekommenderade lösningen är att använda en VPN Gateway VNET-till-VNET-anslutning.

Med hjälp av [den här Azure-mallen](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)kan du snabbt distribuera två geo-replikerade cacheminnen i ett VNet som är anslutet till en VPN gateway VNet-till-VNET-anslutning.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Vad är replikeringsschema för Redis geo-replikering?

Replikeringen är kontinuerlig och asynkron och sker inte enligt ett särskilt schema. Alla skrivningar som görs till den primära replikeras omedelbart och replikeras asynkront på den sekundära.

### <a name="how-long-does-geo-replication-replication-take"></a>Hur länge tar geo-replikeringen att replikera?

Replikeringen är stegvis, asynkron och kontinuerlig och den tid som krävs skiljer sig inte mycket från svars tiden mellan regioner. Under vissa omständigheter kan det sekundära cacheminnet krävas för att utföra en fullständig synkronisering av data från den primära. Tiden för replikering i det här fallet är beroende av antalet faktorer som: Läs in på det primära cacheminnet, tillgänglig nätverks bandbredd och svars tid mellan regioner. Tiden för replikering har hittats för ett fullständigt 53 GB geo-replikerat par kan vara var som helst mellan 5 och 10 minuter.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garanteras återställnings punkten för replikering?

Persistence är inaktiverat för cacheminnen i ett geo-replikerat läge. Om ett geo-replikerat par är olänkat, t. ex. När en kund initierad redundansväxling, behåller den sekundära länkade cachen dess synkroniserade data upp till den tidpunkten. Ingen återställnings punkt garanteras i sådana situationer.

Om du vill hämta en återställnings punkt [exporterar](cache-how-to-import-export-data.md#export) du från något av cacheminnet. Du kan senare [Importera](cache-how-to-import-export-data.md#import) till den primära länkade cachen.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan jag använda PowerShell eller Azure CLI för att hantera geo-replikering?

Ja, geo-replikering kan hanteras med hjälp av Azure Portal, PowerShell eller Azure CLI. Mer information finns i PowerShell- [dokumenten](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) eller [Azure CLI-dokumenten](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hur mycket kostar det att replikera mina data i Azure-regioner?

När du använder geo-replikering replikeras data från den primära länkade cachen till den sekundära länkade cachen. Det kostar inget att överföra data om de två länkade cacheminnena finns i samma region. Om de två länkade cacheminnena finns i olika regioner, är data överförings avgiften nätverks utgångs kostnaden för data som flyttas över vilken region som än är. Mer information finns i [pris information om bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Varför misslyckades åtgärden när jag försökte ta bort mitt länkade cacheminne?

Geo-replikerade cacheminnen och deras resurs grupper kan inte tas bort när du har länkat tills du tar bort länken för geo-replikering. Om du försöker ta bort resurs gruppen som innehåller ett eller båda av de länkade cacheminnen tas de andra resurserna i resurs gruppen bort, men resurs gruppen stannar kvar i det `deleting` aktuella läget och eventuella länkade cacheminnen i resurs gruppen är i `running` läget. För att helt ta bort resurs gruppen och de länkade cacheminnena i den, tar du bort kopplingen mellan cacheminnena enligt beskrivningen i [ta bort en geo-replikeringslänk](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Vilken region ska jag använda för min sekundära länkade cache?

I allmänhet rekommenderas det att ditt cacheminne finns i samma Azure-region som det program som har åtkomst till den. För program med separata primära och reserv regioner rekommenderar vi att dina primära och sekundära cacheminnen finns i samma regioner. Mer information om kopplade regioner finns i [metod tips – Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hur fungerar växling vid fel i det sekundära länkade cacheminnet?

Automatisk redundans i Azure-regioner stöds inte för geo-replikerade cacheminnen. I ett haveri återställnings scenario bör kunderna ta upp hela program stacken på ett samordnat sätt i sin säkerhets kopierings region. Att låta enskilda program komponenter bestämma när de ska växla till sina egna säkerhets kopieringar kan påverka prestanda negativt. En av de främsta fördelarna med Redis är att det är en mycket låg latens lagring. Om kundens huvud program finns i en annan region än dess cacheminne skulle den extra tiden för fördröjningen ha en märkbar inverkan på prestanda. Av den anledningen undviker vi att det inte går att redundansväxla automatiskt på grund av tillfälliga tillgänglighets problem.

Om du vill starta en redundansväxling som initieras av en kund måste du först ta bort kopplingen till cacheminnet. Ändra sedan din Redis-klient så att den använder anslutnings slut punkten för den sekundära cachen (tidigare länkade). När de två cacheminnena är olänkade blir den sekundära cachen en vanlig read-write-cache igen och accepterar begär Anden direkt från Redis-klienter.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure cache för Redis Premium-nivån](cache-premium-tier-intro.md).
