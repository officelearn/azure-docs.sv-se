---
title: Exempel på Azure CLI-skript – Importera till ett appkonfigurationsarkiv
titleSuffix: Azure App Configuration
description: Använda Azure CLI-skript - Importera konfiguration till Azure App-konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523635"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importera till ett Azure App Configuration-arkiv

Det här exempelskriptet importerar nyckelvärdesinställningar till ett Azure App Configuration Store.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Information om hur du installerar eller uppgraderar finns [i Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att importera till ett App Configuration Store. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az appconfig kv import az appconfig kv import az appconfig kv import az app](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importerar till en appkonfigurationsbutiksresurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för appkonfiguration finns i [CLI-exempel på Azure App Configuration CLI](../cli-samples.md).
