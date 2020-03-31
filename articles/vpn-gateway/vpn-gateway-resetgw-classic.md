---
title: Återställa en Azure VPN-gateway för att återupprätta IPsec-tunneln
description: I den här artikeln går du igenom att återställa din Azure VPN-gateway för att återupprätta IPsec-tunnlar. Artikeln gäller VPN-gateways i både den klassiska och Resurshanterarens distributionsmodeller.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244425"
---
# <a name="reset-a-vpn-gateway"></a>Återställ VPN Gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Den här artikeln hjälper dig att återställa din VPN-gateway.

### <a name="what-happens-during-a-reset"></a>Vad händer under en återställning?

En VPN-gateway består av två VM-instanser som körs i en konfiguration med aktiv vänteläge. När du återställer gatewayen startar den om gatewayen och återanvänder sedan de korslokala konfigurationerna på den. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.

När du utfärdar kommandot för att återställa gatewayen startas den aktuella aktiva instansen av Azure VPN-gatewayen omgående. Det kommer att finnas en kort lucka under redundansen från den aktiva instansen (startas om), till standby-instansen. Pausen bör vara kortare än en minut.

Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta kommer att orsaka en längre lucka på VPN-anslutningen, upp till 30 till 45 minuter för virtuella datorer att slutföra omstarter.

Efter två omstarter, om du fortfarande har problem med anslutningsövergripanden, öppnar du en supportbegäran från Azure-portalen.

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Genom att verifiera och korrigera konfigurationerna för dina lokala och Azure VPN-gateways sparar du från onödiga omstarter och avbrott för andra fungerande anslutningar på gateways.

Verifiera följande objekt innan du återställer gatewayen:

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

## <a name="azure-portal"></a><a name="portal"></a>Azure-portal

Du kan återställa en VPN-gateway för Resource Manager med Azure-portalen. Om du vill återställa en klassisk gateway läser du [PowerShell-stegen.](#resetclassic)

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

1. Öppna [Azure-portalen](https://portal.azure.com) och navigera till den virtuella nätverksgateway för Resource Manager som du vill återställa.
2. Klicka på Återställ på bladet för den virtuella nätverksgatewayen.

   ![Återställ bladet VPN Gateway](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Klicka på knappen Återställ på bladet **Återställ.**

## <a name="powershell"></a><a name="ps"></a>Powershell

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cmdleten för återställning av en gateway är **Reset-AzVirtualNetworkGateway**. Innan du återställer ska du se till att du har den senaste versionen av [PowerShell Az-cmdlets](https://docs.microsoft.com/powershell/module/az.network). I följande exempel återställs en virtuell nätverksgateway med namnet VNet1GW i testrg1-resursgruppen:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultat:

När du får ett returresultat kan du anta att återställningen av gatewayen lyckades. Det finns dock inget i returresultatet som uttryckligen anger att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när återställningen av gatewayen inträffade kan du visa informationen i [Azure-portalen](https://portal.azure.com). I portalen navigerar du till **"GatewayName" -> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klassisk distributionsmodell

Cmdleten för återställning av en gateway är **Reset-AzureVNetGateway**. Azure PowerShell-cmdlets för tjänsthantering måste installeras lokalt på skrivbordet. Du kan inte använda Azure Cloud Shell. Innan du gör en återställning kontrollerar du att du har den senaste versionen av [PowerShell-cmdlets (Service Management)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). När du använder det här kommandot kontrollerar du att du använder det fullständiga namnet på det virtuella nätverket. Klassiska virtuella nätverk som har skapats med hjälp av portalen har ett långt namn som krävs för PowerShell. Du kan visa det långa namnet med "Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml".

I följande exempel återställs gatewayen för ett virtuellt nätverk med namnet "GrupptestRG1 TestVNet1" (som visas som helt enkelt "TestVNet1" i portalen):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
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

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Om du vill återställa gatewayen använder du kommandot för återställning av [az-nätverksvnet-gateway.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) I följande exempel återställs en virtuell nätverksgateway med namnet VNet5GW i testrg5-resursgruppen:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultat:

När du får ett returresultat kan du anta att återställningen av gatewayen lyckades. Det finns dock inget i returresultatet som uttryckligen anger att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när återställningen av gatewayen inträffade kan du visa informationen i [Azure-portalen](https://portal.azure.com). I portalen navigerar du till **"GatewayName" -> Resource Health**.
