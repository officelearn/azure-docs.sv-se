---
title: "Skriptexempel Azure CLI - vägtrafik via en virtuell nätverksenhet | Microsoft Docs"
description: "Azure CLI skriptexempel - vägtrafik via en virtuell nätverksenhet för brandväggen."
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
ms.openlocfilehash: 78091b515c00591a4af8d807945475b6be50188a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Dessutom skapas en virtuell dator med IP-vidarebefordring är aktiverat för att vidarebefordra trafik mellan två undernät. Du kan distribuera programvara för nätverk, till exempel en brandvägg för programmet, till den virtuella datorn när skriptet har körts.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ network vnet](/cli/azure/network/vnet#create) | Skapar en Azure-nätverket och frontend-undernät. |
| [Skapa AZ undernät](/cli/azure/network/vnet/subnet#create) | Skapar backend- och DMZ undernät. |
| [Skapa AZ nätverket offentliga-ip](/cli/azure/network/public-ip#create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [Skapa AZ nätverket nic](/cli/azure/network/nic#create) | Skapar ett virtuellt nätverksgränssnitt och aktivera IP-vidarebefordring för den. |
| [Skapa AZ nätverket nsg](/cli/azure/network/nsg#create) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [Skapa AZ nätverket nsg regel](/cli/azure/network/nsg/rule#create) | Skapar NSG-regler som tillåter HTTP och HTTPS-portar inkommande till den virtuella datorn. |
| [AZ network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#update)| Associerar NSG: er och vägtabeller till undernät. |
| [Skapa AZ-routningstabellen för nätverk](/cli/azure/network/route-table#create)| Skapar en routningstabell för alla flöden. |
| [Skapa AZ nätverksväg routningstabellen](/cli/azure/network/route-table/route#create)| Skapar vägar för att vidarebefordra trafik mellan undernät och Internet via den virtuella datorn. |
| [Skapa AZ vm](/cli/azure/vm#create) | Skapar en virtuell dator och bifogar nätverkskortet till den. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [ta bort grupp AZ](/cli/azure/group#delete) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](/cli/azure/overview).

Ytterligare nätverk CLI skriptexempel finns i den [dokumentation för Azure-nätverk – översikt](../cli-samples.md)