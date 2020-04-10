---
title: Konfigurera ett virtuellt nätverk - Premium Azure-cache för Redis
description: Lär dig hur du skapar och hanterar stöd för virtuellt nätverk för Azure-cachen på Premium-nivå för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2821ee637b2562b5287dd3d59cf943b3dcb7ef97
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010893"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Konfigurera stöd för virtuellt nätverk för en Premium Azure-cache för Redis
Azure Cache för Redis har olika cacheerbjudanden, vilket ger flexibilitet i valet av cachestorlek och funktioner, inklusive premium-nivåfunktioner som klustring, persistens och stöd för virtuella nätverk. Ett virtuella nätverk är ett privat nätverk i molnet. När en Azure Cache for Redis-instans är konfigurerad med ett virtuellt nätverk är den inte offentligt adresserbar och kan endast nås från virtuella datorer och program inom det virtuella nätverket. I den här artikeln beskrivs hur du konfigurerar stöd för virtuella nätverk för en premium Azure Cache för Redis-instans.

> [!NOTE]
> Azure Cache för Redis stöder både klassiska virtuella nätverk och resurshanteraren.
> 
> 

Information om andra premiumcachefunktioner finns i [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Varför VNet?
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) distribution ger förbättrad säkerhet och isolering för din Azure Cache för Redis, samt undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk
Stöd för virtuellt nätverk (VNet) har konfigurerats på bladet **Ny Azure-cache för Redis** när cacheminnet skapas. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

När du har valt en premiumprisnivå kan du konfigurera Redis VNet-integrering genom att välja ett virtuella nätverk som finns i samma prenumeration och plats som cacheminnet. Om du vill använda ett nytt virtuellt nätverk skapar du det först genom att följa stegen i [Skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/manage-virtual-network.md#create-a-virtual-network) eller [Skapa ett virtuellt nätverk (klassiskt) med hjälp av Azure-portalen](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) och sedan gå tillbaka till bladet **Ny Azure-cache för Redis för** att skapa och konfigurera premiumcachen.

Om du vill konfigurera det virtuella nätverket för den nya cachen klickar du på **Virtuellt nätverk** på bladet **Ny Azure-cache för Redis** och väljer önskat virtuellt nätverk i listrutan.

![Virtuellt nätverk][redis-cache-vnet]

Markera önskat undernät i listrutan **Undernät.**  Om du vill anger du en **statisk IP-adress**. Fältet **Statisk IP-adress** är valfritt, och om ingen anges väljs ett från det valda undernätet.

> [!IMPORTANT]
> När du distribuerar en Azure-cache för Redis till ett Resurshanteraren-nätverk måste cachen finnas i ett dedikerat undernät som inte innehåller några andra resurser förutom Azure-cache för Redis-instanser. Om ett försök görs att distribuera en Azure-cache för Redis till ett resurshanteraren-VNet till ett undernät som innehåller andra resurser, misslyckas distributionen.
> 
> 

![Virtuellt nätverk][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure reserverar vissa IP-adresser i varje undernät och dessa adresser kan inte användas. De första och sista IP-adresserna för undernäten är reserverade för protokollkonformance, tillsammans med ytterligare tre adresser som används för Azure-tjänster. Mer information finns i [Finns det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje Redis-instans i undernätet två IP-adresser per shard och ytterligare en IP-adress för belastningsutjämnaren. En cache som inte är grupperad anses ha en shard.
> 
> 

När cacheminnet har skapats kan du visa konfigurationen för det virtuella nätverket genom att klicka på **Virtuellt nätverk** på **resursmenyn**.

![Virtuellt nätverk][redis-cache-vnet-info]

Om du vill ansluta till Azure Cache for Redis-instansen när du använder ett virtuella nätverk anger du värdnamnet för cacheminnet i anslutningssträngen som visas i följande exempel:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Vanliga frågor och svar om Azure Cache för Redis VNet
Följande lista innehåller svar på vanliga frågor om Azure Cache for Redis-skalning.

* Vilka är några vanliga felkonfigurationsproblem med Azure Cache för Redis och VNets?
* [Hur kan jag kontrollera att mitt cacheminne fungerar i ett virtuellt nätverk?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* När du försöker ansluta till min Azure-cache för Redis i ett VNET, varför får jag ett felmeddelande om att fjärrcertifikatet är ogiltigt?
* [Kan jag använda virtuella nätverk med en standard eller grundläggande cache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Varför misslyckas det att skapa en Azure-cache för Redis i vissa undernät men inte i andra?
* [Vilka är kraven för undernätsadressutrymme?](#what-are-the-subnet-address-space-requirements)
* [Fungerar alla cachefunktioner när du är värd för en cache i ett VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Vilka är några vanliga felkonfigurationsproblem med Azure Cache för Redis och VNets?
När Azure Cache för Redis finns i ett virtuella nätverk används portarna i följande tabeller. 

>[!IMPORTANT]
>Om portarna i följande tabeller är blockerade kanske cachen inte fungerar korrekt. Att ha en eller flera av dessa portar blockerade är det vanligaste felkonfigurationsproblemet när du använder Azure Cache för Redis i ett virtuella nätverk.
> 
> 

- [Krav för utgående portar](#outbound-port-requirements)
- [Krav för inkommande portar](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Krav för utgående portar

Det finns nio krav på utgående port. Utgående begäranden i dessa intervall är antingen utgående till andra tjänster som krävs för att cachen ska fungera eller internt i Redis-undernätet för internodekommunikation. För geo-replikering finns det ytterligare utgående krav för kommunikation mellan undernät i den primära och sekundära cachen.

| Portar | Riktning | Transportprotokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Utgående |TCP |Återberoenden av Azure Storage/PKI (Internet) | (Redis undernät) |* |
| 443 | Utgående | TCP | Redis beroende av Azure Key Vault | (Redis undernät) | AzureKeyVault <sup>1</sup> |
| 53 |Utgående |TCP/UDP |Återberoenden av DNS (Internet/VNet) | (Redis undernät) | 168.63.129.16 och 169.254.169.254 <sup>2</sup> och alla anpassade DNS-servrar för undernätet <sup>3</sup> |
| 8443 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) | (Redis undernät) |
| 10221-10231 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) | (Redis undernät) |
| 20226 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 13000-13999 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 15000-15999 |Utgående |TCP |Intern kommunikation för Redis och Geo-Replication | (Redis undernät) |(Redis undernät) (Geo-replik peer-undernät) |
| 6379-6380 |Utgående |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |

<sup>1</sup> Du kan använda servicetaggen "AzureKeyVault" med Network Security Groups för Resurshanteraren.

<sup>2</sup> Dessa IP-adresser som ägs av Microsoft används för att adressera den virtuella värddatorn som betjänar Azure DNS.

<sup>3</sup> Behövs inte för undernät utan anpassad DNS-server eller nyare redis-cacheminnen som ignorerar anpassad DNS.

#### <a name="geo-replication-peer-port-requirements"></a>Krav på peerportar vid geo-replikering

Om du använder georeplication mellan cacheminnen i Azure Virtual Networks, observera att den rekommenderade konfigurationen är att avblockera portar 15000-15999 för hela undernätet i både inkommande och utgående riktningar till båda cacheminnena, så att alla replikkomponenter i undernätet kan kommunicera direkt med varandra även i händelse av en framtida geo-redundans.

#### <a name="inbound-port-requirements"></a>Krav för inkommande portar

Det finns åtta krav på inkommande portintervall. Inkommande begäranden i dessa intervall är antingen inkommande från andra tjänster som finns i samma virtuella nätverk eller interna till Redis-undernätskommunikationen.

| Portar | Riktning | Transportprotokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkommande |TCP |Klientkommunikation till Redis, Azure belastningsutjämning | (Redis undernät) | (Redis-undernät), virtuellt nätverk, Azure Load Balancer <sup>1</sup> |
| 8443 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |
| 8500 |Inkommande |TCP/UDP |Azure belastningsutjämning | (Redis undernät) |Azure Load Balancer |
| 10221-10231 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis-undernät), Azure Load Balancer |
| 13000-13999 |Inkommande |TCP |Klientkommunikation till Redis-kluster, Azure-belastningsutjämning | (Redis undernät) |Virtuellt nätverk, Azure belastningsutjämning |
| 15000-15999 |Inkommande |TCP |Klientkommunikation till Redis-kluster, Azure-belastningsutjämning och georeplikering | (Redis undernät) |Virtuellt nätverk, Azure Load Balancer, (Geo-replica peer-undernät) |
| 16001 |Inkommande |TCP/UDP |Azure belastningsutjämning | (Redis undernät) |Azure Load Balancer |
| 20226 |Inkommande |TCP |Intern kommunikation för Redis | (Redis undernät) |(Redis undernät) |

<sup>1</sup> Du kan använda Service Tag 'AzureLoadBalancer' (Resource Manager) (eller 'AZURE_LOADBALANCER' för klassisk) för att skapa NSG-reglerna.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Ytterligare krav på anslutning till VNET-nätverk

Det finns nätverksanslutningskrav för Azure Cache för Redis som kanske inte uppfylls från början i ett virtuellt nätverk. Azure Cache för Redis kräver att alla följande objekt fungerar korrekt när de används i ett virtuellt nätverk.

* Utgående nätverksanslutning till Azure Storage-slutpunkter över hela världen. Detta inkluderar slutpunkter som finns i samma region som Azure Cache för Redis-instans, samt lagringsslutpunkter som finns i **andra** Azure-regioner. Azure Storage-slutpunkter matchas under följande DNS-domäner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*och *file.core.windows.net*. 
* Utgående nätverksanslutning till *ocsp.msocsp.com,* *mscrl.microsoft.com*och *crl.microsoft.com*. Den här anslutningen behövs för att stödja TLS/SSL-funktioner.
* DNS-konfigurationen för det virtuella nätverket måste kunna lösa alla slutpunkter och domäner som nämns i de tidigare punkterna. Dessa DNS-krav kan uppfyllas genom att säkerställa att en giltig DNS-infrastruktur konfigureras och underhålls för det virtuella nätverket.
* Utgående nätverksanslutning till följande Azure Monitoring-slutpunkter, som löser under följande DNS-domäner: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hur kan jag kontrollera att mitt cacheminne fungerar i ett virtuellt nätverk?

>[!IMPORTANT]
>När du ansluter till en Azure-cache för Redis-instans som finns i ett VNET måste dina cacheklienter vara i samma virtuella nätverk eller i ett VNET med VNET-peering aktiverat inom samma Azure-region. Global VNET-peering stöds för närvarande inte. Detta inkluderar alla testprogram eller diagnostiska pingningsverktyg. Oavsett var klientprogrammet finns måste nätverkssäkerhetsgrupper konfigureras så att klientens nätverkstrafik tillåts nå Redis-instansen.
>
>

När portkraven har konfigurerats enligt beskrivningen i föregående avsnitt kan du kontrollera att cachen fungerar genom att utföra följande steg.

- [Starta om](cache-administration.md#reboot) alla cachenoder. Om alla nödvändiga cacheberoenden inte kan nås (som dokumenterats i [inkommande portkrav](cache-how-to-premium-vnet.md#inbound-port-requirements) och [krav på utgående port)](cache-how-to-premium-vnet.md#outbound-port-requirements)kan cachen inte startas om.
- När cachenoderna har startats om (vilket rapporterats av cachestatusen i Azure-portalen) kan du utföra följande tester:
  - cacheslutpunkten (med port 6380) från en maskin som finns inom samma virtuella nätverk som cachen, med hjälp av [tcping](https://www.elifulkerson.com/projects/tcping.php). Ett exempel:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Om `tcping` verktyget rapporterar att porten är öppen är cachen tillgänglig för anslutning från klienter i det virtuella nätverket.

  - Ett annat sätt att testa är att skapa en testcacheklient (som kan vara ett enkelt konsolprogram med StackExchange.Redis) som ansluter till cacheminnet och lägger till och hämtar vissa objekt från cacheminnet. Installera exempelklientprogrammet på en virtuell dator som finns i samma virtuella nätverk som cacheminnet och kör det för att verifiera anslutningen till cachen.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>När du försöker ansluta till min Azure-cache för Redis i ett VNET, varför får jag ett felmeddelande om att fjärrcertifikatet är ogiltigt?

När du försöker ansluta till en Azure-cache för Redis i ett VNET visas ett certifikatverifieringsfel som detta:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Orsaken kan vara att du ansluter till värden med IP-adressen. Vi rekommenderar att du använder värdnamnet. Med andra ord, använd följande:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Undvik att använda IP-adressen som liknar följande anslutningssträng:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Om du inte kan matcha DNS-namnet innehåller vissa klientbibliotek konfigurationsalternativ som `sslHost` tillhandahålls av StackExchange.Redis-klienten. På så sätt kan du åsidosätta det värdnamn som används för certifikatverifiering. Ett exempel:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan jag använda virtuella nätverk med en standard eller grundläggande cache?
Virtuella nätverk kan endast användas med premiumcachar.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Varför misslyckas det att skapa en Azure-cache för Redis i vissa undernät men inte i andra?
Om du distribuerar en Azure-cache för Redis till ett resurshanterarens virtuella nätverk måste cachen finnas i ett dedikerat undernät som inte innehåller någon annan resurstyp. Om ett försök görs att distribuera en Azure-cache för Redis till ett Resurshanterarens VNet-undernät som innehåller andra resurser, misslyckas distributionen. Du måste ta bort befintliga resurser i undernätet innan du kan skapa en ny Azure-cache för Redis.

Du kan distribuera flera typer av resurser till ett klassiskt virtuella nätverk så länge du har tillräckligt med IP-adresser tillgängliga.

### <a name="what-are-the-subnet-address-space-requirements"></a>Vilka är kraven för undernätsadressutrymme?
Azure reserverar vissa IP-adresser i varje undernät och dessa adresser kan inte användas. De första och sista IP-adresserna för undernäten är reserverade för protokollkonformance, tillsammans med ytterligare tre adresser som används för Azure-tjänster. Mer information finns i [Finns det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje Redis-instans i undernätet två IP-adresser per shard och ytterligare en IP-adress för belastningsutjämnaren. En cache som inte är grupperad anses ha en shard.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungerar alla cachefunktioner när du är värd för en cache i ett VNET?
När cacheminnet är en del av ett virtuella nätverk kan endast klienter i det virtuella nätverket komma åt cachen. Därför fungerar inte följande cachehanteringsfunktioner just nu.

* Redis Console - Eftersom Redis Console körs i din lokala webbläsare, som ligger utanför det virtuella nätverket, kan den inte ansluta till cacheminnet.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Använda ExpressRoute med Azure Cache för Redis

Kunder kan ansluta en [Azure ExpressRoute-krets](https://azure.microsoft.com/services/expressroute/) till sin virtuella nätverksinfrastruktur och på så sätt utöka sitt lokala nätverk till Azure. 

Som standard utför en nyligen skapad ExpressRoute-krets inte tvångstunneler (annons för en standardrutt, 0.0.0.0/0) på ett VNET. Därför tillåts utgående Internet-anslutning direkt från VNET och klientprogram kan ansluta till andra Azure-slutpunkter, inklusive Azure Cache för Redis.

En gemensam kundkonfiguration är dock att använda påtvingad tunnel (annonsera en standardväg) som tvingar utgående Internettrafik att i stället flöda lokalt. Det här trafikflödet bryter anslutningen med Azure Cache för Redis om den utgående trafiken sedan blockeras lokalt så att Azure Cache för Redis-instansen inte kan kommunicera med dess beroenden.

Lösningen är att definiera en (eller flera) användardefinierade vägar (UDRs) i undernätet som innehåller Azure Cache för Redis. En UDR definierar undernätsspecifika vägar som ska uppfyllas i stället för standardvägen.

Om möjligt rekommenderas följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och som standard krafttunnlar alla utgående trafik lokalt.
* DEN UDR som tillämpas på undernätet som innehåller Azure Cache för Redis definierar 0.0.0.0/0 med en fungerande väg för TCP/IP-trafik till det offentliga internet. till exempel genom att ställa in nästa hopptyp på "Internet".

Den kombinerade effekten av dessa steg är att UDR på undernätsnivå har företräde framför ExpressRoute-tvångstunneler, vilket säkerställer utgående Internet-åtkomst från Azure-cachen för Redis.

Att ansluta till en Azure-cache för Redis-instans från ett lokalt program med ExpressRoute är inte ett typiskt användningsscenario på grund av prestandaskäl (för bästa prestanda bör Azure Cache för Redis-klienter vara i samma region som Azure Cache för Redis).

>[!IMPORTANT] 
>De vägar som definieras i en UDR **måste** vara tillräckligt specifika för att ha företräde framför alla vägar som annonseras av ExpressRoute-konfigurationen. I följande exempel används det breda adressintervallet 0.0.0.0/0, och som sådan kan eventuellt åsidosättas av vägannonser med mer specifika adressintervall.

>[!WARNING]  
>Azure Cache för Redis stöds inte med ExpressRoute-konfigurationer som **felaktigt korsannonserar vägar från den offentliga peering-sökvägen till den privata peering-sökvägen**. ExpressRoute-konfigurationer som har konfigurerad offentlig peering tar emot vägannonser från Microsoft för en stor uppsättning Microsoft Azure IP-adressintervall. Om dessa adressintervall är felaktigt korsannonserade på den privata peering-sökvägen, blir resultatet att alla utgående nätverkspaket från Azure Cache for Redis-instansens undernät felaktigt tvångstunneleras till en kunds lokala nätverksinfrastruktur. Det här nätverksflödet bryter Azure Cache för Redis. Lösningen på detta problem är att stoppa korsannonseringsvägar från den offentliga peering-vägen till den privata peering-sökvägen.


Bakgrundsinformation om användardefinierade vägar finns i den här [översikten](../virtual-network/virtual-networks-udr-overview.md).

Mer information om ExpressRoute finns i [Den tekniska översikten för ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Nästa steg
Läs om hur du använder fler premiumcachefunktioner.

* [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
