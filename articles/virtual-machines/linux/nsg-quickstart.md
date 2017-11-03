---
title: "Öppna portar till en Linux-VM med Azure CLI 2.0 | Microsoft Docs"
description: "Lär dig hur du öppnar en port / skapa en slutpunkt för ditt Linux-VM med hjälp av Azure resource manager-distributionsmodellen och Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: d176187fe465264b5f433260de5178b48ca9dd4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Öppna portar och slutpunkter till en Linux-VM med Azure CLI
Du öppnar en port eller skapa en slutpunkt för en virtuell dator (VM) i Azure genom att skapa ett filter för nätverk på ett undernät eller Virtuella datorns nätverksgränssnitt. Du kan placera dessa filter som styr både inkommande och utgående trafik på en Nätverkssäkerhetsgrupp kopplad till den resurs som tar emot trafiken. Nu ska vi använda ett vanligt exempel på Internet-trafik på port 80. Den här artikeln visar hur du öppnar en port till en virtuell dator med Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](nsg-quickstart-nodejs.md).


## <a name="quick-commands"></a>Snabbkommandon
Så här skapar du en säkerhetsgrupp för nätverk och regler som du behöver senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login).

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup*, *myNetworkSecurityGroup*, och *myVnet*.

Skapa nätverkssäkerhetsgrupp med [az nätverket nsg skapa](/cli/azure/network/nsg#create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* i den *eastus* plats:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Lägga till en regel med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#create) att tillåta HTTP-trafik till en webbserver (eller justera för egna scenario, till exempel SSH åtkomst eller database connectivity). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* som tillåter TCP-trafik på port 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

Koppla Nätverkssäkerhetsgruppen till den Virtuella datorns nätverksgränssnitt (NIC) med [az nätverket nic uppdatering](/cli/azure/network/nic#update). I följande exempel associerar en befintlig NIC som heter *myNic* med Nätverkssäkerhetsgruppen med namnet *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternativt kan du associera säkerhetsgrupp för nätverk med ett undernät för virtuellt nätverk med [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#update) i stället för bara till nätverksgränssnittet på en enda virtuell dator. I följande exempel associerar ett befintligt undernät med namnet *mySubnet* i den *myVnet* virtuellt nätverk med säkerhetsgruppen nätverk med namnet *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mer information om Nätverkssäkerhetsgrupper
Snabb kommandon här kan du komma igång med trafik som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och granularitet för att styra åtkomst till resurser. Du kan läsa mer om [skapar en säkerhetsgrupp för nätverk och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

Du bör placera dina virtuella datorer bakom en belastningsutjämnare i Azure för hög tillgänglighet webbprogram. Belastningsutjämnaren distribuerar trafik till virtuella datorer med en Nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [hur du läser in balansera Linux-datorer i Azure för att skapa ett program med hög tillgänglighet](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapas en enkel regel för att tillåta HTTP-trafik. Du kan hitta information om hur du skapar mer detaljerad miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../../virtual-network/virtual-networks-nsg.md)
