---
title: Azure CLI-skriptexempel – Filtrera VM-nätverkstrafik | Microsoft Docs
description: Azure CLI-skriptexempel – Filtrera inkommande och utgående VM-nätverkstrafik.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 179a30524aed3063384673c11c4dd3b0424ce2a4
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473919"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-script-sample"></a>Filtrera inkommande och utgående VM-nätverkstrafik – skriptexempel

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP, HTTPS och SSH, medan utgående trafik till Internet från serverdelsundernätet inte är tillåten. När du kört skriptet har du en virtuell dator med två nätverkskort. Nätverkskorten är anslutna till olika undernät.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/bash), eller från en lokal installation av Azure CLI. Om du använder CLI lokalt kräver skriptet att du kör version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar ett serverdelsundernät. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Associerar NSG:er med undernät. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress för åtkomst till den virtuella datorn från Internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Skapar nätverkssäkerhetsgrupper (NSG) som är kopplade till klient- och serverdelsundernäten. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [az vm create](/cli/azure/vm#az_vm_create) | Skapar virtuella datorer och kopplar ett nätverkskort till varje virtuell dator. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Du kan hitta fler skriptexempel om virtuella nätverk för CLI i [CLI-exempel för virtuella nätverk](../cli-samples.md).
