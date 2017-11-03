---
title: Azure CLI Script exempel - skapa en virtuell Windows Server 2016-dator med NLB | Microsoft Docs
description: "Azure CLI Script exempel - skapa en virtuell Windows Server 2016-dator med Utjämning av nätverksbelastning"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: 9662e9a2ea128b609b76edfc4ec7b57016909761
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Läs in Utjämna trafiken mellan virtuella datorer med hög tillgänglighet

Det här exemplet i skriptet skapar allt som behövs för att köra flera Ubuntu virtuella datorer som konfigurerats i en hög tillgänglighet och läsa in belastningsutjämnade konfigurationen. När du har kört skriptet har tre virtuella datorer, ansluten till en Azure Tillgänglighetsuppsättning, och kan öppnas via en Azure belastningsutjämnare.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella, tillgänglighetsuppsättning, belastningsutjämnare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ network vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [Skapa AZ nätverket offentliga-ip](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress med en statisk IP-adress och en tillhörande DNS-namn. |
| [Skapa AZ nätverket lb](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Skapar ett Azure Utjämning av nätverksbelastning (NLB). |
| [Skapa AZ nätverket lb avsökning](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Skapar ett NLB-avsökning. Ett NLB-avsökningen används för att övervaka varje virtuell dator i NLB-uppsättningen. Om någon virtuell dator blir otillgänglig, dirigeras trafik inte till den virtuella datorn. |
| [Skapa AZ nätverket lb regel](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Skapar en regel för Utjämning av nätverksbelastning. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer på Utjämning av nätverksbelastning, dirigeras till port 80 något av de virtuella datorerna i NLB-uppsättningen. |
| [Skapa AZ nätverket lb inkommande nat-regel](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Skapar en regel för NLB (Network Address Translation Translation).  NAT-regler kan du mappa en port i NLB-klustret till en port på en virtuell dator. I det här exemplet skapas en NAT-regel för SSH-trafik på varje virtuell dator i NLB-uppsättningen.  |
| [Skapa AZ nätverket nsg](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Skapar en nätverkssäkerhetsgrupp (NSG), vilket är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [Skapa AZ nätverket nsg regel](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Skapar en NSG-regel för att tillåta inkommande trafik. I det här exemplet används port 22 för SSH-trafik. |
| [Skapa AZ nätverket nic](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Skapar ett virtuellt nätverkskort som kopplas till den virtuella nätverk och undernät NSG. |
| [Skapa AZ vm tillgänglighetsuppsättning](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garantera programmets drifttid genom att sprida virtuella datorer mellan fysiska resurser så att hela uppsättningen inte sker om fel inträffar. |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Skapar den virtuella datorn och ansluter till nätverkskort, virtuella nätverk, undernät och NSG. Det här kommandot anger också avbildningen av virtuella datorn ska användas och administrativa autentiseringsuppgifter.  |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
