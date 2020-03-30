---
title: Azure Traffic Manager-undernät åsidosätta med Azure CLI | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Traffic Manager-undernät åsidosättning kan användas för att åsidosätta routningsmetoden för en Traffic Manager-profil för att dirigera trafik till en slutpunkt baserat på slutanvändarens IP-adress via fördefinierat IP-intervall till slutpunktsmappningar.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938475"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Undernätsförehållning i Traffic Manager med Azure CLI

Med undernätsförståningen i Traffic Manager kan du ändra routningsmetoden för en profil.  Tillägget av en åsidosättning dirigerar trafik baserat på slutanvändarens IP-adress med ett fördefinierat IP-intervall till slutpunktsmappning. 

## <a name="how-subnet-override-works"></a>Så här fungerar åsidosättning av undernät

När åsidosättningar i undernät läggs till i en traffic manager-profil kontrollerar Traffic Manager först om det finns en åsidosättning av undernätet för slutanvändarens IP-adress. Om en sådan hittas dirigeras användarens DNS-fråga till motsvarande slutpunkt.  Om en mappning inte hittas kommer Traffic Manager att återgå till profilens ursprungliga routningsmetod. 

IP-adressintervallen kan anges som antingen CIDR-intervall (till exempel 1.2.3.0/24) eller som adressintervall (till exempel 1.2.3.4-5.6.7.8). IP-intervallen som är associerade med varje slutpunkt måste vara unika för den slutpunkten. Eventuell överlappning av IP-intervall mellan olika slutpunkter gör att profilen avvisas av Traffic Manager.

Det finns två typer av routningsprofiler som stöder åsidosättningar i undernät:

* **Geografisk** - Om Traffic Manager hittar en åsidosättning av undernätet för DNS-frågans IP-adress dirigeras frågan till slutpunkten oavsett slutpunktens hälsotillstånd.
* **Prestanda** - Om Traffic Manager hittar en åsidosättning av undernätet för DNS-frågans IP-adress dirigeras trafiken bara till slutpunkten om den är felfri.  Traffic Manager kommer att falla tillbaka till prestanda routning heuristiska om undernät åsidosättning slutpunkten inte är felfri.

## <a name="create-a-traffic-manager-subnet-override"></a>Skapa en åsidosättning av Trafikhanteraren

Om du vill skapa en åsidosättning av Traffic Manager-undernät kan du använda Azure CLI för att lägga till undernäten för åsidosättningen i Traffic Manager-slutpunkten.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Uppdatera Slutpunkten för Traffic Manager med undernätsförståning.
Använd Azure CLI för att uppdatera slutpunkten med uppdatering av [az-nätverkstrafikhanteraren](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

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

Du kan ta bort IP-adressintervallen genom att köra [slutpunktsuppdateringen för az-network traffic-manager](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) med alternativet **--remove.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Efterföljande moment
Läs mer om [trafikstyrningsmetoder i](traffic-manager-routing-methods.md)Traffic Manager .

Lär dig mer om [trafikroutningsmetoden för undernät](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)