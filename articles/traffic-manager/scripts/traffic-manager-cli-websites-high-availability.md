---
title: Dirigera trafik för HA-program - Azure CLI - Traffic Manager
description: Azure CLI-skriptexempel - Dirigera trafik för hög tillgänglighet av program
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: e108fbe54ce67d4eb354319450259fc410462c53
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460621"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Dirigera trafik för hög tillgänglighet för program med Azure CLI

Det här skriptet skapar en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två slutpunkter för Traffic Manager. Traffic Manager dirigerar trafik till programmet i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen inte är tillgänglig. Innan du kör skriptet måste du ändra värdena MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. När du har kört skriptet kan du komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptexemplet har körts kan kommandot follow användas för att ta bort resursgruppen, App Service-appen och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Skapar en App Service-plan. Det här är som en servergrupp för din Azure-webbapp. |
| [az webapp webben skapa](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Skapar en Azure-webbapp i App Service-planen. |
| [az network traffic-manager profil skapa](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Skapar en Azure Traffic Manager-profil. |
| [az network traffic-manager slutpunkt skapa](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare apptjänst CLI-skriptexempel finns i [Azure Networking-dokumentationen](../cli-samples.md).
