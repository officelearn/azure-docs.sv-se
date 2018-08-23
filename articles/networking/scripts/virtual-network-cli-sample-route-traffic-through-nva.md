---
title: Azure CLI-skriptexempel – Dirigera trafik via en virtuell nätverksinstallation | Microsoft Docs
description: Azure CLI-skriptexempel – Dirigera trafik via en virtuell nätverksinstallation för brandvägg.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 62077f45d96e96a7fef35cf025740849d2b99445
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "42054148"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Dirigera trafik via en virtuell nätverksinstallation

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Det skapar också en virtuell dator med aktiverad IP-vidarebefordran för att dirigera trafik mellan de två undernäten. När du kört skriptet kan du distribuera nätverksprogramvara, till exempel ett brandväggsprogram, till den virtuella datorn.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar serverdels- och DMZ-undernät. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Skapar ett virtuellt nätverksgränssnitt och aktiverar IP-vidarebefordran för det. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Skapar NSG-regler som tillåter inkommande HTTP- och HTTPS-portar till den virtuella datorn. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Associerar NSG:erna och routningstabellerna med undernät. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Skapar en routningstabell för alla vägar. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Skapar vägar för att dirigera trafik mellan undernät och Internet via den virtuella datorn. |
| [az vm create](/cli/azure/vm#az_vm_create) | Skapar en virtuell dator och kopplar nätverkskortet till den. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare nätverk CLI-skriptexempel finns i den [Nätverksöversikt för Azure-dokumentation](../cli-samples.md)