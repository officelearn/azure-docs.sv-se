---
title: Skript exempel för Azure CLI – väg trafik för hög tillgänglighet för program | Microsoft Docs
description: Skript exempel för Azure CLI – väg trafik för hög tillgänglighet för program
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: fdd54039cd3e039439c70e02b962e56d72efa715
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890267"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Dirigera trafik för hög tillgänglighet för program

Det här skriptet skapar en resurs grupp, två App Service-planer, två webb program, en Traffic Manager-profil och två Traffic Manager-slutpunkter. Traffic Manager dirigerar trafik till programmet i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen inte är tillgängligt. Innan du kör skriptet måste du ändra värdena för MyWebAppL1 och MyWebAppL2 till unika värden i Azure. När du har kört skriptet kan du komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Rensa distribution 

När skript exemplet har körts kan du använda kommandot Följ för att ta bort resurs gruppen, App Service app och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Skapar en App Service-plan. Detta är som en Server grupp för din Azure-webbapp. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Skapar en Azure-webbapp i App Service plan. |
| [AZ Network Traffic-Manager profil Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Skapar en Azure Traffic Manager-profil. |
| [AZ Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare App Service CLI-skript exempel finns i dokumentationen för [Azure-nätverk](../cli-samples.md).
