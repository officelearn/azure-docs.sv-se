---
title: Öppna portar till en virtuell dator med Azure CLI
description: Lär dig hur du öppnar en port /skapar en slutpunkt till din virtuella dator med Hjälp av Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: c29fb075fc2d8b512070d7a6cf3fef949def5894
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066634"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Öppna portar och slutpunkter till en virtuell dator med Azure CLI

Du öppnar en port eller skapar en slutpunkt till en virtuell dator (VM) i Azure genom att skapa ett nätverksfilter i ett undernät eller vm-nätverksgränssnitt. Du placerar dessa filter, som styr både inkommande och utgående trafik, i en nätverkssäkerhetsgrupp som är kopplad till den resurs som tar emot trafiken. Låt oss använda ett vanligt exempel på webbtrafik på port 80. Den här artikeln visar hur du öppnar en port till en virtuell dator med Azure CLI. 


För att skapa en nätverkssäkerhetsgrupp och regler behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderar *myResourceGroup,* *myNetworkSecurityGroup*och *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Öppna snabbt en port för en virtuell dator
Om du snabbt behöver öppna en port för en virtuell dator i ett scenario för utveckling/test kan du använda kommandot [az vm open-port.](/cli/azure/vm) Det här kommandot skapar en nätverkssäkerhetsgrupp, lägger till en regel och tillämpar den på en virtuell dator eller ett undernät. I följande exempel öppnas port *80* på den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Om du vill ha mer kontroll över reglerna, till exempel definiera ett käll-IP-adressintervall, fortsätter du med ytterligare steg i den här artikeln.


## <a name="create-a-network-security-group-and-rules"></a>Skapa en nätverkssäkerhetsgrupp och regler
Skapa nätverkssäkerhetsgruppen med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* på *eastus-platsen:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Lägg till en regel med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule) för att tillåta HTTP-trafik till din webbserver (eller justera för ditt eget scenario, till exempel SSH-åtkomst eller databasanslutning). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* för att tillåta TCP-trafik på port 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Använda nätverkssäkerhetsgrupp på virtuell dator
Associera nätverkssäkerhetsgruppen med den virtuella datorns nätverksgränssnitt (NIC) med [az-nätverksuppdatering](/cli/azure/network/nic). I följande exempel associeras ett befintligt nätverkskort med namnet *myNic* med nätverkssäkerhetsgruppen *myNetworkSecurityGroup:*

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Du kan också associera nätverkssäkerhetsgruppen med ett virtuellt nätverksundernät med [az network vnet-undernätsuppdatering](/cli/azure/network/vnet/subnet) i stället för bara till nätverksgränssnittet på en enda virtuell dator. I följande exempel associeras ett befintligt undernät med namnet *mySubnet* i *myVnet-nätverket* med nätverkssäkerhetsgruppen *myNetworkSecurityGroup:*

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mer information om nätverkssäkerhetsgrupper
De snabba kommandona här gör att du kan komma igång med trafik som flödar till din virtuella dator. Nätverkssäkerhetsgrupper ger många bra funktioner och granularitet för att kontrollera åtkomsten till dina resurser. Du kan läsa mer om [hur du skapar en nätverkssäkerhetsgrupp och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

För webbprogram med hög tillgänglig tillgång bör du placera dina virtuella datorer bakom en Azure Load Balancer. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [Så här laddar du fördelning av virtuella Linux-datorer i Azure för att skapa ett program med hög tillgänglig tillgång](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapade du en enkel regel för att tillåta HTTP-trafik. Du hittar information om hur du skapar mer detaljerade miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../../virtual-network/security-overview.md)
