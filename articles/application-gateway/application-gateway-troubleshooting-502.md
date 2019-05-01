---
title: Felsök Azure Application Gateway felaktig Gateway (502)
description: Lär dig att felsöka Application Gateway 502
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697166"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Felsök Felaktig gateway-fel i Application Gateway

Lär dig mer om att Felsök Felaktig gateway (502) togs emot när du använder Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du har konfigurerat en application gateway, en av de fel som kan visas är ”Serverfel: 502 - Webbservern tog emot ett ogiltigt svar när den fungerade som gateway eller proxyserver. Det här felet kan inträffa av följande huvudsakliga skäl:

* NSG, UDR eller anpassad DNS blockerar åtkomsten till medlemmar i serverdelspoolen.
* Backend-virtuella datorer eller instanser av virtual machine scale Sets svarar inte på standard-hälsoavsökning.
* Ogiltig eller felaktig konfiguration av anpassade hälsoavsökningar.
* Azure Application Gateway [backend-poolen är inte konfigurerade eller tom](#empty-backendaddresspool).
* Ingen av de virtuella datorer och instanser i [virtual machine scale Sets är felfria](#unhealthy-instances-in-backendaddresspool).
* [Problem med timeout eller anslutning för begäran](#request-time-out) med användarbegäranden.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Nätverkssäkerhetsgrupp, användardefinierad väg eller anpassad DNS-problem

### <a name="cause"></a>Orsak

Om åtkomst till serverdelen blockeras på grund av en NSG, UDR eller anpassad DNS, kan inte application gateway-instanser nå backend-pool. Detta leder till avsökning fel, vilket resulterar i 502 fel.

NSG/UDR kan finnas i application gateway-undernätet eller undernätet där de virtuella Programdatorerna har distribuerats.

Förekomsten av en anpassad DNS i det virtuella nätverket kan på liknande sätt kan också orsaka problem. Ett FQDN som används för medlemmar i serverdelspool kan inte matchades korrekt Användarkonfigurerad DNS-servern för det virtuella nätverket.

### <a name="solution"></a>Lösning

Validera NSG, UDR och DNS-konfigurationen genom att gå igenom följande steg:

* Kontrollera NSG: er som är associerade med application gateway-undernätet. Se till att kommunikation till serverdelen inte är blockerad.
* Kontrollera UDR som är associerade med application gateway-undernätet. Se till att den användardefinierade vägen inte dirigera trafik från backend-undernät. Kontrollera till exempel routning för att virtuella installationer eller standardvägar som annonserats till application gateway-undernätet via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Kontrollera effektiva NSG och väg med den virtuella datorn på serversidan

```azurepowershell
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
Se till att DNS-servern kan matcha FQDN för backend-poolmedlem korrekt om den finns.

## <a name="problems-with-default-health-probe"></a>Problem med standard-hälsoavsökning

### <a name="cause"></a>Orsak

502 fel kan också vara vanliga indikatorer som standard hälsoavsökningen inte går att nå backend-virtuella datorer.

När en application gateway-instans etableras, konfigurerar den automatiskt en hälsoavsökning som standard ska varje BackendAddressPool med hjälp av egenskaperna för BackendHttpSetting. Inga indata från användaren krävs för att ställa in den här avsökningen. Mer specifikt när en regel för belastningsutjämning konfigureras, görs en association mellan en BackendHttpSetting och en BackendAddressPool. En standard-avsökning har konfigurerats för var och en av dessa associationer och application gateway startar en regelbundna Kontrollera anslutning till varje instans i BackendAddressPool på porten som anges i BackendHttpSetting-elementet. 

I följande tabell visas de värden som är associerade med standard-hälsoavsökning:

| Avsökningen egenskapen | Värde | Beskrivning |
| --- | --- | --- |
| Avsökningswebbadress |`http://127.0.0.1/` |URL-sökväg |
| Interval |30 |Avsökningsintervall i sekunder |
| Time-out |30 |Avsökningen tidsgräns i sekunder |
| Tröskelvärde för Ej felfri |3 |Avsökning för antal nya försök. Backend-server markeras när antalet upprepade fel når tröskelvärde för ej felfri. |

### <a name="solution"></a>Lösning

* Se till att en standardwebbplats har konfigurerats och lyssnar på 127.0.0.1.
* Om BackendHttpSetting anger en annan port än 80, ska standardwebbplatsen konfigureras för att lyssna på porten.
* Anropet till `http://127.0.0.1:port` ska returnera ett HTTP-Resultatkod 200. Detta ska returneras inom tidsgränsen för 30 sekunder.
* Se till att den konfigurerade porten är öppen och att det finns inga brandväggsregler eller Azure Nätverkssäkerhetsgrupperna, som blockerar inkommande eller utgående trafik på porten som konfigurerats.
* Om du använder Azure klassiska virtuella datorer eller en molntjänst med ett fullständigt domännamn eller en offentlig IP-adress, se till att motsvarande [endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) öppnas.
* Om den virtuella datorn är konfigurerad via Azure Resource Manager och ligger utanför det virtuella nätverket där application gateway distribueras en [Nätverkssäkerhetsgrupp](../virtual-network/security-overview.md) måste konfigureras för att tillåta åtkomst på önskad port.

## <a name="problems-with-custom-health-probe"></a>Problem med anpassade hälsoavsökning

### <a name="cause"></a>Orsak

Anpassade hälsoavsökningar ger ytterligare flexibilitet till standard-avsökning beteende. När du använder anpassade avsökningar kan konfigurera du avsökningsintervallet, URL: en, sökvägen till att testa och hur många misslyckade svar att godkänna innan du markerar backend-poolen-instans som skadad.

Följande ytterligare egenskaper har lagts till:

| Avsökningen egenskapen | Beskrivning |
| --- | --- |
| Namn |Namnet på avsökningen. Det här namnet används för att referera till avsökning i backend-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen använder protokollet som definieras i backend-HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när multisite är konfigurerad på application gateway. Detta skiljer sig från den virtuella datorns värdnamn. |
| Sökväg |Relativa sökvägen för avsökningen. Giltig sökväg som börjar med ”/”. Avsökningen skickas till \<protokollet\>://\<värden\>:\<port\>\<sökväg\> |
| Interval |Avsökningsintervall i sekunder. Det här är tidsintervallet mellan två på varandra följande avsökningar. |
| Time-out |Avsökning tidsgräns i sekunder. Om ett giltigt svar inte emot inom denna tidsgräns, markeras avsökningen som misslyckat. |
| Tröskelvärde för Ej felfri |Avsökning för antal nya försök. Backend-server markeras när antalet upprepade fel når tröskelvärde för ej felfri. |

### <a name="solution"></a>Lösning

Verifiera att anpassade Hälsoavsökning har konfigurerats korrekt som tabellen ovan. Utöver föregående felsökningsstegen också kontrollera följande:

* Kontrollera att avsökningen har angetts korrekt enligt den [guide](application-gateway-create-probe-ps.md).
* Om application gateway har konfigurerats för en enda plats, som standard värden namnet anges som `127.0.0.1`, såvida inte annat har konfigurerats i anpassad avsökning.
* Se till att ett anrop till http://\<värden\>:\<port\>\<sökväg\> returnerar ett HTTP-Resultatkod 200.
* Kontrollera att intervallet, Timeout och UnhealtyThreshold är inom det godkända intervallen.
* Om du använder en HTTPS-avsökning, ska du se till att kräver inte backend-servern SNI genom att konfigurera en fallback-certifikatet på själva backend-servern.

## <a name="request-time-out"></a>Timeout för begäran

### <a name="cause"></a>Orsak

När en begäran tas emot, application gateway gäller de konfigurerade reglerna för begäran och dirigerar den till en backend-poolen-instans. Det väntar ett konfigurerbart tidsintervall för ett svar från backend-Server-instans. Det här intervallet är som standard **20** sekunder. Om application gateway inte tar emot något svar från backend-programmet i det här intervallet, hämtar ett 502-fel i användarens begäran.

### <a name="solution"></a>Lösning

Application Gateway kan du konfigurera den här inställningen från BackendHttpSetting, som sedan kan tillämpas på olika pooler. Olika serverdels-pooler kan ha olika BackendHttpSetting och en annan begäran timeout som har konfigurerats.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Tom BackendAddressPool

### <a name="cause"></a>Orsak

Om application gateway inte har några virtuella datorer eller VM-skalningsuppsättning konfigurerats i backend-adresspoolen, den kan inte skicka någon kundkrav och skickar en felaktig gateway-fel.

### <a name="solution"></a>Lösning

Kontrollera att backend-adresspoolen inte är tom. Detta kan göras via PowerShell, CLI eller portalen.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Utdata från föregående cmdleten ska innehålla icke-tom backend adresspoolen. I följande exempel visas två pooler returneras som har konfigurerats med ett fullständigt domännamn eller en IP-adresser för serverdelens virtuella datorer. Etableringsstatus för BackendAddressPool måste vara ”klar”.

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

Om alla instanser av BackendAddressPool inte är felfria har inte application gateway alla serverdelar att dirigera användarförfrågan till. Det kan också vara fallet när serverdels-instanser är felfria men inte har distribuerat programmet.

### <a name="solution"></a>Lösning

Kontrollera att instanserna är felfri och programmet har konfigurerats korrekt. Kontrollera om backend-instanser kan svara på ett ping från en annan virtuell dator i samma virtuella nätverk. Om har konfigurerats med en offentlig slutpunkt, se till att begäran från en webbläsare till webbprogrammet är användbara.

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet kan du öppna en [supportärende](https://azure.microsoft.com/support/options/).

