---
title: CLI-exempel på gransknings-och Avancerat skydd – Azure SQL Database
description: Exempel skript för Azure CLI för att konfigurera gransknings-och Avancerat skydd i en Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 3df43af03fd536dfe50733bb6e10ff43d9f54834
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737110"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Använd CLI för att konfigurera SQL Database granskning och Avancerat skydd

Detta exempel på Azure CLI-skript konfigurerar SQL Database granskning och Avancerat skydd.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [AZ SQL DB audit-policy](/cli/azure/sql/db/audit-policy) | Anger granskningsprincipen för en databas. |
| [AZ SQL DB Threat-policy](/cli/azure/sql/db/threat-policy) | Ställer in en princip för avancerat skydd på en databas. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../../azure-sql/database/az-cli-script-samples-content-guide.md).
