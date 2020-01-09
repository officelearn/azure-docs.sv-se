---
title: Exempel på Azure CLI-skript – skapa batch-konto – användar prenumeration
description: Det här skriptet skapar ett Batch-konto i användarprenumerationsläge. Det här kontot allokerar datornoder till din prenumeration.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 55429e0aafe978cfa6861d73b132ebcee26de493
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449695"
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

| Kommando | Anteckningar |
|---|---|
| [skapa az-rolltilldelning](/cli/azure/role) | Skapa en ny rolltilldelning för en användare, grupp eller tjänstens huvudnamn. |
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-create) | Skapar ett nyckelvalv. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) | Uppdatera säkerhetsprincipen för angivna nyckelvalvet. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
