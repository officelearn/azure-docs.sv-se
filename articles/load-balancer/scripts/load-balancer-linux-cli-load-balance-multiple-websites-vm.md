---
title: CLI-exempel – Belastningsutjämning av flera webbplatser med Azure CLI | Microsoft Docs
description: Det här exemplet på CLI-skript visar hur du belastningsutjämnar flera webbplatser på samma virtuella dator
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: a8a7514624387d0fc00d32e4d47042e4a3b130f0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444427"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Exempel på Azure CLI-skript: Belastningsutjämning av flera webbplatser

Det här exemplet på Azure CLI-skript skapar ett virtuellt nätverk med två virtuella datorer som är medlemmar i en tillgänglighetsuppsättning. En lastbalanserare dirigerar trafik för två olika IP-adresser till de två virtuella datorerna. När du har kört skriptet kan du distribuera webbserverprogramvara för virtuella datorer och vara värd för flera webbplatser, var och en med egen IP-adress.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuellt nätverk, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Skapar en Azure-lastbalanserare. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | Skapar en lastbalanseringsavsökning. En lastbalanseringsavsökning används för att övervaka varje virtuell dator i lastbalanseringsuppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Skapar en lastbalanseringsregel. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer vid lastbalanseraren dirigeras den till port 80, en av de virtuella datorerna i lastbalanseringsuppsättningen. |
| [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) | Skapa en klientdels-IP-adress för lastbalanseraren. |
| [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) | Skapar en serverdelsadresspool. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket och undernätet. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garanterar programmets drifttid genom att fördela virtuella datorer mellan fysiska resurser så att hela uppsättningen inte berörs om något fel inträffar. |
| [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) | Skapar en IP-konfiguration. Du måste ha funktionen Microsoft.Network/AllowMultipleIpConfigurationsPerNic aktiverad för din prenumeration. Bara en konfiguration kan utses som den primära IP-konfigurationen per NIC, med hjälp av flaggan --make-primary. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az-vm-availability-set-create) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även vilken avbildning av virtuell dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare exempel på CLI-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
