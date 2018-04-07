---
title: Öppna portar till en Linux-VM med Azure CLI 1.0 | Microsoft Docs
description: Lär dig hur du öppnar en port / skapa en slutpunkt för ditt Linux-VM med hjälp av Azure resource manager-distributionsmodellen och Azure CLI 1.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: e94b2ee3a14e9046703b66c00e3ba1a305dd3a7d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Öppna portar och slutpunkter till en Linux-VM i Azure med hjälp av Azure CLI 1.0
Du öppnar en port eller skapa en slutpunkt för en virtuell dator (VM) i Azure genom att skapa ett filter för nätverk på ett undernät eller Virtuella datorns nätverksgränssnitt. Du kan placera dessa filter som styr både inkommande och utgående trafik på en Nätverkssäkerhetsgrupp kopplad till den resurs som tar emot trafiken. Nu ska vi använda ett vanligt exempel på Internet-trafik på port 80. Den här artikeln visar hur du öppnar en port till en virtuell dator med hjälp av Azure CLI 1.0.


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-commands) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](nsg-quickstart.md) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="quick-commands"></a>Snabbkommandon
Så här skapar du en säkerhetsgrupp för nätverk och regler som du behöver [Azure CLI 1.0](../../cli-install-nodejs.md) installerad och med hjälp av Resource Manager-läge:

```azurecli
azure config mode arm
```

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår *myResourceGroup*, *myNetworkSecurityGroup*, och *myVnet*.

Skapa din Nätverkssäkerhetsgruppen att ange egna namn och plats på lämpligt sätt. I följande exempel skapas en Nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* i den *eastus* plats:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Lägg till en regel för att tillåta HTTP-trafik till en webbserver (eller justera för egna scenario, till exempel SSH åtkomst eller database connectivity). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* som tillåter TCP-trafik på port 80:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

Koppla Nätverkssäkerhetsgruppen till den Virtuella datorns nätverksgränssnitt (NIC). I följande exempel associerar en befintlig NIC som heter *myNic* med Nätverkssäkerhetsgruppen med namnet *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

Du kan också associera säkerhetsgrupp för nätverk med ett undernät för virtuellt nätverk i stället för bara till nätverksgränssnittet på en enda virtuell dator. I följande exempel associerar ett befintligt undernät med namnet *mySubnet* i den *myVnet* virtuellt nätverk med säkerhetsgruppen nätverk med namnet *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Mer information om Nätverkssäkerhetsgrupper
Snabb kommandon här kan du komma igång med trafik som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och granularitet för att styra åtkomst till resurser. Du kan läsa mer om [skapar en säkerhetsgrupp för nätverk och ACL-regler här](../../virtual-network/tutorial-filter-network-traffic-cli.md).

Du kan definiera Nätverkssäkerhetsgrupper och ACL-regler som en del av Azure Resource Manager-mallar. Läs mer om [skapa Nätverkssäkerhetsgrupper med mallar](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Om du behöver använda port-vidarebefordran för att mappa en unik extern port till en intern port på den virtuella datorn Använd belastningsutjämning och NAT (Network Address Translation)-regler. Du kanske vill exponera TCP-port 8080 externt och har trafik dirigeras till TCP-port 80 på en virtuell dator. Du kan lära dig om [skapar en Internetriktade belastningsutjämnare](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapas en enkel regel för att tillåta HTTP-trafik. Du kan hitta information om hur du skapar mer detaljerad miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../../virtual-network/virtual-networks-nsg.md)
* [Översikt av Azure Resource Manager för belastningsutjämnare](../../load-balancer/load-balancer-arm.md)

