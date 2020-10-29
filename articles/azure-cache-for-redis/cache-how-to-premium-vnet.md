---
title: Konfigurera en Virtual Network-Premium Azure-cache för Redis
description: Lär dig hur du skapar och hanterar Virtual Network support för din Premium-nivå i Azure-cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: a55db6a9db8cc53da15ba6e818db7b78b72cefc9
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927744"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis
Azure cache för Redis har olika cache-erbjudanden, vilket ger flexibilitet i valet av cache-storlek och-funktioner, inklusive funktioner för Premium-nivå, till exempel klustring, beständighet och stöd för virtuella nätverk. Ett VNet är ett privat nätverk i molnet. När en Azure-cache för Redis-instans har kon figurer ATS med ett VNet, är den inte offentligt adresserad och kan endast nås från virtuella datorer och program i VNet. Den här artikeln beskriver hur du konfigurerar stöd för virtuella nätverk för en Premium Azure-cache för Redis-instansen.

> [!NOTE]
> Azure cache för Redis stöder både klassisk och Resource Manager-virtuella nätverk.
> 
> 

## <a name="why-vnet"></a>Varför VNet?
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) -distribution ger förbättrad säkerhet och isolering för Azure-cache för Redis, samt undernät, åtkomst kontroll principer och andra funktioner för att ytterligare begränsa åtkomst.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk
Stöd för Virtual Network (VNet) konfigureras på bladet **ny Azure-cache för Redis** under skapandet av cache. 

1. Om du vill skapa en Premium-cache loggar du in på [Azure Portal](https://portal.azure.com) och väljer **skapa en resurs** . Observera, förutom att skapa cacheminnen i Azure Portal, kan du också skapa dem med hjälp av Resource Manager-mallar, PowerShell eller Azure CLI. Mer information om hur du skapar en Azure-cache för Redis finns i [skapa en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Skapa resurs.":::
   
2. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis** .

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Skapa resurs.":::

3. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya Premium-cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . Redis.cache.Windows.net* . | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Cachestorlek** | Klicka på list rutan och välj en Premium-cache för att konfigurera Premium-funktioner. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |

4. Välj fliken **nätverk** eller klicka på knappen **nätverk** längst ned på sidan.

5. På fliken **nätverk** väljer du **virtuella nätverk** som anslutnings metod. Om du vill använda ett nytt virtuellt nätverk skapar du det först genom att följa stegen i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) eller [skapa ett virtuellt nätverk (klassisk) med hjälp av Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) och återgå sedan till bladet **ny Azure-cache för Redis** för att skapa och konfigurera din Premium-cache.

   > [!IMPORTANT]
   > När du distribuerar en Azure-cache för Redis till ett virtuellt resurs hanterings nätverk måste cachen finnas i ett dedikerat undernät som inte innehåller några andra resurser förutom Azure cache för Redis-instanser. Om ett försök görs att distribuera en Azure-cache för Redis till ett virtuellt nätverk till ett undernät som innehåller andra resurser, Miss lyckas distributionen.
   > 
   > 

   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtual Network** | List rutan och välj ditt virtuella nätverk. | Välj ett virtuellt nätverk som finns i samma prenumeration och plats som din cache. | 
   | **Undernät** | List rutan och välj ditt undernät. | Under nätets adress intervall ska vara i CIDR-notation (t. ex. 192.168.1.0/24). Det måste finnas i det virtuella nätverkets adress utrymme. | 
   | **Statisk IP-adress** | Valfritt Ange en statisk IP-adress. | Om du inte anger en statisk IP-adress väljs en IP-adress automatiskt. | 

   > [!IMPORTANT]
   > Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje Redis-instans i under nätet två IP-adresser per Shard och ytterligare en IP-adress för belastningsutjämnaren. En icke-klustrad cache anses ha en Shard.
   > 
   > 

6. Välj **Nästa: fliken Avancerat** eller klicka på **Nästa: Avancerat** längst ned på sidan.

7. På fliken **Avancerat** för en Premium-cache-instans konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet. 

8. Välj fliken **Nästa: Taggar** eller klicka på knappen **Nästa: Taggar** längst ned på sidan.

9. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen. 

10. Välj **Granska + skapa** . Du kommer till fliken Granska + skapa där Azure verifierar konfigurationen.

11. När meddelandet grön verifiering har skickats visas väljer du **skapa** .

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas. När cachen har skapats kan du Visa konfigurationen för VNet genom att klicka på **Virtual Network** på **resurs-menyn** .

![Virtuellt nätverk][redis-cache-vnet-info]

Om du vill ansluta till Azure-cachen för Redis-instansen när du använder ett VNet, anger du värd namnet för din cache i anslutnings strängen som visas i följande exempel:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Vanliga frågor och svar om Azure cache för Redis VNet
Följande lista innehåller svar på vanliga frågor om Azure cache för Redis-skalning.

* Vilka är några vanliga problem med att konfigurera Azure cache för Redis och virtuella nätverk?
* [Hur kan jag kontrol lera att mitt cacheminne fungerar i ett VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Varför visas ett fel meddelande om att Fjärrcertifikatet är ogiltigt vid försök att ansluta till Azure-cachen för Redis i ett VNET.
* [Kan jag använda virtuella nätverk med en standard-eller Basic-cache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Varför fungerar det inte att skapa en Azure-cache för Redis i vissa undernät, men inte till andra?
* [Vilka är kraven på under näts adress utrymme?](#what-are-the-subnet-address-space-requirements)
* [Fungerar alla cache-funktioner när du är värd för en cache i ett VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Vilka är några vanliga problem med att konfigurera Azure cache för Redis och virtuella nätverk?
När Azure cache för Redis finns i ett VNet används portarna i följande tabeller. 

>[!IMPORTANT]
>Om portarna i följande tabeller är blockerade fungerar inte cachen som den ska. Att ha en eller flera av dessa portar blockerade är det vanligaste problemet vid fel konfiguration när du använder Azure cache för Redis i ett VNet.
> 
> 

- [Krav för utgående portar](#outbound-port-requirements)
- [Krav för inkommande portar](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Krav för utgående portar

Det finns nio krav för utgående port. Utgående begär anden i dessa intervall är antingen utgående till andra tjänster som krävs för att cachen ska fungera eller internt till Redis-undernätet för kommunikation mellan noder. För geo-replikering finns ytterligare utgående krav för kommunikation mellan undernät i den primära cachen och repliken.

| Portar | Riktning | Transport protokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Utgående |TCP |Redis-beroenden för Azure Storage/PKI (Internet) | (Redis-undernät) |* |
| 443 | Utgående | TCP | Redis beroende av Azure Key Vault och Azure Monitor | (Redis-undernät) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Utgående |TCP/UDP |Redis-beroenden för DNS (Internet/VNet) | (Redis-undernät) | 168.63.129.16 och 169.254.169.254 <sup>2</sup> och valfri anpassad DNS-server för under nätet <sup>3</sup> |
| 8443 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) | (Redis-undernät) |
| 10221-10231 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) | (Redis-undernät) |
| 20226 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |
| 13000-13999 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |
| 15000-15999 |Utgående |TCP |Intern kommunikation för Redis och Geo-Replication | (Redis-undernät) |(Redis-undernät) (Geo-Replica-peer-undernät) |
| 6379-6380 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |

<sup>1</sup> du kan använda tjänst taggarna "AzureKeyVault" och "AzureMonitor" med nätverks säkerhets grupper i Resource Manager.

<sup>2</sup> de här IP-adresserna som ägs av Microsoft används för att ADRESSERA den virtuella värddatorn som hanterar Azure DNS.

<sup>3</sup> behövs inte för undernät utan en anpassad DNS-server, eller nyare Redis-cache som ignorerar anpassad DNS.

#### <a name="geo-replication-peer-port-requirements"></a>Krav på peerportar vid geo-replikering

Om du använder en geografisk replikering mellan cacheminnen i virtuella Azure-nätverk bör du notera att den rekommenderade konfigurationen är att avblockera portarna 15000-15999 för hela under nätet i både inkommande och utgående riktning till båda cacheminnena, så att alla replik komponenter i under nätet kan kommunicera direkt med varandra även i händelse av en framtida geo-redundansväxling.

#### <a name="inbound-port-requirements"></a>Krav för inkommande portar

Det finns åtta krav för ingående port intervall. Inkommande begär anden i dessa intervall är antingen inkommande från andra tjänster som finns i samma VNET eller internt i Redis under nät kommunikation.

| Portar | Riktning | Transport protokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkommande |TCP |Klient kommunikation till Redis, Azure Load Balancing | (Redis-undernät) | (Redis-undernät), Virtual Network, Azure Load Balancer <sup>1</sup> |
| 8443 |Inkommande |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |
| 8500 |Inkommande |TCP/UDP |Belastningsutjämning i Azure | (Redis-undernät) |Azure Load Balancer |
| 10221-10231 |Inkommande |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät), Azure Load Balancer |
| 13000-13999 |Inkommande |TCP |Klient kommunikation till Redis-kluster, Azure Load Balancing | (Redis-undernät) |Virtual Network Azure Load Balancer |
| 15000-15999 |Inkommande |TCP |Klient kommunikation till Redis-kluster, Azure Load Balancing och Geo-Replication | (Redis-undernät) |Virtual Network Azure Load Balancer, (geo-Replica-peer-undernät) |
| 16001 |Inkommande |TCP/UDP |Belastningsutjämning i Azure | (Redis-undernät) |Azure Load Balancer |
| 20226 |Inkommande |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |

<sup>1</sup> du kan använda tjänst tag gen "AzureLoadBalancer" (Resource Manager) (eller "AZURE_LOADBALANCER" för klassisk) för att redigera NSG-reglerna.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Ytterligare krav för VNET-nätverks anslutning

Det finns krav på nätverks anslutning för Azure cache för Redis som inte inlednings vis uppfylls i ett virtuellt nätverk. Azure cache för Redis kräver att alla följande objekt fungerar korrekt när de används i ett virtuellt nätverk.

* Utgående nätverks anslutning till Azure Storage slut punkter över hela världen. Detta inkluderar slut punkter som finns i samma region som Azure-cachen för Redis-instansen, samt lagrings slut punkter som finns i **andra** Azure-regioner. Azure Storage slut punkter matchas enligt följande DNS-domäner: *Table.Core.Windows.net* , *blob.Core.Windows.net* , *Queue.Core.Windows.net* och *File.Core.Windows.net* . 
* Utgående nätverks anslutning till *OCSP.DigiCert.com* , *crl4.DigiCert.com* , *OCSP.msocsp.com* , *mscrl.Microsoft.com* , *crl3.DigiCert.com* , *cacerts.DigiCert.com* , *oneocsp.Microsoft.com* och *CRL.Microsoft.com* . Den här anslutningen krävs för att stödja TLS/SSL-funktioner.
* DNS-konfigurationen för det virtuella nätverket måste kunna matcha alla slut punkter och domäner som nämns i de tidigare punkterna. Dessa DNS-krav kan uppfyllas genom att en giltig DNS-infrastruktur konfigureras och underhålls för det virtuella nätverket.
* Utgående nätverks anslutning till följande Azure Monitor slut punkter, som löses under följande DNS-domäner: *shoebox2-Black.shoebox2.Metrics.nsatc.net* , *North-prod2.prod2.Metrics.nsatc.net* , *azglobal-Black.azglobal.Metrics.nsatc.net* , *shoebox2-Red.shoebox2.Metrics.nsatc.net* , *East-prod2.prod2.Metrics.nsatc.net* , *azglobal-Red.azglobal.Metrics.nsatc.net* .

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hur kan jag kontrol lera att mitt cacheminne fungerar i ett VNET?

>[!IMPORTANT]
>När du ansluter till en Azure-cache för Redis-instans som finns i ett VNET måste dina cache-klienter finnas i samma VNET eller i ett VNET med VNET-peering aktiverat inom samma Azure-region. Global VNET-peering stöds inte för närvarande. Detta omfattar alla test program eller verktyg för diagnostik-Pinging. Oavsett var klient programmet finns måste nätverks säkerhets grupper konfigureras så att klientens nätverks trafik kan komma åt Redis-instansen.
>
>

När port kraven har kon figurer ATS enligt beskrivningen i föregående avsnitt kan du kontrol lera att din cache fungerar genom att utföra följande steg.

- [Starta om](cache-administration.md#reboot) alla cache-noder. Om alla nödvändiga cache-beroenden inte kan nås (enligt beskrivningen i krav för [inkommande port](cache-how-to-premium-vnet.md#inbound-port-requirements) och [krav för utgående port](cache-how-to-premium-vnet.md#outbound-port-requirements)), kommer cacheminnet inte att kunna starta om.
- När cache-noderna har startats om (som rapporter ATS av cache-statusen i Azure Portal) kan du utföra följande tester:
  - pinga cache-slutpunkten (med port 6380) från en dator som är inom samma VNET som cachen med hjälp av [TCPing](https://www.elifulkerson.com/projects/tcping.php). Exempel:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Om `tcping` verktyget rapporterar att porten är öppen är cachen tillgänglig för anslutning från klienter i VNet.

  - Ett annat sätt att testa är att skapa en app cache-klient (som kan vara ett enkelt konsol program med hjälp av StackExchange. Redis) som ansluter till cachen och lägger till och hämtar objekt från cachen. Installera exempel klient programmet på en virtuell dator som finns i samma VNET som cachen och kör den för att verifiera anslutningen till cacheminnet.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Varför visas ett fel meddelande om att Fjärrcertifikatet är ogiltigt vid försök att ansluta till Azure-cachen för Redis i ett VNET.

När du försöker ansluta till en Azure-cache för Redis i ett VNET visas ett certifikat verifierings fel som detta:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Orsaken kan vara att du ansluter till värden via IP-adressen. Vi rekommenderar att du använder värd namnet. Använd med andra ord följande:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Undvik att använda IP-adressen som liknar följande anslutnings sträng:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Om du inte kan matcha DNS-namnet innehåller vissa klient bibliotek konfigurations alternativ som `sslHost` tillhandahålls av stackexchange. Redis-klienten. På så sätt kan du åsidosätta det värdnamn som används för certifikat verifiering. Exempel:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan jag använda virtuella nätverk med en standard-eller Basic-cache?
Virtuella nätverk kan bara användas med Premium-cacheminnen.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Varför fungerar det inte att skapa en Azure-cache för Redis i vissa undernät, men inte till andra?
Om du distribuerar en Azure-cache för Redis till ett Resource Manager VNet måste cacheminnet finnas i ett dedikerat undernät som inte innehåller någon annan resurs typ. Om ett försök görs att distribuera en Azure-cache för Redis till ett VNet-undernät i Resource Manager som innehåller andra resurser, Miss lyckas distributionen. Du måste ta bort de befintliga resurserna i under nätet innan du kan skapa en ny Azure-cache för Redis.

Du kan distribuera flera typer av resurser till ett klassiskt VNet så länge du har tillräckligt med tillgängliga IP-adresser.

### <a name="what-are-the-subnet-address-space-requirements"></a>Vilka är kraven på under näts adress utrymme?
Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje Redis-instans i under nätet två IP-adresser per Shard och ytterligare en IP-adress för belastningsutjämnaren. En icke-klustrad cache anses ha en Shard.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungerar alla cache-funktioner när du är värd för en cache i ett VNET?
När din cache ingår i ett VNET kan endast klienter i VNET komma åt cacheminnet. Därför fungerar inte följande hanterings funktioner för cachelagring just nu.

* Redis-konsolen – eftersom Redis-konsolen körs i din lokala webbläsare, som är utanför det virtuella nätverket, kan den inte ansluta till din cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Använda ExpressRoute med Azure cache för Redis

Kunder kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -krets till sin virtuella nätverks infrastruktur och därmed utöka sitt lokala nätverk till Azure. 

Som standard utför en nyligen skapad ExpressRoute-krets ingen Tvingad tunnel trafik (annonsering av en standard väg, 0.0.0.0/0) i ett virtuellt nätverk. Det innebär att utgående Internet anslutning tillåts direkt från VNET-och klient program kan ansluta till andra Azure-slutpunkter, inklusive Azure cache för Redis.

En vanlig kund konfiguration är dock att använda Tvingad tunnel trafik (annonsera en standard väg) som framtvingar utgående Internet trafik till i stället för lokalt flöde. Detta trafikflöde bryter anslutningen till Azure cache för Redis om den utgående trafiken sedan blockeras lokalt så att Azure-cachen för Redis-instansen inte kan kommunicera med dess beroenden.

Lösningen är att definiera en (eller flera) användardefinierade vägar (UDR) på det undernät som innehåller Azure-cachen för Redis. En UDR definierar de undernät-/regionsspecifika vägar som ska användas i stället för standard vägen.

Om möjligt rekommenderar vi att du använder följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och använder som standard tunnel all utgående trafik lokalt.
* UDR som tillämpas på det undernät som innehåller Azure-cachen för Redis definierar 0.0.0.0/0 med en fungerande väg för TCP/IP-trafik till det offentliga Internet. till exempel genom att ange nästa hopp typ till "Internet".

Den kombinerade påverkan av dessa steg är att UDR får företräde framför ExpressRoute-Tvingad tunnel trafik, vilket garanterar utgående Internet åtkomst från Azure-cachen för Redis.

Att ansluta till en Azure-cache för Redis-instansen från ett lokalt program som använder ExpressRoute är inte ett typiskt användnings scenario på grund av prestanda orsaker (för bästa prestanda är Azure cache för Redis-klienter i samma region som Azure-cachen för Redis).

>[!IMPORTANT] 
>Vägarna som definieras i en UDR **måste** vara tillräckligt bestämda för att prioriteras över alla vägar som annonseras av ExpressRoute-konfigurationen. I följande exempel används det breda adress intervallet 0.0.0.0/0, och detta kan eventuellt åsidosättas av en av väg annonseringarna med hjälp av mer exakta adress intervall.

>[!WARNING]  
>Azure cache för Redis stöds inte med ExpressRoute-konfigurationer som **felaktigt korsar vägar från den offentliga peering-vägen till den privata peering-sökvägen** . ExpressRoute-konfigurationer med offentlig peering konfigurerad, tar emot väg meddelanden från Microsoft för en stor uppsättning Microsoft Azure IP-adressintervall. Om dessa adress intervall felaktigt annonseras i den privata peering-sökvägen är resultatet att alla utgående nätverks paket från Azure-cachen för Redis-instansens undernät felaktigt tvingas-tunnlad till en kunds lokala nätverks infrastruktur. Det här nätverks flödet delar upp Azure cache för Redis. Lösningen på det här problemet är att stoppa kors annonserings vägar från den offentliga peering-vägen till den privata peering-sökvägen.


Bakgrunds information om användardefinierade vägar är tillgänglig i den här [översikten](../virtual-network/virtual-networks-udr-overview.md).

Mer information om ExpressRoute finns i [teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
