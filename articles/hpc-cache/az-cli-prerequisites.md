---
title: Krav för Azure CLI för Azure HPC cache
description: Installations steg innan du kan använda Azure CLI för att skapa eller ändra en Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87100332"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurera Azure CLI för Azure HPC Cache

Följ de här stegen för att förbereda din miljö innan du använder Azure CLI för att skapa eller hantera en Azure HPC-cache.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Installera Azure CLI

Azure HPC-cache kräver version 2,7 eller senare av Azure CLI. Kör `az --version` för att se vilken version och vilka beroende bibliotek som är installerade. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Installera Azure HPC cache-tillägget

Azure HPC cache-funktioner ingår inte i huvud kods basen, så du måste också installera tilläggs referensen. Följ instruktionerna nedan.

1. Logga in

   Logga in med kommandot [AZ login](/cli/azure/reference-index#az-login) om du använder en lokalt installerad version av cli.

    ```azurecli
    az login
    ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

   > [!TIP]
   > Om du har flera prenumerationer måste du välja en. Välj den när du startar en Cloud Shell-session i Azure-portalen eller följ instruktionerna i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) för att ställa in din prenumeration från kommando raden.

2. Installera Azure CLI-tillägget

   Azure HPC-funktionerna tillhandahålls som ett Azure CLI-tillägg – det är ännu inte en del av kärnan CLI-paketet. Du måste installera tilläggs referensen innan du kan använda den.

   Azure CLI-tillägg ger dig till gång till experiment-och för hands versions kommandon. Läs mer om tillägg, inklusive uppdatering och avinstallation, i [använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

   Installera tillägget för Azure HPC cache genom att köra följande kommando:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Ange standard resurs grupp (valfritt)

De flesta av HPC-cache-kommandona kräver att du överför cachens resurs grupp. Du kan ställa in standard resurs gruppen genom att använda [AZ konfigurera](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Nästa steg

När du har installerat Azure CLI-tillägget och loggat in kan du använda Azure CLI för att skapa och hantera Azure HPC cache-system.

* [Skapa en Azure HPC-cache](hpc-cache-create.md)
* [Azure CLI HPC-cache-dokumentation](/cli/azure/ext/hpc-cache/hpc-cache)
