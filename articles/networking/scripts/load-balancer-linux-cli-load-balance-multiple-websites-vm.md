---
title: "Azure CLI skriptexempel - belastningsutjämning för flera webbplatser med Azure CLI | Microsoft Docs"
description: "Azure CLI skriptexempel - belastningsutjämning för flera webbplatser till samma virtuella dator"
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 72a780f1870d05a714aaeec879004b6f06d8bb7f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="load-balance-multiple-websites"></a>Belastningsutjämning för flera webbplatser

Det här exemplet i skriptet skapar ett virtuellt nätverk med två virtuella datorer (VM) som är medlemmar i en tillgänglighetsuppsättning. En belastningsutjämnare dirigerar trafik för två olika IP-adresser till de två virtuella datorerna. När skriptet har körts kan du distribuera programvara för webbserver för virtuella datorer och värden flera webbplatser, var och en med sin egen IP-adress.

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

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk, belastningsutjämnare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [Skapa AZ network vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [Skapa AZ nätverket offentliga-ip](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress med en statisk IP-adress och en tillhörande DNS-namn. |
| [Skapa AZ nätverket lb](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Skapar en Azure belastningsutjämnare. |
| [Skapa AZ nätverket lb avsökning](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Skapar en belastningsutjämningsavsökning. En belastningsutjämningsavsökning används för att övervaka varje virtuell dator i uppsättningen av belastningen belastningsutjämnaren. Om någon virtuell dator blir otillgänglig, dirigeras trafik inte till den virtuella datorn. |
| [Skapa AZ nätverket lb regel](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Skapar en regel för belastningsutjämnare. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer på belastningsutjämnaren, dirigeras till port 80 något av de virtuella datorerna i uppsättningen av belastningen belastningsutjämnaren. |
| [Skapa AZ nätverket lb klientdels-ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Skapa en klientdelens IP-adress för belastningsutjämnaren. |
| [Skapa AZ lb-nätverksadresspool](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Skapar en backend-adresspool. |
| [Skapa AZ nätverket nic](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Skapar ett virtuellt nätverkskort och kopplar den till virtuellt nätverk och undernät. |
| [Skapa AZ vm tillgänglighetsuppsättning](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garantera programmets drifttid genom att sprida virtuella datorer mellan fysiska resurser så att hela uppsättningen inte sker om fel inträffar. |
| [Skapa AZ nätverket nic ip-konfiguration](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Skapar en IP-confiuration. Du måste ha funktionen Microsoft.Network/AllowMultipleIpConfigurationsPerNic aktiverad för din prenumeration. Bara en konfiguration kan utses som den primära IP-konfigurationen per NIC, med hjälp av flaggan--gör primär. |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Skapar den virtuella datorn och ansluter till nätverkskort, virtuella nätverk, undernät och NSG. Det här kommandot anger också avbildningen av virtuella datorn ska användas och administrativa autentiseringsuppgifter.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare nätverk CLI skriptexempel finns i den [dokumentation för Azure-nätverk – översikt](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
