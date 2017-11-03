---
title: "Skriptexempel Azure CLI - Filter VM-nätverkstrafik | Microsoft Docs"
description: "Azure CLI-skript exempel – filtrera inkommande och utgående VM-nätverkstrafik."
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
ms.openlocfilehash: 68ee013cff4e0be15af30239e0314f779f50177a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrera inkommande och utgående nätverkstrafik för VM

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Inkommande nätverkstrafik till undernätet frontend är begränsad till HTTP, HTTPS och SSH, medan utgående trafik till Internet från backend-undernät inte är tillåtet. När du har kört skriptet har du en virtuell dator med två nätverkskort. Varje nätverkskort är anslutet till ett annat undernät.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

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
| [Skapa AZ undernät](/cli/azure/network/vnet/subnet#create) | Skapar en backend-undernät. |
| [AZ network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#update) | Associerar NSG: er till undernät. |
| [Skapa AZ nätverket offentliga-ip](/cli/azure/network/public-ip#create) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [Skapa AZ nätverket nic](/cli/azure/network/nic#create) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverket frontend och backend-undernät. |
| [Skapa AZ nätverket nsg](/cli/azure/network/nsg#create) | Skapar säkerhetsgrupper (NSG) som är kopplade till frontend och backend-undernät för nätverket. |
| [Skapa AZ nätverket nsg regel](/cli/azure/network/nsg/rule#create) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [Skapa AZ vm](/cli/azure/vm#create) | Skapar virtuella datorer och bifogar ett nätverkskort på varje virtuell dator. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [ta bort grupp AZ](/cli/azure/group#delete) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](/cli/azure/overview).

Ytterligare nätverk CLI skriptexempel finns i den [dokumentation för Azure-nätverk – översikt](../cli-samples.md)