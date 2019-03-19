---
title: Felsök Azure Application Gateway felaktig Gateway (502) | Microsoft Docs
description: Lär dig att felsöka Application Gateway 502
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 26144b7eb53f5c0d4ebecbc9e6eece741f466719
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997804"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Felsök Felaktig gateway-fel i Application Gateway

Lär dig mer om att Felsök Felaktig gateway (502) tas emot när med application gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du har konfigurerat en application gateway är en av de fel som användare kan råka ut ”Serverfel: 502 - Webbservern tog emot ett ogiltigt svar när den fungerade som gateway eller proxyserver. Det här felet kan inträffa på grund av följande huvudsakliga skäl:

* NSG, UDR eller anpassad DNS blockerar åtkomsten till medlemmar i serverdelspoolen.
* Backend-virtuella datorer eller instanser av virtual machine scale Sets svarar inte på standard-hälsoavsökning.
* Ogiltig eller felaktig konfiguration av anpassade hälsoavsökningar.
* Azure Application Gateway [backend-poolen har inte konfigurerats eller tom](#empty-backendaddresspool).
* Ingen av de virtuella datorer och instanser i [virtual machine scale Sets är felfria](#unhealthy-instances-in-backendaddresspool).
* [Problem med timeout eller anslutning för begäran](#request-time-out) med användarbegäranden.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Nätverkssäkerhetsgrupp, användardefinierad väg eller anpassad DNS-problem

### <a name="cause"></a>Orsak

Om åtkomst till serverdelen har blockerats på grund av förekomsten av NSG, UDR eller anpassad DNS, kommer inte att kunna nå serverdelspoolen Application Gateway-instanser och skulle leda till avsökning fel som orsakar 502 fel. Observera att NSG/UDR kan finnas i Application Gateway-undernät eller undernätet där de virtuella Programdatorerna har distribuerats. Förekomst av anpassad DNS i det virtuella nätverket kan på liknande sätt också orsaka problem om FQDN som används för medlemmar i serverdelspool och har inte matchats korrekt Användarkonfigurerad DNS-servern för det virtuella nätverket.

### <a name="solution"></a>Lösning

Validera NSG, UDR och DNS-konfigurationen genom att gå igenom följande steg:
* Kontrollera NSG: er som är associerade med Application Gateway-undernät. Se till att kommunikation till serverdelen inte är blockerad.
* Kontrollera UDR som är associerade med Application Gateway-undernät. Se till att UDR inte dirigerar trafik från backend-undernät – till exempel söka efter routning för att virtuella installationer eller standardvägar som annonseras för Application Gateway-undernät via ExpressRoute/VPN.

```powershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Kontrollera effektiva NSG och väg med den virtuella datorn på serversidan

```powershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Kontrollera förekomst av anpassad DNS i det virtuella nätverket. DNS kan kontrolleras genom att titta på information om VNet-egenskaperna i utdata.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Kontrollera att DNS-servern kan matcha FQDN för backend-poolmedlem korrekt om den finns.

## <a name="problems-with-default-health-probe"></a>Problem med standard-hälsoavsökning

### <a name="cause"></a>Orsak

502 fel kan också vara vanliga indikatorer hälsoavsökningen standard inte är kan nå backend-virtuella datorer. När en Application Gateway-instans etableras, konfigurerar den automatiskt en hälsoavsökning som standard ska varje BackendAddressPool med hjälp av egenskaperna för BackendHttpSetting. Inga indata från användaren krävs för att ställa in den här avsökningen. Mer specifikt när en belastningsutjämningsregel konfigureras, görs en association mellan en BackendHttpSetting och BackendAddressPool. En standard-avsökning har konfigurerats för var och en av dessa associationer och Application Gateway startar en regelbundna Kontrollera anslutning till varje instans i BackendAddressPool på porten som anges i BackendHttpSetting-elementet. Följande tabell visas de värden som är associerade med standard-hälsoavsökning.

| Avsökningen egenskapen | Värde | Beskrivning |
| --- | --- | --- |
| Avsökningswebbadress |`http://127.0.0.1/` |URL-sökväg |
| Intervall |30 |Avsökningsintervall i sekunder |
| Time-out |30 |Avsökningen tidsgräns i sekunder |
| Tröskelvärde för Ej felfri |3 |Avsökning för antal nya försök. Backend-server markeras när antalet upprepade fel når tröskelvärde för ej felfri. |

### <a name="solution"></a>Lösning

* Se till att en standardwebbplats har konfigurerats och lyssnar på 127.0.0.1.
* Om BackendHttpSetting anger en annan port än 80, ska standardwebbplatsen konfigureras för att lyssna på porten.
* Anropet till `http://127.0.0.1:port` ska returnera ett HTTP-Resultatkod 200. Detta ska returneras inom tidsgränsen som 30 sekunder.
* Se till att konfigurerade porten är öppen och att det finns inga brandväggsregler eller Azure Nätverkssäkerhetsgrupperna, som blockerar inkommande eller utgående trafik på porten som konfigurerats.
* Om du använder Azure klassiska virtuella datorer eller en molntjänst med FQDN eller offentlig IP-adress, se till att motsvarande [endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) öppnas.
* Om den virtuella datorn är konfigurerad via Azure Resource Manager och ligger utanför det virtuella nätverket där Application Gateway distribueras [Nätverkssäkerhetsgrupp](../virtual-network/security-overview.md) måste konfigureras för att tillåta åtkomst på önskad port.

## <a name="problems-with-custom-health-probe"></a>Problem med anpassade hälsoavsökning

### <a name="cause"></a>Orsak

Anpassade hälsoavsökningar ger ytterligare flexibilitet till standard-avsökning beteende. När du använder anpassade avsökningar, kan användare konfigurera avsökningsintervallet, URL: en, och sökvägen för att testa och hur många misslyckade svar att godkänna innan du markerar backend-poolen-instans som skadad. Följande ytterligare egenskaper har lagts till.

| Avsökningen egenskapen | Beskrivning |
| --- | --- |
| Namn |Namnet på avsökningen. Det här namnet används för att referera till avsökning i backend-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen använder protokollet som definieras i backend-HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när multisite är konfigurerad på Application Gateway. Detta skiljer sig från den virtuella datorns värdnamn. |
| Sökväg |Relativa sökvägen för avsökningen. Giltig sökväg som börjar med ”/”. Avsökningen skickas till \<protokollet\>://\<värden\>:\<port\>\<sökväg\> |
| Intervall |Avsökningsintervall i sekunder. Det här är tidsintervallet mellan två på varandra följande avsökningar. |
| Time-out |Avsökning tidsgräns i sekunder. Om ett giltigt svar inte tas emot inom denna tidsgräns, markeras avsökningen som misslyckat. |
| Tröskelvärde för Ej felfri |Avsökning för antal nya försök. Backend-server markeras när antalet upprepade fel når tröskelvärde för ej felfri. |

### <a name="solution"></a>Lösning

Verifiera att anpassade Hälsoavsökning har konfigurerats korrekt som tabellen ovan. Utöver föregående felsökningsstegen också kontrollera följande:

* Kontrollera att avsökningen har angetts korrekt enligt den [guide](application-gateway-create-probe-ps.md).
* Om Application Gateway har konfigurerats för en enda plats, som standard värden namnet anges som `127.0.0.1`, såvida inte annat har konfigurerats i anpassad avsökning.
* Se till att ett anrop till http://\<värden\>:\<port\>\<sökväg\> returnerar ett HTTP-Resultatkod 200.
* Kontrollera att intervallet, timeout och UnhealtyThreshold är inom det godkända intervallen.
* Om du använder en HTTPS-avsökning, ska du se till att kräver inte backend-servern SNI genom att konfigurera en fallback-certifikatet på själva backend-servern.

## <a name="request-time-out"></a>Timeout för begäran

### <a name="cause"></a>Orsak

När en begäran tas emot, Application Gateway gäller de konfigurerade reglerna för begäran och dirigerar den till en backend-poolen-instans. Det väntar ett konfigurerbart tidsintervall för ett svar från backend-Server-instans. Det här intervallet är som standard **30 sekunder**. Om Application Gateway inte tar emot något svar från backend-programmet i det här intervallet, se användarbegäran felet 502.

### <a name="solution"></a>Lösning

Application Gateway kan du konfigurera den här inställningen via BackendHttpSetting, som sedan kan tillämpas på olika pooler. Olika serverdels-pooler kan ha olika BackendHttpSetting och därmed olika begäran timeout konfigurerats.

```powershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Tom BackendAddressPool

### <a name="cause"></a>Orsak

Om Application Gateway inte har några virtuella datorer eller VM-skalningsuppsättning konfigurerats i backend-adresspoolen, den kan inte skicka någon kundkrav och genererar en felaktig gateway-fel.

### <a name="solution"></a>Lösning

Kontrollera att backend-adresspoolen inte är tom. Detta kan göras via PowerShell, CLI eller portalen.

```powershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Utdata från föregående cmdleten ska innehålla icke-tom backend adresspoolen. Följande är ett exempel där två pooler returneras som är konfigurerade med FQDN eller IP-adresser för serverdelens virtuella datorer. Etableringsstatus för BackendAddressPool måste vara ”klar”.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Felaktiga instanser i BackendAddressPool

### <a name="cause"></a>Orsak

Om alla instanser av BackendAddressPool inte är felfria behöver alla serverdelar till route användarbegäran om att inte i Application Gateway. Detta kan också vara fallet när serverdels-instanser är felfria men har inte programmet distribueras.

### <a name="solution"></a>Lösning

Kontrollera att instanserna är felfri och programmet har konfigurerats korrekt. Kontrollera om backend-instanserna är kunna svara på ett ping från en annan virtuell dator i samma virtuella nätverk. Om du har konfigurerats med en offentlig slutpunkt, se till att begäran från en webbläsare till webbprogrammet funktionsduglig.

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet kan du öppna en [supportärende](https://azure.microsoft.com/support/options/).

