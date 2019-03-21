---
title: Konfigurera ett virtuellt nätverk för en Premium Azure Cache för Redis | Microsoft Docs
description: Lär dig att skapa och hantera Virtual Network-stöd för din Premium-nivån Azure Cache för Redis-instanser
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: d4b8fd6ccb3fc7cb2627d4bd3e103239181e4d9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994393"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Så här konfigurerar du stöd för Virtual Network för Premium Azure Cache för Redis
Azure Redis-Cache har olika cachefunktioner som ger flexibilitet i valet av cachestorlek och funktioner, inklusive funktioner på Premiumnivå som klustring, persistence och stöd för virtuella nätverk. Ett virtuellt nätverk är ett privat nätverk i molnet. När en Azure-Cache för Redis-instans är konfigurerad med ett virtuellt nätverk, är inte offentligt adresserbar och kan bara kommas åt från virtuella datorer och program i det virtuella nätverket. Den här artikeln beskriver hur du konfigurerar virtual network-stöd för premium Azure Cache för Redis-instans.

> [!NOTE]
> Azure Redis-Cache har stöd för både klassiska och Resource Manager-VNets.
> 
> 

Information om andra premiumfunktioner för cache finns i [introduktion till Azure Cache för Premium-nivån Redis](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Varför VNet?
[Azure-nätverk (VNet)](https://azure.microsoft.com/services/virtual-network/) distributionen tillhandahåller förbättrad säkerhet och isolering för din Azure-Cache för Redis, samt undernät, åtkomstkontrollprinciper och andra funktioner för att ytterligare begränsa åtkomsten.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk
Stöd för Virtual Network (VNet) är konfigurerad på den **nya Azure-Cache för Redis** bladet när cache skapas. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

När du har valt en premium-prisnivån kan konfigurera du Redis-VNet-integrering genom att välja ett virtuellt nätverk som tillhör samma prenumeration och plats som cacheminnet. Om du vill använda ett nytt virtuellt nätverk, skapa den första genom att följa stegen i [skapa ett virtuellt nätverk med Azure portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) eller [skapa ett virtuellt nätverk (klassisk) med hjälp av Azure portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) och återgå sedan till **Nya Azure-Cache för Redis** bladet för att skapa och konfigurera din premium-cache.

Om du vill konfigurera det virtuella nätverket för ditt nya cacheminne, klickar du på **virtuellt nätverk** på den **nya Azure-Cache för Redis** bladet och välj det önskade virtuella nätverket från den nedrullningsbara listan.

![Virtuellt nätverk][redis-cache-vnet]

Välj önskad undernät från den **undernät** nedrullningsbara listan, och ange den önskade **statisk IP-adress**. Om du använder ett klassiskt virtuellt nätverk i **statisk IP-adress** fältet är valfritt och om inget anges en väljs från det valda undernätet.

> [!IMPORTANT]
> När du distribuerar en Azure Cache för Redis till ett Resource Manager-VNet måste cacheminnet vara i ett dedikerat undernät som innehåller inga resurser förutom Azure Cache Redis-instanser. Om ett försök görs att distribuera en Azure Cache för Redis till ett Resource Manager-VNet till ett undernät som innehåller andra resurser kan distributionen misslyckas.
> 
> 

![Virtuellt nätverk][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure reserverar vissa IP-adresser i varje undernät och det går inte att använda dessa adresser. Första och sista IP-adresserna i undernäten är reserverade för protokollöverensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Förutom de IP-adresser som används av infrastrukturen som virtuella Azure-nätverket, varje Redis-instansen i undernät använder två IP-adresser per shard och en ytterligare IP-adress för belastningsutjämnaren. En icke-klustrade cache anses ha en shard.
> 
> 

När cachen har skapats kan du visa konfigurationen för det virtuella nätverket genom att klicka på **virtuellt nätverk** från den **resursmenyn**.

![Virtuellt nätverk][redis-cache-vnet-info]

Ange värdnamnet för ditt cacheminne i anslutningssträngen för att ansluta till din Azure-Cache för Redis-instans när du använder ett virtuellt nätverk, som visas i följande exempel:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Azure Cache för Redis VNet vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om Azure Cache för att skala Redis.

* Vilka är några vanliga problem med konfigurationsfel för Azure Cache för Redis och virtuella nätverk?
* [Hur kan jag kontrollera att mitt cacheminne fungerar i ett virtuellt nätverk?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* När du försöker ansluta till mitt Azure Cache för Redis i ett virtuellt nätverk, varför får jag ett felmeddelande om Fjärrcertifikatet är ogiltigt?
* [Kan jag använda virtuella nätverk med en standard eller basic-cache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Varför skapa en Azure Cache för Redis misslyckas i vissa undernät, men inte för andra?
* [Vad är utrymmeskraven för undernät?](#what-are-the-subnet-address-space-requirements)
* [Fungerar alla cache-funktioner när du har en cache i ett virtuellt nätverk?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Vilka är några vanliga problem med konfigurationsfel för Azure Cache för Redis och virtuella nätverk?
När Azure Cache för Redis finns i ett virtuellt nätverk, används portarna i följande tabeller. 

>[!IMPORTANT]
>Om portarna i följande tabeller är blockerade fungerar cachen inte korrekt. Är att ha en eller flera av de här portarna blockeras vanligaste felkonfiguration problemet när du använder Azure Cache för Redis i ett virtuellt nätverk.
> 
> 

- [Krav för utgående port](#outbound-port-requirements)
- [Krav för inkommande port](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Krav för utgående port

Det finns sju krav för utgående port.

- Alla utgående anslutningar till internet kan göras via en klient är en lokal granskning enheten.
- Tre av portarna som dirigerar trafik till Azure-slutpunkter vilka Azure Storage och Azure DNS.
- Återstående portintervall och för intern kommunikation för Redis-undernät. Inga undernät NSG-regler måste anges för intern kommunikation för Redis-undernät.

| Portar | Riktning | Transport-protokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Utgående |TCP |Redis beroenden på Azure Storage/PKI (Internet) | (Redis undernät) |* |
| 53 |Utgående |TCP/UDP |Redis beroenden av DNS (Internet/VNet) | (Redis undernät) |* |
| 8443 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) | (Redis undernät) |
| 10221-10231 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) | (Redis undernät) |
| 20226 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 13000-13999 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 15000-15999 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 6379-6380 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |


#### <a name="inbound-port-requirements"></a>Krav för inkommande port

Det finns åtta inkommande port intervallet krav. Inkommande begäranden i dessa områden är inkommande från andra tjänster som finns i samma virtuella nätverk eller intern kommunikation för Redis-undernät.

| Portar | Riktning | Transport-protokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkommande |TCP |Klientkommunikation till Redis, Azure belastningsutjämning | (Redis undernät) | (Redis subnet), Virtual Network, Azure Load Balancer |
| 8443 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 8500 |Inkommande |TCP/UDP |Azure för belastningsutjämning | (Redis undernät) |Azure Load Balancer |
| 10221-10231 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät), Azure Load Balancer |
| 13000-13999 |Inkommande |TCP |Klientkommunikation till Redis-kluster, Azure belastningsutjämning | (Redis undernät) |Virtual Network, Azure Load Balancer |
| 15000-15999 |Inkommande |TCP |Klientkommunikation till Redis-kluster, Azure läsa in belastningsutjämning | (Redis undernät) |Virtual Network, Azure Load Balancer |
| 16001 |Inkommande |TCP/UDP |Azure för belastningsutjämning | (Redis undernät) |Azure Load Balancer |
| 20226 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Ytterligare anslutningskrav för virtuellt nätverk

Det finns anslutningskrav för Azure Cache för Redis som inte kanske inledningsvis uppfyllas i ett virtuellt nätverk. Azure Redis-Cache kräver alla följande ska fungera korrekt när den används i ett virtuellt nätverk.

* Utgående nätverksanslutning till Azure Storage-slutpunkter i hela världen. Detta inkluderar slutpunkter som finns i samma region som Azure Cache för Redis-instans, samt storage-slutpunkter som finns i **andra** Azure-regioner. Azure Storage-slutpunkter lösa under följande DNS-domäner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*, och *file.core.windows.net*. 
* Utgående nätverksanslutning till *ocsp.msocsp.com*, *mscrl.microsoft.com*, och *crl.microsoft.com*. Den här anslutningen krävs för att stödja SSL-funktioner.
* DNS-konfiguration för det virtuella nätverket måste kunna lösa alla slutpunkter och domäner som nämns i de tidigare punkterna. Dessa DNS-krav kan uppfyllas genom att kontrollera att en giltig DNS-infrastruktur konfigurerad och underhållen för det virtuella nätverket.
* Utgående nätverksanslutning till följande Azure Monitoring slutpunkter, vilket löser under följande DNS-domäner: shoebox2 black.shoebox2.metrics.nsatc.net Nord-prod2.prod2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , shoebox2 red.shoebox2.metrics.nsatc.net östra prod2.prod2.metrics.nsatc.net azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hur kan jag kontrollera att mitt cacheminne fungerar i ett virtuellt nätverk?

>[!IMPORTANT]
>När du ansluter till en Azure-Cache för Redis-instans som är värd för ett virtuellt nätverk, måste cache-klienter finnas i samma virtuella nätverk eller i ett virtuellt nätverk med VNET-peering aktiverad. Detta inkluderar test program eller pingar diagnostikverktyg. Oavsett var klientprogrammet finns måste nätverkssäkerhetsgrupper konfigureras så att klientens nätverkstrafik tillåts att nå Redis-instans.
>
>

När portkraven har konfigurerats enligt beskrivningen i föregående avsnitt, kan du kontrollera att din cache fungerar genom att utföra följande steg.

- [Starta om](cache-administration.md#reboot) alla cachenoder. Om alla nödvändiga cacheberoenden inte kan nås (enligt beskrivningen i [inkommande portkraven](cache-how-to-premium-vnet.md#inbound-port-requirements) och [utgående portkraven](cache-how-to-premium-vnet.md#outbound-port-requirements)), cachen kommer inte att kunna starta om har.
- När cachenoderna har startats om (som rapporterats av status för cache i Azure portal), kan du utföra följande test:
  - Pinga cache-slutpunkten (via port 6380) från en dator som ligger inom samma virtuella nätverk som cache, med hjälp av [tcping](https://www.elifulkerson.com/projects/tcping.php). Exempel:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Om den `tcping` verktyget rapporterar att porten är öppen, cachen är tillgängliga för anslutningen från klienter i det virtuella nätverket.

  - Ett annat sätt att testa är att skapa en test-cacheklient (vilket kan orsaka ett enkelt konsolprogram med StackExchange.Redis) som ansluter till cachen och lägger till och hämtar vissa objekt från cachen. Installera klienten exempelprogrammet till en virtuell dator som är i samma virtuella nätverk som cache och kör den för att verifiera anslutningen till cachen.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>När du försöker ansluta till mitt Azure Cache för Redis i ett virtuellt nätverk, varför får jag ett felmeddelande om Fjärrcertifikatet är ogiltigt?

När du försöker ansluta till en Azure Cache för Redis i ett virtuellt nätverk kan du se ett verifieringsfel för certifikat som detta:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Orsaken kan vara du ansluter till värden av IP-adressen. Vi rekommenderar att du använder värdnamnet. Med andra ord, använder du följande:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Undvik att använda IP-adressen liknar följande anslutningssträng:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Om det inte går att matcha DNS-namnet, vissa klientbibliotek innehåller konfigurationsalternativ som `sslHost` som tillhandahålls av StackExchange.Redis-klienten. På så sätt kan du åsidosätta värdnamn som används för validering av klientcertifikat. Exempel:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan jag använda virtuella nätverk med en standard eller basic-cache?
Virtuella nätverk kan bara användas med premium-cacheminnen.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Varför skapa en Azure Cache för Redis misslyckas i vissa undernät, men inte för andra?
Om du distribuerar en Azure Cache för Redis till ett Resource Manager-VNet måste cacheminnet vara i ett dedikerat undernät som innehåller inga andra resurstyp. Vid ett försök att distribuera en Azure Cache för Redis till ett Resource Manager-VNet-undernät som innehåller andra resurser kan distributionen misslyckas. Du måste ta bort befintliga resurser i undernätet innan du kan skapa en ny Azure-Cache för Redis.

Du kan distribuera flera typer av resurser till ett klassiskt virtuellt nätverk som du har tillräckligt med tillgängliga IP-adresser.

### <a name="what-are-the-subnet-address-space-requirements"></a>Vad är utrymmeskraven för undernät?
Azure reserverar vissa IP-adresser i varje undernät och det går inte att använda dessa adresser. Första och sista IP-adresserna i undernäten är reserverade för protokollöverensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av infrastrukturen som virtuella Azure-nätverket, varje Redis-instansen i undernät använder två IP-adresser per shard och en ytterligare IP-adress för belastningsutjämnaren. En icke-klustrade cache anses ha en shard.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungerar alla cache-funktioner när du har en cache i ett virtuellt nätverk?
När din cache är en del av ett virtuellt nätverk kan endast klienter i det virtuella nätverket kan komma åt cachen. Därför kan fungerar de följande funktionerna för cache inte just nu.

* Redis-konsol - eftersom Redis-konsolen körs i din lokala webbläsare, vilket är utanför det virtuella nätverket kan den inte kan ansluta till ditt cacheminne.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Använd ExpressRoute med Azure Cache för Redis

Kunder kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kretsen till sina virtuell nätverksinfrastruktur, vilket utöka sina lokala nätverk till Azure. 

Som standard en nyligen skapade ExpressRoute-krets utför inte Tvingad tunneltrafik (annons för en standardväg 0.0.0.0/0) på ett virtuellt nätverk. Därför utgående Internet-anslutning är tillåtna direkt från det virtuella nätverket och klientprogram ska kunna ansluta till andra Azure-slutpunkter, inklusive Azure Cache för Redis.

Men en vanlig kund-konfiguration är att använda Tvingad tunneltrafik (annonsera en standardväg) som tvingar utgående Internet-trafiken flöda lokalt i stället. Det här flödet i nätverkstrafiken bryter anslutningen med Azure Cache för Redis om den utgående trafiken är sedan blockeras på plats så att Azure Cache för Redis-instans inte kan kommunicera med dess beroenden.

Lösningen är att definiera en (eller mer) användardefinierade vägar (Udr) i det undernät som innehåller Azure Cache för Redis. En UDR definierar undernät-specifika vägar som kommer att användas i stället för standardväg.

Om det är möjligt rekommenderar vi att du använder du följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och som standard tunnlar tvinga all utgående trafik lokalt.
* Den användardefinierade vägen som tillämpas på undernät som innehåller Azure Cache för Redis definierar 0.0.0.0/0 med en fungerande väg för TCP/IP-trafik till offentliga internet. genom att till exempel nästa hopptyp ”Internet”.

Den kombinerade effekten av de här stegen är att undernätverksnivån UDR har företräde framför ExpressRoute Tvingad tunneltrafik, vilket säkerställer utgående Internetåtkomst från Azure Cache för Redis.

Ansluter till en Azure-Cache för Redis-instans från ett lokalt är program med hjälp av ExpressRoute inte ett normalt scenario på grund av prestandaskäl (för bästa prestanda Azure Cache för Redis-klienter ska vara i samma region som Azure Cache för Redis) .

>[!IMPORTANT] 
>Vägarna som definieras i en UDR **måste** vara tillräckligt specifika för att få företräde framför eventuella vägar som annonseras av ExpressRoute-konfigurationen. I följande exempel använder det breda adressintervallet 0.0.0.0/0 och därför kan eventuellt åsidosättas av misstag av vägannonseringar som använder mer specifika adressintervall.

>[!WARNING]  
>Azure Redis-Cache stöds inte med ExpressRoute-konfigurationer som **felaktigt korsannonserar vägar från den offentliga peering-sökvägen till den privata peering-sökvägen**. ExpressRoute-konfigurationer som har offentlig peering har konfigurerats, få vägannonseringar från Microsoft för ett stort antal Microsoft Azure IP-adressintervall. Om dessa adressintervallen korsannonseras felaktigt på den privata peering-sökvägen, är resultatet att alla utgående nätverkspaket från Azure-Cache för Redis-instans undernätet är felaktigt tvingas med tunneltrafik till en kunds lokala nätverk infrastruktur. Det här nätverksflödet delar Azure Cache för Redis. Lösning på problemet är att stoppa korsannonsering vägar från den offentliga peering-sökvägen till den privata peering-sökvägen.


Bakgrundsinformation om användardefinierade vägar finns i den här [översikt](../virtual-network/virtual-networks-udr-overview.md).

Mer information om ExpressRoute finns i [teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder mer premiumfunktioner för cache.

* [Introduktion till Azure Cache Redis Premium-nivån](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

