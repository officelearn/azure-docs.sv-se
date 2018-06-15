---
title: Azure 2.0-exempel – Skapa en VM-skalningsuppsättning | Microsoft Docs
description: Azure CLI 2.0-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8eda45a5e886f755f9f1850b146fd0b0521d1cba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246465"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Skapa en VM-skalningsuppsättning med Azure CLI 2.0
Det här skriptet skapar en Azure VM-skalningsuppsättning med ett Ubuntu-operativsystem och relaterade nätverksresurser inklusive en belastningsutjämnare. När skriptet har körts får du åtkomst till de virtuella datorinstanserna via SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa en resursgrupp, en VM-skalningsuppsättning och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Skapar VM-skalningsuppsättningen och ansluter den till det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen. En belastningsutjämnare skapas även för att distribuera trafik till flera virtuella datorinstanser. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](/cli/azure/ad/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure CLI 2.0 finns i [Azure CLI 2.0-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure CLI 2.0-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för VM-skalningsuppsättningar i Azure](../cli-samples.md).