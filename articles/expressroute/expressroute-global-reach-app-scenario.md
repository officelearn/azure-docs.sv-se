---
title: Azure ExpressRoute globala nå Programscenariot | Microsoft Docs
description: Den här sidan innehåller en programscenariot för Global räckvidd.
documentationcenter: na
services: networking
author: cherylmc
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: cherylmc
ms.openlocfilehash: 2adb8debf641a9b3875c5c386df7a35273f2a258
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428246"
---
# <a name="expressroute-global-reach-application-scenario"></a>ExpressRoute Global räckvidd programscenariot

Läs mer om ExpressRoute Global räckvidd i [ExpressRoute Global räckvidd][Global Reach]. I den här artikeln ska vi gå igenom ett scenario för programmet, jämför ExpressRoute Global räckvidd lösningen till några andra lösningar, konfigurera Global räckvidd för exempelscenariot och Kontrollera anslutningarna. 

##<a name="application-scenario"></a>Programscenariot

Fabrikam, Inc. har ett stort fysisk närvaro och Azure-distribution i USA, östra. Fabrikam har en backend-anslutning mellan lokala och Azure-distributioner via ExpressRoute. Contoso Ltd. har en närvaro och Azure-distribution i västra USA. Contoso har backend-anslutning mellan sina lokala och Azure-distributioner via ExpressRoute.  

Fabrikam Inc. acquires Contoso Ltd. Följande fusion vill Fabrikam sammankoppling nätverken. Följande bild visar scenario:

 [![1]][1]

Streckad pilar mitt i bilden ovan visar anslutningarna för nätverket. I följande tabell visas i routningstabellen för primär privat peering av ExpressRoute för Contoso Ltd. före sammanslagningen.

[![2]][2]

I följande tabell visas i routningstabellen för primär privat peering i ExpressRoute för Fabrikam Inc. före sammanslagningen.

[![3]][3]

## <a name="traditional-solutions"></a>Traditionella lösningar

### <a name="option-1-interconnect-on-premises-networks"></a>Alternativ 1: Sammankoppling lokala nätverk

Följande bild visar det här alternativet. Som du ser kan du hantera alla routning mellan två entiteter sammankoppling de två lokala nätverken med plats-till-plats-VPN eller andra bredbandsanslutning alternativ. 

[![4]][4]

Det här alternativet har följande nackdelar:

- Du tvingar kommunikation mellan regionala Azure kommunikation för distributionen över en icke-optimal väg.
- Du nekat fördelen med hög tillgänglighet för Microsoft-stamnätverk för flera regioner kommunikation.
- Du ska ta med fullständig routning ansvar för flera regioner kommunikation.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Alternativ 2: ExpressRoute mellan anslutning och sammankoppling lokala nätverk

Följande bild visar det här alternativet.

[![5]][5]

Som du ser i bilden ovan kan upprätta du dessutom anslutningen mellan ExpressRoute-kretsar till flera regionala virtuella nätverk. Flera regionala anslutningen mellan virtuella nätverk till ExpressRoute-kretsar kan aktivera följande kommunikation:

- Västra USA / östra USA virtuella nätverk kan kommunicera respektive med Fabrikam/Contoso lokala nätverk.
- USA, Väst virtuellt nätverk kan kommunicera med det östra USA virtuella nätverket.

Gränssnittet mellan de två lokala nätverken krävs fortfarande för de lokala nätverken kan kommunicera med varandra.

Det här alternativet kan flera regioner Azure kommunikation för privat distribution till trampa över inom Microsofts stamnätverk och kommunikationen mellan det lokala nätverket ska överföras det externa nätverket. Den huvudsakliga Nackdelen med lösningen är dock att du måste upprätta flera mellan regionala anslutningar, vilket kan göra det svårare för underhåll och felsökning. Dessutom kan inte alternativet du dra nytta av hög tillgänglighet inom Microsofts globala stamnätverk för kommunikation mellan de två lokala nätverken.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Alternativ 3: VNet-peering och sammankoppling lokala nätverk

Följande bild visar det här alternativet. Alternativet använder VNet-peering för kommunikation mellan regional VNet-kommunikation. Alternativet, är som på bilden, ofullständig eftersom den inte upp tvärregional VNet till en lokal kommunikation (identifieras via de två prickade pil raderna i mitten). Använd en lokal plats till den lokala anslutningen (som alternativ 1) eller ExpressRoute över anslutning (som i alternativ 2) för tvärregional till en lokal kommunikation.

[![6]][6]

Det här alternativet ger optimal routning för kommunikation mellan regional VNet-kommunikation. Den hamnar kort om Fabrikam eller Contoso har ett mer komplexa Azure-distribution, till exempel en modell för virtuellt nätverk av typen hub-spoke. Dessutom som föregående två alternativ kan det här alternativet du inte dra nytta av hög tillgänglighet inom Microsofts globala stamnätverk för kommunikation mellan de två lokala nätverken.

## <a name="global-reach"></a>Global räckvidd

ExpressRoute Global räckvidd länkar privat peering i ExpressRoute-kretsar, såsom illustreras på följande bild:

[![7]][7]

Konfigurera Global räckvidd mellan de två ExpressRoute-kretsarna kan privat kommunikation mellan de två lokala nätverken och Azure-distribution i två regioner. Därför uppfyller Global räckvidd alla önskade kommunikation (identifieras via streckad linje i första bilden i den här artikeln) över Microsoft-stamnätverket.

### <a name="configure-global-reach"></a>Konfigurera Global räckvidd

Läs hur du konfigurerar ExpressRoute Global räckvidd i [konfigurera Global räckvidd][Configure Global Reach]. 

Eftersom Fabrikam, Inc. och Contoso Ltd. publicerat Azure som separata företag, ExpressRoute-kretsar för två företag finns i två olika Azure-prenumerationer. För att skapa en Global räckvidd över prenumerationer, måste du skapa ett tillstånd i ExpressRoute-krets som hör till Contoso Ltd. och skickar den till Fabrikam Inc. ExpressRoute-krets.


Skapa en auktorisering för Contosos ExpressRoute-krets, första inloggningen i Contosos Azure-konto och väljer du lämplig prenumeration (om det finns flera prenumerationer). PowerShell-kommandon för de här stegen är:

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
Stegen för att skapa en ExpressRoute-krets auktorisering visas nedan:

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

”Set-AzureRmExpressRouteCircuit' utdata visar en lista över ExpressRouteCircuit. Observera den privata peering-ID och den auktoriseringsnyckeln som anges mot slutet av listan. Se ett exempel PowerShell utdata kodavsnittet nedan:

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

Du kan skapa globala nå under den Fabrikam ExpressRoute-krets med peering-ID: T och auktoriseringsnyckeln för. Logga in på den Fabrikam Azure-konto. Om det är mer än en prenumeration väljer du lämplig prenumeration.

Global räckvidd skapar en redundant uppsättning anslutningar mellan punkter med över två msee: N-par. För två point-to-point-anslutningar måste du ange ett/29 adressprefix (exempelvis som körs nu ska vi använda 192.168.11.64/29). Använd följande kommandon för att skapa anslutningen Global räckvidd:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När ovanstående kommandon utförs, tar det några minuter att skapa de redundanta anslutningarna för Global räckvidd.

### <a name="verify-global-reach"></a>Verify Global Reach

I följande tabell visas i routningstabellen för primär privat peering i ExpressRoute Contoso Ltd. när du har konfigurerat Global räckvidd.

[![8]][8]

I följande tabell visas i routningstabellen för primär privat peering i ExpressRoute Fabrikam Inc. när du har konfigurerat Global räckvidd.

[![9]][9]

I tabellerna ovan ser vi alla de förväntade ”nätverk” målprefix och i rätt ”nästa HOPP”.

Senare kan du se bladet Get route-tabell som är tillgängliga i Azure-portalen under ”privat peering” för en ExpressRoute-krets. Du kan också ange en ExpressRoute-routningstabell med hjälp av följande PowerShell-kommando:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Om du vill se i routningstabellen för den sekundära msee: N, ersätter du ”primära” med nyckelordet ”sekundär” i kommandot ovan.

Vi ska också kontrollera dataanslutning för plan genom att pinga olika mål från olika nätverk. Följande tre pingar verifiera data plan anslutning till Contoso lokala nätverk, Contoso Azure VNet och Fabrikam virtuella Azure-nätverket från Fabrikam lokalt nätverk.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


Följande två pingar bekräfta data plan anslutningen till Azure virtuellt nätverk för Contoso och Fabrikam virtuella Azure-nätverket från Contoso lokala nätverk.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

Följande ping verifierar plan dataanslutning till Contoso Azure virtuellt nätverk från Fabrikam Azure VNet.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Optimering för svarstid känsliga trafik

Global räckvidd dirigerar trafik via Microsoft Edge-enheter. Du kan uppnå mer optimal routning mellan två virtuella nätverk genom att aktivera VNet-peering mellan dem för det specifika scenariot anses vara i den här artikeln. På samma sätt kan du uppnå mer optimal routning mellan de två lokala nätverken via en tjänstleverantör som kan ge en mer direkt WAN-anslutning mellan platserna. I sådana fall kan använda du Global räckvidd routning som en misslyckas tillbaka alternativ för anslutningarna. 

## <a name="next-steps"></a>Nästa steg

Global räckvidd distribueras på basis av per land. Om du vill se om Global räckvidd är tillgängligt i länder som du vill se [ExpressRoute Global räckvidd][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "programmet scenario"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "Contoso ExpressRoute routningstabellen innan sammanslagning"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "Fabrikam ExpressRoute routningstabellen innan sammanslagning"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "interconnecting lokala nätverk"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "ExpressRoute mellan ansluta"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet-peering"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Contoso ExpressRoute routningstabellen med Global räckvidd"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Fabrikam ExpressRoute routningstabellen med Global räckvidd"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





