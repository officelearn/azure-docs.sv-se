---
title: Skriptexempel Azure CLI - vägtrafik för hög tillgänglighet för program | Microsoft Docs
description: Skriptexempel Azure CLI - vägtrafik för hög tillgänglighet för program
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: kumud
ms.openlocfilehash: 48d265cd42954018d3482b74daf64ccf4d1b40cc
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958990"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Vidarebefordra trafik för hög tillgänglighet för program

Det här skriptet skapar en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. Traffic Manager dirigerar trafik till program i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen är inte tillgänglig. Innan du kör skriptet måste du ändra värdena MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. Du kan komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net när skriptet har körts.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Rensa distribution 

Efter skriptexempel har körts, följ-kommando kan användas för att ta bort resursgruppen, App Service-appen och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. Detta påminner om en servergrupp för din Azure webbapp. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Skapar ett Azure-webbapp i App Service-plan. |
| [Skapa AZ network traffic manager-profilen](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Skapar en Azure Traffic Manager-profil. |
| [Skapa AZ network traffic manager-slutpunkt](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Lägger till en slutpunkt för en Azure Traffic Manager-profilen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare App Service CLI skriptexempel finns i den [Azure nätverk dokumentationen](../cli-samples.md).
