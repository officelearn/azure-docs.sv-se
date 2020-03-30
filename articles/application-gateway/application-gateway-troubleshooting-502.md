---
title: Felsöka fel i felaktig gateway – Azure Application Gateway
description: 'Lär dig hur du felsöker Application Gateway Server Error: 502 - Webbservern fick ett ogiltigt svar när du agerade som en gateway eller proxyserver.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130482"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Felsöka felaktig gateway i Application Gateway

Lär dig hur du felsöker felaktiga gatewayfel (502) som tas emot när du använder Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du har konfigurerat en programgateway visas ett av felen som kan visas "Serverfel: 502 – webbservern fick ett ogiltigt svar när den agerade som en gateway- eller proxyserver". Det här felet kan inträffa av följande huvudskäl:

* NSG, UDR eller Anpassad DNS blockerar åtkomst till serverda poolmedlemmar.
* Backend-datorer eller instanser av skalningsuppsättning för virtuella datorer svarar inte på standardhälsoavsökningen.
* Ogiltig eller felaktig konfiguration av anpassade hälsoavsökningar.
* Azure Application Gateways [backend-pool är inte konfigurerad eller tom](#empty-backendaddresspool).
* Ingen av de virtuella datorerna eller instanserna i [skalningsuppsättningen för virtuella datorer är felfria](#unhealthy-instances-in-backendaddresspool).
* [Begär time out- eller anslutningsproblem](#request-time-out) med användarbegäranden.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Nätverkssäkerhetsgrupp, användardefinierad väg eller anpassat DNS-problem

### <a name="cause"></a>Orsak

Om åtkomsten till serverdan blockeras på grund av en NSG, UDR eller anpassad DNS, kan programgatewayinstanser inte nå serverda poolen. Detta orsakar avsökningsfel, vilket resulterar i 502 fel.

NSG/UDR kan finnas antingen i undernätet för programgateway eller i undernätet där program-virtuella datorer distribueras.

På samma sätt kan förekomsten av en anpassad DNS i det virtuella nätverket också orsaka problem. En FQDN som används för serverda poolmedlemmar kanske inte matchas korrekt av den användarkonfigurerade DNS-servern för det virtuella nätverket.

### <a name="solution"></a>Lösning

Validera NSG-, UDR- och DNS-konfigurationen genom att gå igenom följande steg:

* Kontrollera NSG:er som är associerade med undernätet för programgateway. Se till att kommunikationen till backend inte är blockerad.
* Kontrollera UDR som är associerat med undernätet för programgateway. Kontrollera att UDR inte dirigerar trafik bort från backend-undernätet. Kontrollera till exempel om det finns routning till virtuella nätverksinstallationer eller standardvägar som annonseras till undernätet för programgateway via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Kontrollera effektiv NSG och rutt med backend VM

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Kontrollera förekomsten av anpassad DNS i det virtuella nätverket. DNS kan kontrolleras genom att titta på information om VNet-egenskaperna i utdata.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Om det finns, se till att DNS-servern kan lösa serverda poolmedlemmens FQDN korrekt.

## <a name="problems-with-default-health-probe"></a>Problem med standardhälsoavsökning

### <a name="cause"></a>Orsak

502 fel kan också vara vanliga indikatorer på att standardhälsoavsökningen inte kan nå backend-datorer.

När en programgatewayinstans etableras konfigureras automatiskt en standardhälsoavsökning till varje BackendAddressPool med hjälp av egenskaperna för Serverdelhttpsetting. Ingen användarindata krävs för att ställa in den här avsökningen. När en belastningsutjämningsregel har konfigurerats görs en association mellan en Serverdelhttpsetting och en BackendAddressPool. En standardavsökning har konfigurerats för var och en av dessa associationer och programgatewayen startar en periodisk hälsokontrollanslutning till varje instans i BackendAddressPool i porten som anges i elementet BackendHttpSetting. 

I följande tabell visas de värden som är associerade med standardhälsoavsökningen:

| Egenskapen Probe | Värde | Beskrivning |
| --- | --- | --- |
| Url för avsökning |`http://127.0.0.1/` |URL-sökväg |
| Intervall |30 |Avsökningsintervall i sekunder |
| Timeout |30 |Avsökning time-out i sekunder |
| Felfritt tröskelvärde |3 |Antalet försök för avsökningen. Backend-servern markeras efter att antalet avsökningsfel i följd når det felaktiga tröskelvärdet. |

### <a name="solution"></a>Lösning

* Kontrollera att en standardplats är konfigurerad och lyssnar klockan 127.0.0.1.
* Om BackendHttpSetting anger en annan port än 80 ska standardplatsen konfigureras för att lyssna på den porten.
* Anropet `http://127.0.0.1:port` ska returnera en HTTP-resultatkod på 200. Detta bör returneras inom 30-sekundersperioden.
* Kontrollera att porten som konfigurerats är öppen och att det inte finns några brandväggsregler eller Azure Network Security Groups, som blockerar inkommande eller utgående trafik på porten konfigurerad.
* Om Azure klassiska virtuella datorer eller molntjänst används med en FQDN eller en offentlig IP, se till att motsvarande [slutpunkt](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) öppnas.
* Om den virtuella datorn är konfigurerad via Azure Resource Manager och ligger utanför det virtuella nätverk där programgatewayen distribueras, måste en [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md) konfigureras för att tillåta åtkomst på önskad port.

## <a name="problems-with-custom-health-probe"></a>Problem med anpassad hälsoavsökning

### <a name="cause"></a>Orsak

Anpassade hälsoavsökningar ger ytterligare flexibilitet till standardutsökningsbeteendet. När du använder anpassade avsökningar kan du konfigurera avsökningsintervallet, URL:en, sökvägen som ska testas och hur många misslyckade svar som ska accepteras innan backend-poolinstansen markeras som felaktig.

Följande ytterligare egenskaper läggs till:

| Egenskapen Probe | Beskrivning |
| --- | --- |
| Namn |Sondens namn. Det här namnet används för att referera till avsökningen i http-inställningar för backend. |
| Protokoll |Protokoll som används för att skicka sonden. Avsökningen använder protokollet som definierats i http-inställningarna för backend |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera plats konfigureras på programgatewayen. Detta skiljer sig från vm-värdnamnet. |
| Sökväg |Sondens relativa sökväg. Den giltiga sökvägen börjar från '/'. Avsökningen skickas \<\>till\<\>protokoll\<\>\<:// värd: portsökväg\> |
| Intervall |Avsökningsintervallet på några sekunder. Detta är tidsintervallet mellan två på varandra följande sonder. |
| Timeout |Sondens time-out på några sekunder. Om ett giltigt svar inte tas emot inom den här time-out-perioden markeras avsökningen som misslyckad. |
| Felfritt tröskelvärde |Antalet försök för avsökningen. Backend-servern markeras efter att antalet avsökningsfel i följd når det felaktiga tröskelvärdet. |

### <a name="solution"></a>Lösning

Verifiera att den anpassade hälsoavsökningen är korrekt konfigurerad som föregående tabell. Förutom föregående felsökningssteg ska du även se till följande:

* Se till att sonden är korrekt angiven enligt [guiden](application-gateway-create-probe-ps.md).
* Om programgatewayen är konfigurerad för en enskild plats bör `127.0.0.1`värdnamnet som standard anges som , om inte annat konfigureras i anpassad avsökning.
* Kontrollera att ett anrop\<till\>\<http://\>\<\> värd: portsökvägen returnerar en HTTP-resultatkod på 200.
* Se till att intervall, timeout och unhealtyThreshold ligger inom de acceptabla intervallen.
* Om du använder en HTTPS-avsökning kontrollerar du att serveråtkomstservern inte kräver SNI genom att konfigurera ett reservcertifikat på serveringsservern själv.

## <a name="request-time-out"></a>Tidsförst utflykt för begäran

### <a name="cause"></a>Orsak

När en användarbegäran tas emot tillämpar programgatewayen de konfigurerade reglerna på begäran och dirigerar den till en backend-pool-instans. Den väntar på ett konfigurerbart tidsintervall för ett svar från backend-instansen. Som standard är det här intervallet **20** sekunder. Om programgatewayen inte får ett svar från backend-programmet i det här intervallet får användarbegäran ett 502-fel.

### <a name="solution"></a>Lösning

Med Application Gateway kan du konfigurera den här inställningen via BackendHttpSetting, som sedan kan tillämpas på olika pooler. Olika backend-pooler kan ha olika BackendHttpSetting och en annan tidsinställning för begäran konfigurerad.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Töm BackendAddressPool

### <a name="cause"></a>Orsak

Om programgatewayen inte har några virtuella datorer eller skala för virtuella datorer som konfigurerats i backend-adresspoolen, kan den inte dirigera någon kundbegäran och skickar ett felaktigt gatewayfel.

### <a name="solution"></a>Lösning

Kontrollera att backend-adresspoolen inte är tom. Detta kan göras antingen via PowerShell, CLI eller portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Utdata från föregående cmdlet ska innehålla en icke-tom backend-adresspool. I följande exempel visas två pooler som returneras och som har konfigurerats med en FQDN eller en IP-adress för serverda virtuella datorer. Avstämmelsen för BackendAddressPool måste vara "lyckades".

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

Om alla instanser av BackendAddressPool är felaktiga, har programgatewayen ingen backend att dirigera användarbegäran till. Detta kan också vara fallet när backend-instanser är felfria men inte har det nödvändiga programmet distribuerat.

### <a name="solution"></a>Lösning

Kontrollera att instanserna är felfria och att programmet är korrekt konfigurerat. Kontrollera om backend-instanserna kan svara på en ping från en annan virtuell dator i samma virtuella nätverk. Om den konfigureras med en offentlig slutpunkt kontrollerar du att en webbläsarbegäran till webbprogrammet kan hanteras.

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du en [supportbiljett](https://azure.microsoft.com/support/options/).

