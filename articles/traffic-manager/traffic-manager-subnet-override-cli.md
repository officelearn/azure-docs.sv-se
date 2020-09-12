---
title: Åsidosättning av Azure Traffic Manager-undernät med hjälp av Azure CLI | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Traffic Manager över gångs punkt kan användas för att åsidosätta Dirigerings metoden för en Traffic Manager profil för att dirigera trafik till en slut punkt baserat på IP-adressen för slutanvändare via fördefinierade IP-intervall till slut punkts mappningar.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: b66f1f0061f697349afae21f5f9c63a4089c2794
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401715"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager under näts Åsidosätt med Azure CLI

Med Traffic Manager under näts åsidosättning kan du ändra routningsmetod för en profil.  Att lägga till en åsidosättning dirigerar trafik baserat på slutanvändarens IP-adress med ett fördefinierat IP-intervall för slut punkts mappning. 

## <a name="how-subnet-override-works"></a>Hur under näts åsidosättning fungerar

När under näts åsidosättningar läggs till i en Traffic Manager-profil, kontrollerar Traffic Manager först om det finns en under näts åsidosättande för slutanvändarens IP-adress. Om en hittas dirigeras användarens DNS-fråga till motsvarande slut punkt.  Om ingen mappning hittas kommer Traffic Manager att återgå till profilens ursprungliga routningsmetod. 

IP-adressintervall kan anges som antingen CIDR-intervall (till exempel 1.2.3.0/24) eller som adress intervall (till exempel 1.2.3.4-5.6.7.8). De IP-intervall som är associerade med varje slut punkt måste vara unika för den slut punkten. Överlappande IP-intervall mellan olika slut punkter gör att profilen avvisas av Traffic Manager.

Det finns två typer av vägvals profiler som stöder under näts åsidosättningar:

* **Geografisk** -om Traffic Manager hittar en under näts ÅSIDOSÄTTNING för DNS-FRÅGAns IP-adress, kommer den att dirigera frågan till slut punkten oavsett hur hälsan är för slut punkten.
* **Prestanda** – om Traffic Manager hittar en under näts ÅSIDOSÄTTNING för DNS-FRÅGAns IP-adress dirigerar den bara trafiken till slut punkten om den är felfri.  Traffic Manager återgår till heuristiken för prestanda routning om det inte går att åsidosätta slut punkten för under nätet.

## <a name="create-a-traffic-manager-subnet-override"></a>Skapa en Traffic Manager under näts åsidosättning

Om du vill skapa en åsidosättning för Traffic Manager under nätet kan du använda Azure CLI för att lägga till undernät för åsidosättningen i Traffic Manager-slutpunkten.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Uppdatera Traffic Manager slut punkten med under nätets åsidosättning.
Använd Azure CLI för att uppdatera din slut punkt med [AZ Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Du kan ta bort IP-adressintervall genom att köra [slut punkten för AZ Network Traffic-Manager](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) med alternativet **--Remove** .

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Efterföljande moment
Läs mer om hur du Traffic Manager [metoder för trafik cirkulation](traffic-manager-routing-methods.md).

Lär dig mer om [routningsmetod för under näts trafik](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)