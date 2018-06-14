---
title: Skriptexempel Azure CLI - vägtrafik via en virtuell nätverksenhet | Microsoft Docs
description: Azure CLI skriptexempel - vägtrafik via en virtuell nätverksenhet för brandväggen.
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
ms.openlocfilehash: 24b0c6873721ef196b1c0dc6d6a357f87a4a5e39
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846374"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Dessutom skapas en virtuell dator med IP-vidarebefordring är aktiverat för att vidarebefordra trafik mellan två undernät. Du kan distribuera programvara för nätverk, till exempel en brandvägg för programmet, till den virtuella datorn när skriptet har körts.

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

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [Skapa AZ network vnet](/cli/azure/network/vnet#az_network_vnet_create) | Skapar en Azure-nätverket och frontend-undernät. |
| [Skapa AZ undernät](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar backend- och DMZ undernät. |
| [Skapa AZ nätverket offentliga-ip](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [Skapa AZ nätverket nic](/cli/azure/network/nic#az_network_nic_create) | Skapar ett virtuellt nätverksgränssnitt och aktivera IP-vidarebefordring för den. |
| [Skapa AZ nätverket nsg](/cli/azure/network/nsg#az_network_nsg_create) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [Skapa AZ nätverket nsg regel](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Skapar NSG-regler som tillåter HTTP och HTTPS-portar inkommande till den virtuella datorn. |
| [AZ network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Associerar NSG: er och vägtabeller till undernät. |
| [Skapa AZ-routningstabellen för nätverk](/cli/azure/network/route-table#az_network_route_table_create)| Skapar en routningstabell för alla flöden. |
| [Skapa AZ nätverksväg routningstabellen](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Skapar vägar för att vidarebefordra trafik mellan undernät och Internet via den virtuella datorn. |
| [Skapa AZ vm](/cli/azure/vm#az_vm_create) | Skapar en virtuell dator och bifogar nätverkskortet till den. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare nätverk CLI skriptexempel finns i den [dokumentation för Azure-nätverk – översikt](../cli-samples.md)