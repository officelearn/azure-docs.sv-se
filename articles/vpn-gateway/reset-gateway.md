---
title: Återställa en Azure VPN-gateway för att återupprätta IPsec-tunneln
description: Återställ Azure-VPN Gateway för att återupprätta IPsec-tunnlar för VPN-gatewayer i både de klassiska och Resource Manager-distributions modellerna.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: cd25c7638bd7e178cdb963ba528cccefde6b9eca
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646516"
---
# <a name="reset-a-vpn-gateway"></a>Återställ VPN Gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Den här artikeln hjälper dig att återställa din VPN-gateway.

### <a name="what-happens-during-a-reset"></a>Vad händer under en återställning?

En VPN-gateway består av två VM-instanser som körs i en aktiv standby-konfiguration. När du återställer gatewayen startar den om gatewayen och tillämpar sedan de lokala konfigurationerna på den igen. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.

När du utfärdar kommandot för att återställa gatewayen, startas den aktuella aktiva instansen av Azure VPN-gatewayen om omedelbart. Det kommer att finnas en kort lucka under redundansväxlingen från den aktiva instansen (startas om) till instansen för vänte läge. Pausen bör vara kortare än en minut.

Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta leder till en längre lucka i VPN-anslutningen, upp till 45 minuter för virtuella datorer för att slutföra omstarterna.

Efter två omstarter, om du fortfarande har problem med anslutning till olika platser, måste du öppna en supportbegäran från Azure Portal.

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Genom att verifiera och korrigera konfigurationerna för dina lokala och Azure VPN-gatewayer slipper du onödiga omstarter och avbrott för de andra fungerande anslutningarna på gatewayerna.

Verifiera följande objekt innan du återställer din Gateway:

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

## <a name="azure-portal"></a><a name="portal"></a>Azure Portal

Du kan återställa en VPN-gateway för Resource Manager med hjälp av Azure Portal. Om du vill återställa en klassisk Gateway, se PowerShell-stegen för den [klassiska distributions modellen](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cmdleten för att återställa en gateway **återställs-AzVirtualNetworkGateway**. Kontrol lera att du har den senaste versionen av [PowerShell AZ-cmdlet: ar](/powershell/module/az.network)innan du utför en återställning. I följande exempel återställs en virtuell nätverksgateway som heter VNet1GW i resurs gruppen TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultat:

När du får ett retur resultat kan du anta att Gateway-återställningen lyckades. Det finns dock inget i det returnerade resultatet som indikerar att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när gatewayen har återställts kan du Visa informationen i [Azure Portal](https://portal.azure.com). I portalen navigerar du till **' GatewayName ' – > Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klassisk distributionsmodell

Cmdleten för att återställa en gateway **återställs-AzureVNetGateway**. Azure PowerShell-cmdletar för Service Management måste installeras lokalt på Skriv bordet. Du kan inte använda Azure Cloud Shell. Innan du utför en återställning kontrollerar du att du har den senaste versionen av [PowerShell-cmdletarna för Service Management (SM)](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets). När du använder det här kommandot kontrollerar du att du använder det fullständiga namnet på det virtuella nätverket. En klassisk virtuella nätverk som skapats med portalen har ett långt namn som krävs för PowerShell. Du kan visa det långa namnet med hjälp av "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml".

I följande exempel återställs gatewayen för ett virtuellt nätverk med namnet "Group TestRG1 TestVNet1" (som visar som bara "TestVNet1" i portalen):

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

För att återställa gatewayen använder du kommandot [AZ Network VNet-Gateway reset](/cli/azure/network/vnet-gateway) . I följande exempel återställs en virtuell nätverksgateway som heter VNet5GW i resurs gruppen TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultat:

När du får ett retur resultat kan du anta att Gateway-återställningen lyckades. Det finns dock inget i det returnerade resultatet som indikerar att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när gatewayen har återställts kan du Visa informationen i [Azure Portal](https://portal.azure.com). I portalen navigerar du till **' GatewayName ' – > Resource Health**.