---
title: Använda S2S VPN som backup för Azure ExpressRoute Private Peering | Microsoft-dokument
description: Den här sidan innehåller arkitekturrekommendationer för säkerhetskopiering av Azure ExpressRoute-privat peering med S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687836"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Använda S2S VPN som backup för ExpressRoute privat peering

I artikeln [designa för haveriberedskap med ExpressRoute privat peering][DR-PP]diskuterade vi behovet av säkerhetskopieringsanslutningslösning för en ExpressRoute-privat peering-anslutning och hur du använder geo-redundanta ExpressRoute-kretsar för ändamålet. I den här artikeln kan vi överväga hur du kan utnyttja och underhålla S2S VPN (Site-to-site) som en baksida för ExpressRoute privat peering. 

Till skillnad från geo-redundanta ExpressRoute-kretsar kan du bara använda ExpressRoute-VPN-kombination för haveriberedskap i aktivt passivt läge. En stor utmaning med att använda någon säkerhetskopiering nätverksanslutning i passivt läge är att den passiva anslutningen ofta skulle misslyckas vid sidan av den primära anslutningen. Den vanligaste orsaken till fel i den passiva anslutningen är brist på aktivt underhåll. Därför, i den här artikeln ska vi fokusera på hur man verifierar och aktivt underhåller S2S VPN-anslutning som säkerhetskopierar en ExpressRoute privat peering.

>[!NOTE] 
>När en viss väg annonseras via både ExpressRoute och VPN föredrar Azure routning via ExpressRoute.  
>

I den här artikeln ska vi se hur du verifierar anslutningen både från Azure-perspektivet och nätverkskantperspektivet på kundsidan. Möjligheten att validera från båda hållen hjälper oavsett om du hanterar nätverksenheter på kundsidan som peer med Microsoft-nätverksenheterna. 

## <a name="example-topology"></a>Exempel topologi

I vår konfiguration har vi ett lokalt nätverk anslutet till ett Azure hub VNet via både en ExpressRoute-krets och en S2S VPN-anslutning. Azure hub VNet är i sin tur peered till en eker VNet, som visas i diagrammet nedan:

![1][1]

I installationen avslutas ExpressRoute-kretsen på ett par CE-routrar (Customer Edge" (CE) lokalt. Det lokala nätverket är anslutet till CE-routrarna via ett par brandväggar som fungerar i leader-follower-läge. S2S VPN avslutas direkt på brandväggarna.

I följande tabell visas topologins viktigaste IP-prefix:

| **Entitet** | **Prefix** |
| --- | --- |
| Lokalt LAN | 10.1.11.0/25 |
| Virtuella nätverk för Azure Hub | 10.17.11.0/25 |
| Azure talade VNet | 10.17.11.128/26 |
| Lokal testserver | 10.1.11.10 |
| Eker-virtuell dator för VNet-test | 10.17.11.132 |
| ExpressRoute primär anslutning p2p undernät | 192.168.11.16/30 |
| ExpressRoute sekundär anslutning p2p undernät | 192.168.11.20/30 |
| VPN gateway primär BGP-peer-IP | 10.17.11.76 |
| Sekundär IP-för VPN-gateway-peer-IP | 10.17.11.77 |
| Lokal VPN-IP-grupp för brandvägg | 192.168.11.88 |
| Primär CE-router i/f mot brandväggs-IP | 192.168.11.0/31 |
| Brandvägg i/f mot primär CE-router IP | 192.168.11.1/31 |
| Sekundär CE-router i/f mot brandvägg IP | 192.168.11.2/31 |
| Brandvägg i/f mot sekundär CE-router IP | 192.168.11.3/31 |


I följande tabell visas topologins ASN:A.koder of the topology:

| **Autonomt system** | **Asn** |
| --- | --- |
| Lokal | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Virtuellt nätverk GW (ExR) | 65515 |
| Virtuellt nätverk GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Hög tillgänglighet utan asymmetricitet

### <a name="configuring-for-high-availability"></a>Konfigurera för hög tillgänglighet

[Konfigurera ExpressRoute- och Site-to-Site-samtidiga anslutningar][Conf-CoExist] diskuterar hur du konfigurerar den samtidiga ExpressRoute-kretsen och S2S VPN-anslutningar. Som vi diskuterade i [Designing för hög tillgänglighet med ExpressRoute][HA], för att förbättra ExpressRoute hög tillgänglighet vår setup upprätthåller nätverket redundans (undviker en punkt av fel) hela vägen upp till slutpunkterna. Även både de primära och sekundära anslutningarna för ExpressRoute-kretsarna är konfigurerade för att fungera i aktivt aktivt läge genom att annonsera de lokala prefixen på samma sätt genom båda anslutningarna. 

Den lokala ruttannonsen för den primära CE-routern genom den primära anslutningen av ExpressRoute-kretsen visas nedan (Junos-kommandon):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Den lokala ruttannonsen för den sekundära CE-routern genom den sekundära anslutningen av ExpressRoute-kretsen visas nedan (Junos-kommandon):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

För att förbättra den höga tillgängligheten för säkerhetskopieringsanslutningen konfigureras S2S VPN också i aktivt aktivt läge. Azure VPN-gatewaykonfigurationen visas nedan. Observera som en del av VPN-konfigurationen VPN bgp-peer-IP-adresserna för gatewayen --10.17.11.76 och 10.17.11.77--anges också.

![2][2]

Den lokala vägen annonseras av brandväggarna till de primära och sekundära BGP-peer-datorerna för VPN-gatewayen. Ruttannonserna visas nedan (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Konfigurera S2S VPN i aktivt aktivt läge ger inte bara hög tillgänglighet till din katastrofåterställning backup nätverksanslutning, men ger också högre dataflöde till backup-anslutning. Med andra ord rekommenderas konfigurera S2S VPN i aktivt aktivt läge eftersom det tvingar skapa flera underliggande tunnlar.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurera för symmetriskt trafikflöde

Vi noterade att när en viss lokal rutt annonseras via både ExpressRoute och S2S VPN, skulle Azure föredra ExpressRoute-sökvägen. För att tvinga Azure föredrar S2S VPN-sökväg framför den samexisterande ExpressRoute måste du annonsera mer specifika vägar (längre prefix med större nätmask) via VPN-anslutningen. Vårt mål här är att använda VPN-anslutningar som tillbaka bara. Så standardsökvägvalsbeteendet för Azure är i linje med vårt mål. 

Det är vårt ansvar att se till att trafiken som är avsedd för Azure från lokala också föredrar ExpressRoute-sökväg framför S2S VPN. Standardinställningarna för CE-routrar och brandväggar i vår lokala installation är 100. Så genom att konfigurera den lokala inställningen för de rutter som tas emot via ExpressRoute privata peerings större än 100 (säg 150), kan vi göra trafiken avsedd för Azure föredrar ExpressRoute-kretsen i stabilt tillstånd.

BGP-konfigurationen för den primära CE-routern som avslutar den primära anslutningen av ExpressRoute-kretsen visas nedan. Observera att värdet för den lokala inställningen för de vägar som annonseras via iBGP-sessionen är konfigurerat till 150. På samma sätt måste vi se till att den sekundära CE-routern som avslutar den sekundära anslutningen av ExpressRoute-kretsen också är konfigurerad till 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

Routningstabellen för de lokala brandväggarna bekräftar (visas nedan) att för den lokala trafik som är avsedd för Azure är den önskade sökvägen över ExpressRoute i stabilt tillstånd.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

I den ovannämnda rutttabellen för navet och eker VNet-rutterna --10.17.11.0/25 och 10.17.11.128/26 - vi ser ExpressRoute krets är att föredra framför VPN-anslutningar. 192.168.11.0 och 192.168.11.2 är IP-adresser på brandväggsgränssnitt mot CE-routrar.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validering av ruttväxling över S2S VPN

Tidigare i den här artikeln verifierade vi lokal ruttannons av brandväggarna till de primära och sekundära BGP-peer-datorerna i VPN-gatewayen. Dessutom ska vi bekräfta Azure-vägar som tas emot av brandväggar från de primära och sekundära BGP-peer-datorerna i VPN-gatewayen.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

På samma sätt ska vi verifiera för lokala nätverksvägprefix som tas emot av Azure VPN-gatewayen. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Som framgår ovan har VPN-gatewayen vägar som tas emot både av de primära och sekundära BGP-peer-datorerna i VPN-gatewayen. Den har också synlighet över de rutter som tas emot via primära och sekundära ExpressRoute-anslutningar (de med AS-path prepended med 12076). För att bekräfta de vägar som tas emot via VPN-anslutningar måste vi känna till den lokala BGP-peer-IP:en för anslutningarna. I vår setup under övervägande är det 192.168.11.88 och vi ser de rutter som mottagits från den.

Låt oss sedan verifiera de vägar som annonseras av Azure VPN-gatewayen till den lokala brandväggen BGP-peer (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


Det gick inte att se ruttutbyten indikerar anslutningsfel. Se [Felsökning: En VPN-anslutning från Azure-plats till plats kan inte ansluta och slutar arbeta][VPN Troubleshoot] för att få hjälp med felsökning av VPN-anslutningen.

## <a name="testing-failover"></a>Testa redundans

Nu när vi har bekräftat framgångsrika ruttutbyten över VPN-anslutningen (kontrollplan), är vi inställda på att växla trafik (dataplan) från ExpressRoute-anslutningen till VPN-anslutningen. 

>[!NOTE] 
>I produktionsmiljöer måste redundanstestning göras under schemalagda nätverksunderhållsarbetsfönster eftersom det kan vara avbrottsrörande.
>

Innan du gör trafikväxeln ska vi spåra vägen den aktuella sökvägen i vår inställning från den lokala testservern till testa den virtuella datorn i det ekerbaserade virtuella nätverket.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

De primära och sekundära ExpressRoute point-to-point-anslutningsundernäten i vår inställning är 192.168.11.16/30 och 192.168.11.20/30. I ovanstående spårväg, i steg 3 ser vi att vi slår 192.168.11.18, vilket är gränssnittet IP för den primära MSEE. Närvaro av MSEE-gränssnittet bekräftar att som förväntat vår nuvarande väg är över ExpressRoute.

Som rapporterats i [Reset ExpressRoute krets peerings][RST], låt oss använda följande powershell kommandon för att inaktivera både den primära och sekundära peering av ExpressRoute kretsen.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Växlingstiden beror på BGP-konvergenstiden. I vår setup tar redundansväxlingen några sekunder (mindre än 10). Efter växeln visar upprepningen av traceroute följande bana:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Traceroute-resultatet bekräftar att säkerhetskopieringsanslutningen via S2S VPN är aktiv och kan ge tjänsten kontinuitet om både de primära och sekundära ExpressRoute-anslutningarna misslyckas. För att slutföra redundanstestningen, låt oss aktivera ExpressRoute-anslutningar tillbaka och normalisera trafikflödet med hjälp av följande uppsättning kommandon.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

För att bekräfta att trafiken växlas tillbaka till ExpressRoute upprepar du traceroute och ser till att den går igenom ExpressRoute-privata peering.

## <a name="next-steps"></a>Nästa steg

ExpressRoute är utformat för hög tillgänglighet utan en enda felpunkt i Microsoft-nätverket. Fortfarande en ExpressRoute krets är begränsad till en enda geografisk region och till en tjänsteleverantör. S2S VPN kan vara en bra katastrofåterställning passiv säkerhetskopiering lösning till en ExpressRoute krets. För en pålitlig passiv anslutningslösning för säkerhetskopiering, regelbundet underhåll av den passiva konfigurationen och den periodiska valideringen är anslutningen viktig. Det är viktigt att inte låta VPN-konfigurationen bli inaktuell och att regelbundet (säg varje kvartal) upprepa de validerings- och redundansteststeg som beskrivs i den här artikeln under underhållsfönstret.

Om du vill aktivera övervakning och aviseringar baserat på VPN-gatewaymått läser [du Konfigurera aviseringar om VPN Gateway-mått][VPN-alerts].

[Konfigurera BFD via ExpressRoute][BFD]för att påskynda BGP-konvergens efter ett ExpressRoute-fel.

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologi under övervägande"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW-konfiguration"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



