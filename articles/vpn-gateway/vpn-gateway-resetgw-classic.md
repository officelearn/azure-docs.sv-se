---
title: Återställa Azure VPN gateway för att återupprätta IPsec-tunnlar | Microsoft Docs
description: Den här artikeln visar hur du återställer din Azure VPN Gateway för att återupprätta IPsec-tunnlar. Artikeln gäller VPN-gatewayer i både klassiska och Resource Manager-distributionsmodeller.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: cherylmc
ms.openlocfilehash: d09f03aed8a739eaaaf8c60c379a746551d3b6f8
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009893"
---
# <a name="reset-a-vpn-gateway"></a>Återställ VPN Gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Den här artikeln hjälper dig att återställa din VPN-gateway.

### <a name="what-happens-during-a-reset"></a>Vad händer under en återställning?

En VPN-gateway består av två VM-instanser som körs i en aktiv-standby-konfiguration. När du återställer gatewayen startar den om gatewayen och sedan återställer konfigurationerna för flera platser för. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.

När du utfärda kommandot för att återställa gatewayen, startas den aktuella aktiva instansen av Azure VPN-gateway om omedelbart. Det blir en kort paus under redundansen från den aktiva instansen (som startas om), till vänteläges-instansen. Pausen bör vara kortare än en minut.

Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta kommer att medföra ett längre uppehåll i VPN-anslutningen, cirka 2–4 minuter, för att de virtuella datorerna ska slutföra omstarterna.

Efter två omstarter, om du fortfarande har problem med nätverksanslutningen mellan olika platser, öppna en supportbegäran från Azure-portalen.

## <a name="before"></a>Innan du börjar

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Att kontrollera och korrigera konfigurationerna för dina lokala och Azure VPN-gatewayer du behöver du inte onödiga omstarter och avbrott i andra fungerande anslutningar på gatewayerna.

Kontrollera följande innan du återställer din gateway:

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

## <a name="portal"></a>Azure-portalen

Du kan återställa en Resource Manager VPN-gateway med Azure portal. Om du vill återställa en klassiska gateway finns i den [PowerShell](#resetclassic) steg.

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

1. Öppna den [Azure-portalen](https://portal.azure.com) och navigera till den virtuella nätverksgatewayen för Resource Manager som du vill återställa.
2. Klicka på ”Återställ” på bladet för den virtuella nätverksgatewayen.

  ![Vill du återställa bladet för VPN-Gateway](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Återställ bladet och klicka på den **återställa** knappen.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cmdlet: en om du vill återställa en gateway är **återställning AzVirtualNetworkGateway**. Innan du utför en återställning måste du kontrollera att du har den senaste versionen av den [Az PowerShell-cmdlet: ar](https://docs.microsoft.com/powershell/module/az.network). I följande exempel återställs en virtuell nätverksgateway med namnet VNet1GW i resursgruppen TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultat:

När du får ett returnerade resultat kan du anta att gateway-återställningen lyckades. Men finns det inget i det returnera resultat som explicit anger att återställningen lyckades. Om du vill titta på historiken för att se exakt när gateway-återställning uppstod, du kan visa den informationen i den [Azure-portalen](https://portal.azure.com). I portalen navigerar du till **{GatewayName} -> Resource Health**.

### <a name="resetclassic"></a>Klassisk distributionsmodell

Cmdlet: en om du vill återställa en gateway är **Reset-AzureVNetGateway**. Innan du utför en återställning måste du kontrollera att du har den senaste versionen av den [Service Management (SM) PowerShell-cmdletar](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). I följande exempel återställs gatewayen för ett virtuellt nätverk med namnet ”ContosoVNet”:

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

Om du vill återställa gatewayen, Använd den [az network vnet-gateway återställa](https://docs.microsoft.com/cli/azure/network/vnet-gateway) kommando. I följande exempel återställs en virtuell nätverksgateway med namnet VNet5GW i resursgruppen TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultat:

När du får ett returnerade resultat kan du anta att gateway-återställningen lyckades. Men finns det inget i det returnera resultat som explicit anger att återställningen lyckades. Om du vill titta på historiken för att se exakt när gateway-återställning uppstod, du kan visa den informationen i den [Azure-portalen](https://portal.azure.com). I portalen navigerar du till **{GatewayName} -> Resource Health**.
