---
title: "Konfigurera ett virtuellt nätverk för Premium Azure Redis-Cache | Microsoft Docs"
description: "Lär dig hur du skapar och hanterar virtuella nätverk stöd för din Premium-nivån Azure Redis-Cache-instanser"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 74ec104bebec2004a8b7116865c2394c02b12638
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Så här konfigurerar du stöd för virtuella nätverk för Premium Azure Redis-Cache
Azure Redis-Cache har olika cache-erbjudanden som ger flexibilitet vid val av cachestorlek och funktioner, inklusive funktioner för Premium-nivån, till exempel klustring, beständiga och stöd för virtuella nätverk. Ett virtuellt nätverk är ett privat nätverk i molnet. När en instans av Azure Redis-Cache har konfigurerats med ett VNet, är inte offentligt adresserbara och kan endast nås från virtuella datorer och program inom VNet. Den här artikeln beskriver hur du konfigurerar virtual network-stöd för en premium Azure Redis-Cache-instans.

> [!NOTE]
> Azure Redis-Cache har stöd för både klassiska och Resource Manager VNets.
> 
> 

Information om andra cache premium-funktioner finns [introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Varför VNet?
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) distribution ger förbättrad säkerhet och isolering för Azure Redis-Cache, samt undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk
Stöd för virtuella nätverk (VNet) har konfigurerats på den **nytt Redis-Cache** bladet under skapande av cachen. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

När du har valt en premium-prisnivån, kan du konfigurera Redis VNet integration genom att välja ett virtuellt nätverk som finns i samma prenumeration och plats som ditt cacheminne. Om du vill använda ett nytt virtuellt nätverk, skapa den första genom att följa stegen i [skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [skapa ett virtuellt nätverk (klassiska) med hjälp av Azure portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) och återgå sedan till **Nytt Redis-Cache** bladet för att skapa och konfigurera din premium-cache.

Om du vill konfigurera VNet för ditt nya cacheminne, klickar du på **virtuellt nätverk** på den **nytt Redis-Cache** , och välj det önskade virtuella nätverket från den nedrullningsbara listan.

![Virtuellt nätverk][redis-cache-vnet]

Välj önskad undernät från det **undernät** nedrullningsbara listan, och ange den önskade **statisk IP-adress**. Om du använder ett klassiskt virtuellt nätverk i **statisk IP-adress** fältet är valfritt och om inget anges väljs en från det valda undernätet.

> [!IMPORTANT]
> När du distribuerar ett Azure Redis-Cache till en Resource Manager-VNet, måste cachen vara i ett dedikerat undernät som innehåller några resurser förutom Azure Redis-Cache-instanser. Om ett försök görs att distribuera ett Azure Redis-Cache till en Resource Manager VNet till ett undernät som innehåller andra resurser, misslyckas distributionen.
> 
> 

![Virtuellt nätverk][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure reserverar vissa IP-adresser inom varje undernät, och dessa adresser kan inte användas. De första och sista IP-adresserna undernät är reserverade för överensstämmelse med protokollet, tillsammans med tre flera adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med hjälp av IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Förutom IP-adresser som används av den virtuella Azure-infrastrukturen, varje Redis-instansen i undernät använder två IP-adresser per Fragmentera och en ytterligare IP-adress för belastningsutjämnaren. En icke-klustrade cache anses ha en Fragmentera.
> 
> 

När cachen har skapats kan du visa konfigurationen för det virtuella nätverket genom att klicka på **virtuellt nätverk** från den **resurs menyn**.

![Virtuellt nätverk][redis-cache-vnet-info]

För att ansluta till din Azure Redis-cache-instans när du använder ett virtuellt nätverk, anger du värdnamnet för ditt cacheminne i anslutningssträngen som visas i följande exempel:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis-Cache VNet vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om Azure Redis-Cache skalning.

* [Vilka är några vanliga problem med Azure Redis-Cache och Vnet?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Hur kan jag bekräfta att min cache fungerar i ett virtuellt nätverk?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Kan jag använda Vnet med en standard- eller basic-cache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Varför skapa ett Redis-cache misslyckas i vissa undernät, men inte andra?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Vad är utrymmeskraven på undernät?](#what-are-the-subnet-address-space-requirements)
* [Fungerar alla cachefunktioner när värd en cachelagring i ett virtuellt nätverk?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Vilka är några vanliga problem med Azure Redis-Cache och Vnet?
När Azure Redis-Cache finns i ett virtuellt nätverk, används portarna i följande tabeller. 

>[!IMPORTANT]
>Om portar i tabellerna nedan blockeras fungerar cachen inte korrekt. Med en eller flera av de här portarna blockeras är de vanligaste felkonfiguration problemet när du använder Azure Redis-Cache i ett VNet.
> 
> 

- [Utgående portkrav](#outbound-port-requirements)
- [Krav för inkommande portar](#inbound-port-requirements)

### <a name="outbound-port-requirements"></a>Utgående portkrav

Det finns sju krav för utgående port.

- Om önskade, alla utgående anslutningar till internet kan göras via en klient lokalt granskning enhet.
- Tre av portarna som dirigerar trafik till Azure-slutpunkter servicing Azure Storage- och Azure DNS.
- Återstående portintervall och för intern kommunikation för Redis-undernät. Inga undernät NSG-regler krävs för intern kommunikation för Redis-undernät.

| Portar | Riktning | Transportprotokoll | Syfte | Lokala IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Utgående |TCP |Redis-beroenden på Azure Storage/PKI (Internet) | (Redis undernät) |* |
| 53 |Utgående |TCP/UDP |Redis-beroenden i DNS (Internet/VNet) | (Redis undernät) |* |
| 8443 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) | (Redis undernät) |
| 10221-10231 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) | (Redis undernät) |
| 20226 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 13000-13999 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 15000-15999 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |


### <a name="inbound-port-requirements"></a>Krav för inkommande portar

Det finns åtta inkommande port intervallet krav. Inkommande begäranden i dessa områden är inkommande trafik från andra tjänster i samma virtuella nätverk eller intern kommunikation för Redis-undernät.

| Portar | Riktning | Transportprotokoll | Syfte | Lokala IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkommande |TCP |Klientkommunikation till Redis Azure belastningsutjämning | (Redis undernät) |Virtual Network, Azure Load Balancer |
| 8443 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 8500 |Inkommande |TCP/UDP |Azure belastningsutjämning | (Redis undernät) |Azure Load Balancer |
| 10221-10231 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät), Azure belastningsutjämnare |
| 13000-13999 |Inkommande |TCP |Klientkommunikation till Redis-kluster, Azure belastningsutjämning | (Redis undernät) |Virtual Network, Azure Load Balancer |
| 15000-15999 |Inkommande |TCP |Klientkommunikation till Redis-kluster, Azure läsa in belastningsutjämning | (Redis undernät) |Virtual Network, Azure Load Balancer |
| 16001 |Inkommande |TCP/UDP |Azure belastningsutjämning | (Redis undernät) |Azure Load Balancer |
| 20226 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |

### <a name="additional-vnet-network-connectivity-requirements"></a>Ytterligare anslutningskrav för virtuella nätverk

Det finns anslutningskrav för Azure Redis-Cache som inte kanske ursprungligen uppfyllas i ett virtuellt nätverk. Azure Redis-Cache kräver följande ska fungera korrekt när den används i ett virtuellt nätverk.

* Utgående nätverksanslutning till Azure Storage slutpunkter över hela världen. Detta inkluderar slutpunkter finns i samma region som Azure Redis-Cache-instans, samt lagring slutpunkter finns i **andra** Azure-regioner. Azure Storage-slutpunkter lösa under följande DNS-domäner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*, och *file.core.windows.net*. 
* Utgående nätverksanslutning till *ocsp.msocsp.com*, *mscrl.microsoft.com*, och *crl.microsoft.com*. Den här anslutningen krävs för att stödja SSL-funktioner.
* DNS-konfigurationen för det virtuella nätverket måste kunna matcha alla slutpunkter och domäner som nämns i tidigare återställningspunkter. Dessa DNS-krav kan uppfyllas genom att säkerställa att ett giltigt DNS-infrastrukturen är konfigurerad och underhålls för det virtuella nätverket.
* Utgående nätverksanslutning till följande Azure-övervakning slutpunkter, vilket löser under följande DNS-domäner: shoebox2 black.shoebox2.metrics.nsatc.net Nord-prod2.prod2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , shoebox2 red.shoebox2.metrics.nsatc.net Öst-prod2.prod2.metrics.nsatc.net azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hur kan jag bekräfta att min cache fungerar i ett virtuellt nätverk?

>[!IMPORTANT]
>När du ansluter till en Azure Redis-Cache-instans som är värd för ett virtuellt nätverk kan måste cacheklienter vara i samma virtuella nätverk, inklusive testprogrammen eller pinga diagnosverktyg.
>
>

När portkraven har konfigurerats enligt beskrivningen i föregående avsnitt, kan du kontrollera att ditt cacheminne fungerar genom att utföra följande steg.

- [Starta om](cache-administration.md#reboot) alla cachenoder. Om alla nödvändiga cacheberoenden inte kan nås (enligt beskrivningen i [inkommande portkrav](cache-how-to-premium-vnet.md#inbound-port-requirements) och [utgående portkrav](cache-how-to-premium-vnet.md#outbound-port-requirements)), cachen inte kan starta korrekt.
- När cachenoderna har startats om (som rapporteras av Cachestatus i Azure-portalen) kan du utföra följande test:
  - Pinga cache-slutpunkten (med port 6380) från en dator som ligger inom samma virtuella nätverk som cache, med hjälp av [tcping](https://www.elifulkerson.com/projects/tcping.php). Exempel:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Om den `tcping` verktyget rapporterar att porten är öppen, cachen är tillgängliga för anslutningen från klienter i virtuella nätverk.

  - Ett annat sätt att testa är att skapa en test-cacheklient (som kan vara ett enkelt konsolprogram med StackExchange.Redis) som ansluter till cacheminnet och lägger till och hämtar några objekt från cacheminnet. Installera klienten exempelprogrammet till en virtuell dator som är i samma virtuella nätverk som cacheminnet och köra den för att verifiera anslutningen till cachen.


### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan jag använda Vnet med en standard- eller basic-cache?
Vnet kan endast användas med premium-cache.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Varför skapa ett Redis-cache misslyckas i vissa undernät, men inte andra?
Om du distribuerar ett Azure Redis-Cache till en Resource Manager-VNet måste cachen vara i ett dedikerat undernät som innehåller inga andra resurstypen. Om ett försök görs att distribuera ett Azure Redis-Cache till en Resource Manager VNet-undernät som innehåller andra resurser, misslyckas distributionen. Du måste ta bort befintliga resurser i undernätet innan du kan skapa ett nytt Redis-cache.

Du kan distribuera flera typer av resurser till ett klassiskt virtuellt nätverk som du har tillräckligt med tillgängliga IP-adresser.

### <a name="what-are-the-subnet-address-space-requirements"></a>Vad är utrymmeskraven på undernät?
Azure reserverar vissa IP-adresser inom varje undernät, och dessa adresser kan inte användas. De första och sista IP-adresserna undernät är reserverade för överensstämmelse med protokollet, tillsammans med tre flera adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med hjälp av IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom IP-adresser som används av den virtuella Azure-infrastrukturen, varje Redis-instansen i undernät använder två IP-adresser per Fragmentera och en ytterligare IP-adress för belastningsutjämnaren. En icke-klustrade cache anses ha en Fragmentera.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungerar alla cachefunktioner när värd en cachelagring i ett virtuellt nätverk?
När din cache är en del av ett virtuellt nätverk kan bara klienter i virtuella nätverk kan komma åt cachen. Följande funktioner för hantering av cache fungerar därför inte just nu.

* Redis-konsolen - eftersom Redis-konsolen körs i din lokala webbläsare som är utanför det virtuella nätverket, det går inte att ansluta till ditt cacheminne.

## <a name="use-expressroute-with-azure-redis-cache"></a>Använda ExpressRoute med Azure Redis-Cache
Kunder kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) krets på sina virtuella nätverksinfrastrukturen därför utöka sina lokala nätverk till Azure. 

Som standard en nyligen skapade ExpressRoute-krets utför inte Tvingad tunneling (annons med en standardväg 0.0.0.0/0) på ett virtuellt nätverk. Därför utgående Internetanslutning är tillåtna direkt från VNET och klientprogram ska kunna ansluta till andra Azure-slutpunkter inklusive Azure Redis-Cache.

Men en gemensam konfiguration av customer är att använda Tvingad tunneltrafik (annonsera en standardväg) som tvingar utgående Internet-trafiken flöda lokalt i stället. Den här trafikflöde bryter anslutningen till Azure Redis-Cache om utgående trafik sedan blockeras lokalt så att den Azure Redis-Cache-instansen inte är kan kommunicera med dess beroenden.

Lösningen är att definiera en (eller flera) användardefinierade vägar (udr: er) för det undernät som innehåller Azure Redis-Cache. En UDR definierar undernät-specifika vägar som ska användas i stället för standardväg.

Om möjligt bör du använder följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och som standard kraft tunnlar all utgående trafik lokalt.
* UDR tillämpad på undernätet som innehåller Azure Redis-Cache definierar 0.0.0.0/0 med en fungerande väg för TCP/IP-trafik till det offentliga internet; till exempel genom att nästa hopptyp ”Internet”.

Den kombinerade effekten av dessa steg är att undernätverksnivån UDR företräde framför den ExpressRoute Tvingad tunneltrafik tillse utgående Internetåtkomst från Azure Redis-Cache.

Ansluter till en Azure Redis-Cache-instans från ett lokalt program som använder ExpressRoute är inte en typisk användningsscenariot på grund av prestandaskäl (för bästa prestanda Azure Redis-Cache klienter måste vara i samma region som Azure Redis-Cache).

>[!IMPORTANT] 
>De vägar som definierats i en UDR **måste** vara specifikt nog att företräde framför alla vägarna som annonseras av ExpressRoute-konfigurationen. I följande exempel använder adressintervallet bred 0.0.0.0/0 och som sådan kan potentiellt av misstag åsidosättas av flödet annonser med hjälp av mer specifika adressintervall.

>[!WARNING]  
>Azure Redis-Cache stöds inte med ExpressRoute-konfigurationer som **felaktigt cross-annonsera vägar från offentlig peering sökvägen att privat peering sökväg**. ExpressRoute-konfigurationer som har konfigurerats, offentlig peering får vägannonser från Microsoft för ett stort antal Microsoft Azure IP-adressintervall. Om dessa adressintervall felaktigt cross-annonseras i privat peering sökväg, är resultatet att alla utgående paket från Azure Redis-Cache-instansens undernät är felaktigt kraft tunnlar kundens lokala nätverkets infrastruktur . Det här nätverket flödet bryts Azure Redis-Cache. Lösning på problemet är att stoppa mellan reklam vägar från offentlig peering sökvägen att privat peering sökväg.


Bakgrundsinformation om användardefinierade vägar är tillgänglig i det här [översikt](../virtual-network/virtual-networks-udr-overview.md).

Mer information om ExpressRoute finns [teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder flera funktioner som cache premium.

* [Introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

