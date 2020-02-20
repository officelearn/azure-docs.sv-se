---
title: Skript exempel för Azure CLI – importera till ett konfigurations lager för appar
titleSuffix: Azure App Configuration
description: Innehåller information och exempelskript för att importera till ett Azure App Configuration-arkiv
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b0704c6262451cef615355e871ce7f26005b3132
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467682"
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
