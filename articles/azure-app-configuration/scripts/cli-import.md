---
title: Skript exempel för Azure CLI – importera till ett konfigurations lager för appar
titleSuffix: Azure App Configuration
description: Använd Azure CLI-skript – importera konfiguration till Azure App konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe4c22042c31d55575320d9c3823783902700f9f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494782"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importera till ett Azure App Configuration-arkiv

Det här exempel skriptet importerar nyckel värdes inställningar till en Azure App konfigurations lagring.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt, kräver den här artikeln att du kör Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att importera till ett konfigurations lager för appar. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importerar till en resurs för konfigurations Arkiv för appar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare skript exempel för programkonfigurations-CLI finns i [Azure App konfigurations-CLI-exempel](../cli-samples.md).
