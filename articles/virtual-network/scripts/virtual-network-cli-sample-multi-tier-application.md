---
title: Azure CLI-skript exempel – skapa ett nätverk för flera nivåer program | Microsoft Docs
description: Azure CLI-skript exempel – skapa ett virtuellt nätverk för program på flera nivåer.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: fdccf145b9cd9d64076cd6c181420adbc992c6a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-network-for-multi-tier-applications"></a>Skapa ett nätverk för flera nivåer program

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Trafik till undernätet för frontend är begränsad till http- och SSH, medan trafik till backend-undernät är begränsad till MySQL port 3306. När du har kört skriptet har två virtuella datorer i varje undernät som du kan distribuera webbservern och MySQL programvaran till.

Du kan köra skriptet från Azure [moln Shell](https://shell.azure.com/bash), eller från en lokal installation av Azure CLI. Om du använder CLI lokalt skriptet kräver att du har version 2.0.28 eller senare. Du hittar den installerade versionen genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommando-specifika dokumentation:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Skapar en Azure-nätverket och frontend-undernät. |
| [Skapa AZ undernät](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar en backend-undernät. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverket frontend och backend-undernät. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Skapar säkerhetsgrupper (NSG) som är kopplade till frontend och backend-undernät för nätverket. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [az vm create](/cli/azure/vm#az_vm_create) | Skapar virtuella datorer och bifogar ett nätverkskort på varje virtuell dator. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare virtuella nätverk CLI skriptexempel finns i [virtuella nätverk CLI exempel](../cli-samples.md).
