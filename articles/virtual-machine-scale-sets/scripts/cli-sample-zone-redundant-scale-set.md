---
title: Azure CLI-exempel – Zonredundant skalningsuppsättning | Microsoft Docs
description: Azure CLI-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
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
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 0a54889a93888ff3cbc50453ec6a10ade8304284
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411331"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Skapa en zonredundant VM-skalningsuppsättning med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning som kör Ubuntu över flera tillgänglighetszoner. När du kört skriptet har du åtkomst till den virtuella datorn över RDP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa en resursgrupp, en VM-skalningsuppsättning och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/ad/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](/cli/azure/vmss) | Skapar VM-skalningsuppsättningen och ansluter den till det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen. En lastbalanserare skapas även för att distribuera trafik till flera virtuella datorinstanser. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](/cli/azure/ad/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure CLI-skriptexempel för skalningsuppsättningar för virtuella datorer finns i [dokumentationen för Azure-skalningsuppsättningar för virtuella datorer](../cli-samples.md).
