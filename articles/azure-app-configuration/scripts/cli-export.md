---
title: Exempel på Azure CLI-skript – exportera från ett Azure App Configuration Store
titleSuffix: Azure App Configuration
description: Använda Azure CLI-skript för att exportera konfiguration från Azure App-konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 3e014504fc1e94c3b323a49b408fa992de00d14a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523638"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportera från ett Azure App Configuration-arkiv

Det här exempelskriptet exporterar nyckelvärden från ett Azure App Configuration-arkiv.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att exportera från ett App Configuration Store. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az appconfig kv export](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Exporterar från en appkonfigurationsbutiksresurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för appkonfiguration finns i [CLI-exempel på Azure App Configuration CLI](../cli-samples.md).
