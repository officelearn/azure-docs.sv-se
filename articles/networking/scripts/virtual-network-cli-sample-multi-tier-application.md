---
title: "Azure CLI-skript exempel – skapa ett nätverk för flera nivåer program | Microsoft Docs"
description: "Azure CLI-skript exempel – skapa ett virtuellt nätverk för program på flera nivåer."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 9ce89f3aa84b7639b0367ba91304d97ae2754aba
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-network-for-multi-tier-applications"></a>Skapa ett nätverk för flera nivåer program

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Trafik till undernätet för frontend är begränsad till http- och SSH, medan trafik till backend-undernät är begränsad till MySQL port 3306. När du har kört skriptet har två virtuella datorer i varje undernät som du kan distribuera webbservern och MySQL programvaran till.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ network vnet](/cli/azure/network/vnet#az_network_vnet_create) | Skapar en Azure-nätverket och frontend-undernät. |
| [Skapa AZ undernät](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar en backend-undernät. |
| [Skapa AZ nätverket offentliga-ip](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [Skapa AZ nätverket nic](/cli/azure/network/nic#az_network_nic_create) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverket frontend och backend-undernät. |
| [Skapa AZ nätverket nsg](/cli/azure/network/nsg#az_network_nsg_create) | Skapar säkerhetsgrupper (NSG) som är kopplade till frontend och backend-undernät för nätverket. |
| [Skapa AZ nätverket nsg regel](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [Skapa AZ vm](/cli/azure/vm#az_vm_create) | Skapar virtuella datorer och bifogar ett nätverkskort på varje virtuell dator. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [ta bort grupp AZ](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](/cli/azure/overview).

Ytterligare nätverk CLI skriptexempel finns i den [dokumentation för Azure-nätverk – översikt](../cli-samples.md)