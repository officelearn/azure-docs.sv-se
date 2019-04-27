---
title: Öppna portar till en Linux VM med Azure CLI | Microsoft Docs
description: Lär dig hur du öppnar en port / skapa en slutpunkt för din Linux-VM med hjälp av Azure resource manager-distributionsmodellen och Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a12952c73863d10c4fffd013ab594a83ab1b6433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771540"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Öppna portar och slutpunkter till en Linux VM med Azure CLI

Du öppnar en port eller skapa en slutpunkt för att en virtuell dator (VM) i Azure genom att skapa ett filter för nätverk på ett undernät eller Virtuella nätverksgränssnitt. Du kan placera dessa filter som styr både inkommande och utgående trafik på en Nätverkssäkerhetsgrupp som är kopplade till den resurs som tar emot trafiken. Nu ska vi använda ett vanligt exempel på Internet-trafik på port 80. Den här artikeln visar hur du öppnar en port till en virtuell dator med Azure CLI. 


Skapa en Nätverkssäkerhetsgrupp och regler som du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar *myResourceGroup*, *myNetworkSecurityGroup*, och *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Snabbt öppna en port för en virtuell dator
Om du behöver snabbt öppnar en port för en virtuell dator i ett scenario för utveckling och testning kan du använda den [az vm open-port](/cli/azure/vm) kommando. Det här kommandot skapar en Nätverkssäkerhetsgrupp, lägger till en regel och tillämpar den på en virtuell dator eller undernät. I följande exempel öppnas port *80* på den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

För mer kontroll över regler, till exempel definiera källan IP-adressintervall, fortsätter du med de ytterligare stegen i den här artikeln.


## <a name="create-a-network-security-group-and-rules"></a>Skapa en Nätverkssäkerhetsgrupp och regler
Skapa nätverkssäkerhetsgrupp med [az network nsg skapa](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* i den *eastus* plats:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Lägga till en regel med [az network nsg-regel skapar](/cli/azure/network/nsg/rule) att tillåta HTTP-trafik till en webbserver (eller justera för ditt eget scenario, till exempel anslutningar för SSH-åtkomst eller databas). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* att tillåta TCP-trafik på port 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Tillämpa Nätverkssäkerhetsgrupp kopplad till virtuell dator
Associera Nätverkssäkerhetsgruppen med den Virtuella datorns nätverksgränssnitt (NIC) med [az network nic update](/cli/azure/network/nic). I följande exempel kopplar ett befintligt nätverkskort med namnet *myNic* med Nätverkssäkerhetsgruppen med namnet *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Du kan också associera Nätverkssäkerhetsgruppen med ett virtuellt nätverksundernät med [az network vnet-undernät update](/cli/azure/network/vnet/subnet) istället för bara till nätverksgränssnittet på en enskild virtuell dator. I följande exempel kopplar ett befintligt undernät med namnet *mySubnet* i den *myVnet* virtuellt nätverk med Nätverkssäkerhetsgruppen med namnet *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mer information om Nätverkssäkerhetsgrupper
Snabbkommandon här kan du komma igång med trafiken som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och kornighet för att styra åtkomst till resurser. Du kan läsa mer om [skapar en Nätverkssäkerhetsgrupp och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

För webbprogram med hög tillgänglighet, bör du placera dina virtuella datorer bakom en belastningsutjämnare för Azure. Belastningsutjämnaren distribuerar trafik till virtuella datorer med en Nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [läsa in belastningsutjämna Linux-datorer i Azure för att skapa ett program med hög tillgänglighet](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapade du en enkel regel för att tillåta HTTP-trafik. Du hittar information om hur du skapar mer detaljerad miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../../virtual-network/security-overview.md)
