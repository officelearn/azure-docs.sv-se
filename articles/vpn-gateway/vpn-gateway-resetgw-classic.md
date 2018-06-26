---
title: Återställa en Azure VPN-gateway för att återupprätta IPsec-tunnlar | Microsoft Docs
description: Den här artikeln beskriver hur du återställer din Azure VPN-Gateway för att återupprätta IPsec-tunnlar. Artikeln gäller för VPN-gatewayer i både klassiskt och Resource Manager distributionsmodellerna.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.openlocfilehash: 693996595f1a1b963c96e5f59e72fe4c9ca80ff0
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753048"
---
# <a name="reset-a-vpn-gateway"></a>Återställ VPN Gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Den här artikeln hjälper dig att återställa din VPN-gateway.

### <a name="what-happens-during-a-reset"></a>Vad som händer under en återställning?

En VPN-gateway består av två VM-instanser som körs i en konfiguration i aktivt vänteläge. När du återställer en gateway, startar du om gatewayen och tillämpa mellan lokala konfigurationer till den. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.

När du utfärda kommandot för att återställa gatewayen om den aktuella aktiva instansen av Azure VPN-gatewayen omedelbart. Det blir ett kort mellanrum under växling vid fel från den aktiva instansen (startas), till standby-instansen. Pausen bör vara kortare än en minut.

Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta kommer att medföra ett längre uppehåll i VPN-anslutningen, cirka 2–4 minuter, för att de virtuella datorerna ska slutföra omstarterna.

Efter två omstarter, om du fortfarande har problem med nätverksanslutningen mellan platser, öppnar du en supportbegäran från Azure-portalen.

## <a name="before"></a>Innan du börjar

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Kontrollera och korrigera konfigurationer för dina lokala kataloger och Azure VPN-gatewayer du behöver du inte onödiga omstarter och avbrott för de andra fungerande anslutningarna på gateway.

Kontrollera följande innan du återställer din gateway:

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

## <a name="portal"></a>Azure-portalen

Du kan återställa en Resource Manager VPN-gateway med Azure-portalen. Om du vill återställa en klassiska gateway finns på [PowerShell](#resetclassic) steg.

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

1. Öppna den [Azure-portalen](https://portal.azure.com) och navigera till den virtuella nätverksgatewayen för Resource Manager som du vill återställa.
2. Klicka på ”Återställ' på bladet för den virtuella nätverksgatewayen.

  ![Återställ VPN-Gateway-bladet](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Klicka på bladet Återställ den **återställa** knappen.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

Cmdlet för att återställa en gateway är **Återställ AzureRmVirtualNetworkGateway**. Innan du utför en återställning, kontrollera att du har den senaste versionen av den [Resource Manager PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0). I följande exempel återställer en virtuell nätverksgateway med namnet VNet1GW i resursgruppen TestRG1:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultat:

När du får ett returnerade resultat, kan du anta gateway återställningen lyckades. Men finns det inget i det returnera resultat som explicit anger att återställningen lyckades. Om du vill titta på händelser att se exakt när gateway-återställning uppstod, du kan visa informationen i den [Azure-portalen](https://portal.azure.com). I portalen, går du till **'GatewayName' -> Resource Health**.

### <a name="resetclassic"></a>Klassiska distributionsmodellen

Cmdlet för att återställa en gateway är **Återställ AzureVNetGateway**. Innan du utför en återställning, kontrollera att du har den senaste versionen av den [Service Management (SM) PowerShell-cmdlets](https://docs.microsoft.com/en-us/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). I följande exempel återställs gateway för virtuellt nätverk med namnet ”ContosoVNet”:

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Resultat:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

Om du vill återställa gatewayen, Använd den [az vnet-nätverksgateway återställa](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) kommando. I följande exempel återställer en virtuell nätverksgateway med namnet VNet5GW i resursgruppen TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultat:

När du får ett returnerade resultat, kan du anta gateway återställningen lyckades. Men finns det inget i det returnera resultat som explicit anger att återställningen lyckades. Om du vill titta på händelser att se exakt när gateway-återställning uppstod, du kan visa informationen i den [Azure-portalen](https://portal.azure.com). I portalen, går du till **'GatewayName' -> Resource Health**.