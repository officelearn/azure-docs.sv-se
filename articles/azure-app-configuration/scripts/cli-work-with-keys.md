---
title: Azure CLI-skriptexempel – Arbeta med nyckelvärden i ett Azure App Configuration-arkiv | Microsoft Docs
description: Ger information om hur du arbetar med nyckelvärden i ett Azure App Configuration-arkiv
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
ms.openlocfilehash: 9288ea08da6335dd29e7a15a9bc871b76c1ce7e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60235157"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Arbeta med nyckelvärden i ett Azure App Configuration-arkiv

Det här exempelskriptet skapar ett nytt nyckelvärde i ett Azure App Configuration-arkiv, visar en lista över alla befintliga nyckelvärden, uppdaterar värdet för den nya nyligen skapade nyckeln och tar slutligen bort det.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Du behöver installera Azure App Configuration CLI-tillägget först genom att köra följande kommando:

        az extension add -n appconfig

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att utföra åtgärder på nyckelvärden i ett appkonfigurationsarkiv. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig) | Skapar eller uppdaterar ett nyckelvärde. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig) | Visar en lista över nyckelvärden i ett appkonfigurationsarkiv. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig) | Tar bort ett nyckelvärde. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Configuration CLI-skriptexempel finns i [dokumentationen för Azure App Configuration](../cli-samples.md).
