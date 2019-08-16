---
title: Återställa en Azure VPN-gateway för att återupprätta IPsec-tunnlar | Microsoft Docs
description: Den här artikeln vägleder dig genom att återställa Azure-VPN Gateway för att återupprätta IPsec-tunnlar. Artikeln gäller VPN-gatewayer både i den klassiska och i Resource Manager-distributions modellerna.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.openlocfilehash: 359773dad53f333b2f052dd5b5481645c72746da
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533929"
---
# <a name="reset-a-vpn-gateway"></a>Återställ VPN Gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Den här artikeln hjälper dig att återställa din VPN-gateway.

### <a name="what-happens-during-a-reset"></a>Vad händer under en återställning?

En VPN-gateway består av två VM-instanser som körs i en aktiv standby-konfiguration. När du återställer gatewayen startar den om gatewayen och tillämpar sedan de lokala konfigurationerna på den igen. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.

När du utfärdar kommandot för att återställa gatewayen, startas den aktuella aktiva instansen av Azure VPN-gatewayen om omedelbart. Det kommer att finnas en kort lucka under redundansväxlingen från den aktiva instansen (startas om) till instansen för vänte läge. Pausen bör vara kortare än en minut.

Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta leder till en längre lucka i VPN-anslutningen, upp till 45 minuter för virtuella datorer för att slutföra omstarterna.

Efter två omstarter, om du fortfarande har problem med anslutning till olika platser, måste du öppna en supportbegäran från Azure Portal.

## <a name="before"></a>Innan du börjar

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Genom att verifiera och korrigera konfigurationerna för dina lokala och Azure VPN-gatewayer slipper du onödiga omstarter och avbrott för de andra fungerande anslutningarna på gatewayerna.

Verifiera följande objekt innan du återställer din Gateway:

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

## <a name="portal"></a>Azure-portalen

Du kan återställa en VPN-gateway för Resource Manager med hjälp av Azure Portal. Om du vill återställa en klassisk Gateway, se [PowerShell](#resetclassic) -stegen.

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

1. Öppna den [Azure Portal](https://portal.azure.com) och navigera till den virtuella Nätverksgatewayen i Resource Manager som du vill återställa.
2. På bladet för den virtuella Nätverksgatewayen klickar du på Återställ.

   ![Återställ VPN Gateway bladet](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Klicka på knappen **Återställ** på Återställ-bladet.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cmdleten för att återställa en gateway **återställs-AzVirtualNetworkGateway**. Kontrol lera att du har den senaste versionen av [PowerShell AZ-cmdlet: ar](https://docs.microsoft.com/powershell/module/az.network)innan du utför en återställning. I följande exempel återställs en virtuell nätverksgateway som heter VNet1GW i resurs gruppen TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Medför

När du får ett retur resultat kan du anta att Gateway-återställningen lyckades. Det finns dock inget i det returnerade resultatet som indikerar att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när gatewayen har återställts kan du Visa informationen i [Azure Portal](https://portal.azure.com). I portalen navigerar du till **' GatewayName ' – > Resource Health**.

### <a name="resetclassic"></a>Klassisk distributions modell

Cmdleten för att återställa en gateway **återställs-AzureVNetGateway**. Azure PowerShell-cmdletar för Service Management måste installeras lokalt på Skriv bordet. Du kan inte använda Azure Cloud Shell. Innan du utför en återställning kontrollerar du att du har den senaste versionen av [PowerShell-cmdletarna för Service Management (SM)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). I följande exempel återställs gatewayen för ett virtuellt nätverk med namnet "ContosoVNet":

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Medför

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

För att återställa gatewayen använder du kommandot [AZ Network VNet-Gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) . I följande exempel återställs en virtuell nätverksgateway som heter VNet5GW i resurs gruppen TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Medför

När du får ett retur resultat kan du anta att Gateway-återställningen lyckades. Det finns dock inget i det returnerade resultatet som indikerar att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när gatewayen har återställts kan du Visa informationen i [Azure Portal](https://portal.azure.com). I portalen navigerar du till **' GatewayName ' – > Resource Health**.
