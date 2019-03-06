---
title: Azure CLI-skriptexempel – Ta bort ett Azure App Configuration-arkiv | Microsoft Docs
description: Azure CLI-skriptexempel – Ta bort ett Azure App Configuration-arkiv
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3482cc14e73801af6d0db910ded84adf722bc6f4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884225"
---
# <a name="delete-an-azure-app-configuration-store"></a>Ta bort ett Azure App Configuration-arkiv

Det här exempelskriptet tar bort en instans av Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Du behöver installera Azure App Configuration CLI-tillägget först genom att köra följande kommando:

        az extension add -n appconfig

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#/bin/bash

# Delete an app configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att ta bort ett appkonfigurationsarkiv. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az appconfig delete](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-delete) | Tar bort en resurs för ett appkonfigurationsarkiv. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Configuration CLI-skriptexempel finns i [dokumentationen för Azure App Configuration](../cli-samples.md).
