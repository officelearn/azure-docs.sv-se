---
title: Skriptexempel Azure CLI - vägtrafik via en virtuell nätverksenhet | Microsoft Docs
description: Azure CLI skriptexempel - vägtrafik via en virtuell nätverksenhet för brandväggen.
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
ms.openlocfilehash: 8506d67152a21586b2854891674d50f89d1a643c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Dessutom skapas en virtuell dator med IP-vidarebefordring är aktiverat för att vidarebefordra trafik mellan två undernät. Du kan distribuera programvara för nätverk, till exempel en brandvägg för programmet, till den virtuella datorn när skriptet har körts.

Du kan köra skriptet från Azure [moln Shell](https://shell.azure.com/bash), eller från en lokal installation av Azure CLI. Om du använder CLI lokalt skriptet kräver att du har version 2.0.28 eller senare. Du hittar den installerade versionen genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

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
| [Skapa AZ undernät](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar backend- och DMZ undernät. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Skapar ett virtuellt nätverksgränssnitt och aktivera IP-vidarebefordring för den. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Skapar NSG-regler som tillåter HTTP och HTTPS-portar inkommande till den virtuella datorn. |
| [AZ network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Associerar NSG: er och vägtabeller till undernät. |
| [Skapa AZ-routningstabellen för nätverk](/cli/azure/network/route-table#az_network_route_table_create)| Skapar en routningstabell för alla flöden. |
| [Skapa AZ nätverksväg routningstabellen](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Skapar vägar för att vidarebefordra trafik mellan undernät och internet via den virtuella datorn. |
| [az vm create](/cli/azure/vm#az_vm_create) | Skapar en virtuell dator och bifogar nätverkskortet till den. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare virtuella nätverk CLI skriptexempel finns i [virtuella nätverk CLI exempel](../cli-samples.md).
