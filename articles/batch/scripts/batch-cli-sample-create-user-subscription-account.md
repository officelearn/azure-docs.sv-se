---
title: Exempel på Azure CLI-skript – skapa batch-konto – användar prenumeration
description: Det här skriptet skapar ett Batch-konto i användarprenumerationsläge. Det här kontot allokerar datornoder till din prenumeration.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c60b9c3de5f2370d7bbc557d36db61d23d32ccf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494408"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-exempel: Skapa ett Batch-konto i användarprenumerationsläge

Det här skriptet skapar ett Batch-konto i användarprenumerationsläge. Ett konto som allokerar beräkningsnoder i prenumerationen måste autentiseras via en Azure Active Directory-token. Beräkningsnoderna som allokeras räknas in i kvoten för prenumerationens vCPU-kvot (kärna). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [skapa az-rolltilldelning](/cli/azure/role) | Skapa en ny rolltilldelning för en användare, grupp eller tjänstens huvudnamn. |
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Skapar ett nyckelvalv. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Uppdatera säkerhetsprincipen för angivna nyckelvalvet. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
