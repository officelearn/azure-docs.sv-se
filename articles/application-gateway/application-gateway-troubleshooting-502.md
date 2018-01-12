---
title: "Felsöka Azure Application Gateway felaktiga Gateway (502) | Microsoft Docs"
description: "Lär dig att felsöka program Gateway 502"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: e0099734a81cd8b1edf5cf80cb56b5c322a5feee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Felsöka Felaktig gateway-fel i Programgateway

Lär dig hur du felsöker Felaktig gateway (502) felmeddelanden när du använder Programgateway.

## <a name="overview"></a>Översikt

När du har konfigurerat en Programgateway är en av de fel som kan användarna få ”Serverfel: 502 - webbservern tog emot ett ogiltigt svar när den fungerade som en gateway eller proxy server”. Det här felet kan bero på följande huvudsakliga orsaker:

* NSG, UDR eller anpassad DNS blockerar åtkomst till backend-poolmedlemmar.
* Backend-virtuella datorer eller instanser av virtuella datorns skaluppsättning [inte svarar på hälsoavsökningen standard](#problems-with-default-health-probe.md).
* Ogiltig eller felaktig [konfigurationen av anpassade hälsoavsökningar](#problems-with-custom-health-probe.md).
* Azure Application Gateway [backend-adresspool har inte konfigurerats eller tom](#empty-backendaddresspool).
* Inga virtuella datorer eller instanser i [skaluppsättning för virtuell dator är felfria](#unhealthy-instances-in-backendaddresspool).
* [Begär timeout eller anslutning problem](#request-time-out) med användarförfrågningar.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Nätverkssäkerhetsgruppen, användardefinierad väg eller anpassade DNS-problem

### <a name="cause"></a>Orsak

Om åtkomst till backend har blockerats på grund av förekomsten av NSG, UDR eller anpassade DNS-kommer inte att kunna nå serverdelspoolen Programgateway instanser och skulle resultera i avsökningen fel som orsakar 502 fel. Observera att NSG/UDR kan finnas i programmet Gateway-undernät eller undernätet där de virtuella datorerna som programmet har distribuerats. På liknande sätt leda förekomst av anpassade DNS i VNET också till problem om FQDN som används för backend-poolmedlemmar och har inte matchats korrekt konfigurerade användaren DNS-servern för det virtuella nätverket.

### <a name="solution"></a>Lösning

Validera NSG, UDR och DNS-konfigurationen genom att gå igenom följande steg:
* Kontrollera NSG: er som är associerade med programmet Gateway-undernätet. Se till att kommunikationen till serverdelen inte ska blockeras.
* Kontrollera UDR som är associerade med programmet Gateway-undernätet. Se till att UDR inte är att dirigera trafik från backend-undernät - till exempel kontrollera för routning till nätverket virtuella installationer eller annonseras för Programgateway undernätet via ExpressRoute/VPN standardvägar.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Kontrollera effektiva NSG och vägen med serverdelens VM

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Kontrollera förekomst av anpassade DNS i virtuella nätverk. DNS kan kontrolleras genom att titta på detaljer för VNet-egenskaperna i utdata.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se till att DNS-servern kan matcha backend poolmedlem FQDN korrekt om den finns.

## <a name="problems-with-default-health-probe"></a>Problem med standard hälsoavsökningen

### <a name="cause"></a>Orsak

502 fel kan också vara ofta indikatorer hälsoavsökningen standard inte är kan nå backend-virtuella datorer. När en Programgateway instans etableras, konfigurerar den automatiskt en standard hälsoavsökningen till varje BackendAddressPool med hjälp av egenskaperna för BackendHttpSetting. Inga indata från användaren krävs för att ange den här avsökningen. När en regel för belastningsutjämning konfigureras görs mer specifikt en association mellan en BackendHttpSetting och BackendAddressPool. En standard-avsökning har konfigurerats för var och en av dessa associationer och Programgateway initierar en regelbundna hälsokontroller Kontrollera anslutning till varje instans i BackendAddressPool på porten som anges i BackendHttpSetting-elementet. Följande tabell visas de värden som är associerade med hälsoavsökningen standard.

| Avsökningen egenskapen | Värde | Beskrivning |
| --- | --- | --- |
| Avsökningswebbadress |http://127.0.0.1/ |URL-sökväg |
| Intervall |30 |Avsökningsintervall i sekunder |
| Timeout |30 |Avsökningen tidsgräns i sekunder |
| Tröskelvärde för ohälsosamt värde |3 |Avsökning för antal nya försök. Backend-server markeras när efterföljande avsökningen Felberäkning når tröskelvärde för ohälsosamt värde. |

### <a name="solution"></a>Lösning

* Se till att en standardwebbplats har konfigurerats och lyssnar på 127.0.0.1.
* Om BackendHttpSetting anger en annan port än 80, ska standardwebbplatsen konfigureras för att lyssna på porten.
* Anropet till http://127.0.0.1:port ska returnera ett HTTP-Resultatkod 200. Detta ska returneras inom tidsgränsen 30 sekunder.
* Se till att konfigurerade porten är öppen och att det inte finns några brandväggsregler Azure Nätverkssäkerhetsgrupperna, som blockerar inkommande eller utgående trafik på den konfigurerade porten.
* Om klassiska virtuella Azure-datorer eller tjänst i molnet används med FQDN eller offentlig IP-adress, se till att motsvarande [endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) öppnas.
* Om den virtuella datorn är konfigurerad via Azure Resource Manager och ligger utanför VNet där Application Gateway har distribuerats, [Nätverkssäkerhetsgruppen](../virtual-network/virtual-networks-nsg.md) måste konfigureras för att tillåta åtkomst på önskad port.

## <a name="problems-with-custom-health-probe"></a>Problem med anpassade hälsoavsökningen

### <a name="cause"></a>Orsak

Anpassade hälsoavsökningar ger ytterligare flexibilitet till standardvärdet sökning beteende. När du använder anpassade avsökningar kan användare konfigurera avsökningsintervall, URL-Adressen och sökvägen för att testa och hur många misslyckade svar att godkänna innan du markerar backend-pool-instans som ohälsosamt. Följande ytterligare egenskaper läggs till.

| Avsökningen egenskapen | Beskrivning |
| --- | --- |
| Namn |Namnet på avsökningen. Det här namnet används för att referera till avsökning i backend-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen används protokollet som definieras i backend-HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera platser har konfigurerats på Application Gateway. Detta skiljer sig från den virtuella datorns värdnamn. |
| Sökväg |Avsökningen relativ sökväg. Ogiltig sökväg startar från '/'. Avsökningen skickas till \<protokollet\>://\<värden\>:\<port\>\<sökväg\> |
| Intervall |Avsökning intervall i sekunder. Detta är tidsintervallet mellan två på varandra följande avsökningar. |
| Timeout |Avsökning tidsgräns i sekunder. Om ett giltigt svar inte emot inom denna tidsgräns, markeras avsökningen som misslyckad. |
| Tröskelvärde för ohälsosamt värde |Avsökning för antal nya försök. Backend-server markeras när efterföljande avsökningen Felberäkning når tröskelvärde för ohälsosamt värde. |

### <a name="solution"></a>Lösning

Verifiera att avsökning för anpassad hälsa är korrekt konfigurerad som tabellen ovan. Utöver föregående felsökningsstegen också kontrollera följande:

* Kontrollera att sonden har angetts korrekt enligt den [guiden](application-gateway-create-probe-ps.md).
* Om Application Gateway har konfigurerats för en viss plats, som standard värden ska namn anges som 127.0.0.1, såvida inte annat konfigurerade i anpassade avsökning.
* Se till att ett anrop till http://\<värden\>:\<port\>\<sökväg\> returnerar ett HTTP-Resultatkod 200.
* Kontrollera att intervallet, timeout och UnhealtyThreshold är inom intervallen som är godkända.
* Om du använder en HTTPS-avsökning ska du se till att kräver backend-servern SNI genom att konfigurera ett fallback-certifikatet på själva backend-servern.

## <a name="request-time-out"></a>Tidsgräns för begäran

### <a name="cause"></a>Orsak

När en begäran tas emot Programgateway gäller de konfigurerade reglerna för begäran och skickar den till en backend-adresspool. Det väntar på en konfigurerbar tidsperiod för svar från backend-instansen. Det här intervallet är som standard **30 sekunder**. Om Application Gateway inte får ett svar från serverprogrammet i det här intervallet visas användarbegäran ett 502 fel.

### <a name="solution"></a>Lösning

Programgateway tillåter användare att konfigurera den här inställningen via BackendHttpSetting, som sedan kan tillämpas på olika pooler. Olika backend-pooler kan ha olika BackendHttpSetting och därför olika begäran-timeout konfigurerats.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Tom BackendAddressPool

### <a name="cause"></a>Orsak

Om Application Gateway inte har några virtuella datorer eller skaluppsättningen för virtuell dator konfigureras i backend-adresspoolen den kan inte vidarebefordra alla kundbegäran och genererar en felaktig gateway-fel.

### <a name="solution"></a>Lösning

Kontrollera att backend-adresspool inte är tom. Detta kan göras antingen via PowerShell, CLI eller portal.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Utdata från den föregående cmdleten bör innehålla icke-tom backend-adresspool. Följande är ett exempel där två pooler returneras som har konfigurerats med FQDN eller IP-adresser för serverdel virtuella datorer. Etablering tillståndet för BackendAddressPool måste vara ”lyckades”.

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Feltillstånd instanser i BackendAddressPool

### <a name="cause"></a>Orsak

Om alla instanser av BackendAddressPool är ohälsosamt kan sedan Programgateway inte alla serverdelar på vägen begäran om användaren. Detta kan också vara fallet när backend-instanser är felfria men har inte programmet distribueras.

### <a name="solution"></a>Lösning

Kontrollera att instanserna är felfri och programmet har konfigurerats korrekt. Kontrollera om backend-instanserna är kan svara på ett ping från en annan virtuell dator i samma virtuella nätverk. Se till att begäran från en webbläsare till webbprogrammet är användbar om konfigurerats med en offentlig slutpunkt.

## <a name="next-steps"></a>Nästa steg

Om de föregående stegen inte löser problemet kan du öppna en [stöder biljett](https://azure.microsoft.com/support/options/).

