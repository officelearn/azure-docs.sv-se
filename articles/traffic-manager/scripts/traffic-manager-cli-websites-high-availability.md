---
title: Azure CLI-skriptexempel – dirigera trafik för hög tillgänglighet för program | Microsoft Docs
description: Azure CLI-skriptexempel – dirigera trafik för hög tillgänglighet för program
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 6c610a1cddb0854878d4c2bd5531f88a1cf2ec51
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009112"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Dirigera trafik för hög tillgänglighet för program med hjälp av Azure CLI

Det här skriptet skapar en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. Traffic Manager dirigerar trafik till program i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen är inte tillgänglig. Innan du kör skriptet måste du ändra värdena för MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. När skriptet har körts kan komma du åt appen i den primära regionen med URL-mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Rensa distribution 

När exempelskriptet har körts kan följande kommando kan användas för att ta bort resursgruppen, App Service-app och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ-grupp](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [Skapa AZ appservice plan](https://docs.microsoft.com/cli/azure/appservice/plan) | Skapar en App Service-plan. Detta fungerar som en servergrupp för Azure-webbappen. |
| [Skapa AZ webapp web](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Skapar en Azure-webbapp i App Service-planen. |
| [Skapa AZ network traffic-manager-profil](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Skapar en Azure Traffic Manager-profil. |
| [Skapa AZ network traffic-manager endpoint](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare App Service CLI-skriptexempel finns i den [Azure Networking dokumentation](../cli-samples.md).
