---
title: Skapa en fullständig Linux-miljö med den klassiska Azure-CLI | Microsoft Docs
description: Skapa storage, en Linux VM, ett virtuellt nätverk och undernät, en belastningsutjämnare, ett nätverkskort, en offentlig IP-adress och en nätverkssäkerhetsgrupp, allt från grunden med hjälp av den klassiska Azure-CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 04c1d69fc46b9a918038e93c4fc56681f225d365
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006221"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Skapa en fullständig Linux-miljö med den klassiska Azure-CLI
I den här artikeln skapar vi ett enkelt nätverk med en belastningsutjämnare och ett par med virtuella datorer som är användbara för utveckling och enkel databehandling. Vi går igenom processen kommandot av kommandot förrän du har två arbetar, säker virtuella Linux-datorer som du kan ansluta från var som helst på Internet. Du kan sedan gå vidare till mer komplexa nätverk och miljöer.

Under tiden får du lära dig om beroende hierarkin att Resource Manager-distributionsmodellen ger dig och starta den om hur mycket ger. När du ser hur systemet har skapats, kan du återskapa det mycket snabbare genom att använda [Azure Resource Manager-mallar](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När du lär dig hur delarna av din miljö fungerar ihop, blir hur du skapar mallar för att automatisera dem också enklare.

Miljön innehåller:

* Två virtuella datorer i en tillgänglighetsuppsättning.
* En belastningsutjämnare med en regel för belastningsutjämning på port 80.
* Nätverks-regler för nätverkssäkerhetsgrupper (NSG) till att skydda den virtuella datorn från oönskad trafik.

Om du vill skapa den här anpassade miljön, du behöver senast [klassiska Azure-CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i Resource Manager-läge (`azure config mode arm`). Du behöver också en JSON-parsningsverktyg. Det här exemplet används [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure klassiskt CLI](#quick-commands) – vår CLI för de klassiska distributionsmodellen och resource Manager-distributionsmodellerna (den här artikeln)
- [Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen i resource management


## <a name="quick-commands"></a>Snabbkommandon
Om du behöver att snabbt utföra uppgiften, de följande avsnittet beskriver grundläggande kommandon för att ladda upp en virtuell dator till Azure. Mer detaljerad information och kontext för varje steg finns i resten av dokumentet, med början [här](#detailed-walkthrough).

Se till att du har [den klassiska Azure-CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) loggat in och använda Resource Manager-läge:

```azurecli
azure config mode arm
```

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar `myResourceGroup`, `mystorageaccount`, och `myVM`.

Skapa resursgruppen. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Kontrollera resursgruppen med hjälp av JSON-parser:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Skapa lagringskontot. I följande exempel skapas ett lagringskonto med namnet `mystorageaccount`. (Namnet på lagringskontot måste vara unikt, så ange ditt eget unika namn)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Kontrollera storage-konto med hjälp av JSON-parser:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Skapa det virtuella nätverket. I följande exempel skapas ett virtuellt nätverk med namnet `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Skapa ett undernät. I följande exempel skapas ett undernät med namnet `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Kontrollera det virtuella nätverket och undernätet med hjälp av JSON-parser:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Skapa en offentlig IP-adress. I följande exempel skapas en offentlig IP-adress med namnet `myPublicIP` med DNS-namnet på `mypublicdns`. (DNS-namnet måste vara unikt, så ange ditt eget unika namn)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Skapa belastningsutjämnaren. I följande exempel skapas en belastningsutjämnare med namnet `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Skapa en IP-adresspool på klientsidan för belastningsutjämnaren och associerar den offentliga IP-Adressen. I följande exempel skapas en IP-adresspool på klientsidan med namnet `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Skapa backend-IP-adresspool för belastningsutjämnaren. I följande exempel skapas en backend-IP-adresspoolen med namnet `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Skapa SSH inkommande nätverk regler för nätverksadressöversättning (NAT) för belastningsutjämnaren. I följande exempel skapas två belastningsutjämningsregler `myLoadBalancerRuleSSH1` och `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Skapa ingående NAT-regler för belastningsutjämnaren. I följande exempel skapas en regel för belastningsutjämnaren med namnet `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Skapa hälsoavsökningen för belastningsutjämnaren. I följande exempel skapas en TCP-avsökning med namnet `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Kontrollera belastningsutjämnaren, IP-pooler och NAT-regler med hjälp av JSON-parser:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Skapa första network interface card (NIC). Ersätt den `#####-###-###` avsnitt med din egen Azure prenumerations-ID. Din prenumeration ID som anges i utdata från **jq** när du undersöker de resurser som du skapar. Du kan också visa ditt prenumerations-ID med `azure account list`.

I följande exempel skapas ett nätverkskort med namnet `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Skapa det andra nätverkskortet. I följande exempel skapas ett nätverkskort med namnet `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Kontrollera de två nätverkskorten med hjälp av JSON-parser:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Skapa nätverkssäkerhetsgruppen. I följande exempel skapas en nätverkssäkerhetsgrupp med namnet `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Lägg till två inkommande regler för nätverkssäkerhetsgruppen. I följande exempel skapas två regler `myNetworkSecurityGroupRuleSSH` och `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifiera nätverkssäkerhetsgrupp och regler för inkommande trafik med hjälp av JSON-parser:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Binda nätverkssäkerhetsgruppen till två nätverkskort:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Skapa tillgänglighetsuppsättningen. I följande exempel skapas en tillgänglighetsuppsättning med namnet `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Skapa den första Linux-VM. I följande exempel skapas en virtuell dator med namnet `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Skapa andra Linux-VM. I följande exempel skapas en virtuell dator med namnet `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Använda JSON-parsern för att kontrollera att allt som har skapats:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportera din nya miljö till en mall för att snabbt återskapa nya instanser:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång
Detaljerade anvisningar som följer beskrivs vad varje kommando gör när du skapar i din miljö. De här koncepten är till hjälp när du skapar dina egna anpassade miljöer för utveckling eller produktion.

Se till att du har [den klassiska Azure-CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) loggat in och använda Resource Manager-läge:

```azurecli
azure config mode arm
```

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar `myResourceGroup`, `mystorageaccount`, och `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Skapa resursgrupper och välj distributionen platser
Azure-resursgrupper är logiska distribution entiteter som innehåller konfigurationsinformation och metadata för att aktivera logiska hantering av resursdistributioner. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Utdata:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Du behöver storage-konton för dina VM-diskar och eventuella ytterligare hårddiskar som du vill lägga till. Du kan skapa lagringskonton nästan omedelbart när du skapar resursgrupper.

Här använder vi den `azure storage account create` kommandot Skicka platsen för kontot, resursgruppens namn som styr det och vilken typ av support för lagring som du vill. I följande exempel skapas ett lagringskonto med namnet `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Utdata:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Att undersöka resursgrupp med hjälp av den `azure group show` kommandot, använder vi den [jq](https://stedolan.github.io/jq/) verktyget tillsammans med den `--json` Azure CLI-alternativet. (Du kan använda **jsawk** eller något bibliotek för språk som du föredrar att parsa JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Utdata:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Om du vill undersöka storage-konto med hjälp av CLI måste du först ange kontonamn och nycklar. Ersätt namnet på storage-konto i följande exempel med ett namn som du väljer:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Sedan kan du visa information om din enkelt:

```azurecli
azure storage container list
```

Utdata:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät
Därefter ska du skapa ett virtuellt nätverk som körs i Azure och ett undernät som du kan skapa dina virtuella datorer. I följande exempel skapas ett virtuellt nätverk med namnet `myVnet` med den `192.168.0.0/16` adressprefix:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Utdata:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Vi använder du alternativet--json av `azure group show` och `jq` att se hur vi bygger våra resurser. Nu har vi en `storageAccounts` resurs och en `virtualNetworks` resurs.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Utdata:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Nu ska vi skapa ett undernät i det `myVnet` virtuellt nätverk där de virtuella datorerna distribueras. Vi använder den `azure network vnet subnet create` kommandot tillsammans med de resurser som vi redan har skapat: den `myResourceGroup` resursgrupp och `myVnet` virtuellt nätverk. I följande exempel lägger vi till undernätet med namnet `mySubnet` med undernätet adressprefix `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Utdata:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Eftersom undernätet är logiskt i virtuella nätverk kan titta vi för information om undernät med ett något annorlunda kommando. Kommandot använder vi är `azure network vnet show`, men vi fortsätter att granska JSON-utdata med hjälp av `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Utdata:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
Nu ska vi skapa offentliga IP-adress (PIP) som vi tilldelade till belastningsutjämnaren. Du kan ansluta till dina virtuella datorer från Internet med hjälp av den `azure network public-ip create` kommando. Eftersom den standardadressen är dynamisk kan vi skapa en namngiven DNS-post i den **cloudapp.azure.com** domänen med hjälp av den `--domain-name-label` alternativet. I följande exempel skapas en offentlig IP-adress med namnet `myPublicIP` med DNS-namnet på `mypublicdns`. Eftersom DNS-namnet måste vara unikt, kan du ange ditt eget unika DNS-namn:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Utdata:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Offentliga IP-adress är också en resurs på toppnivå, så att du kan se den med `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Utdata:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Du kan undersöka mer information om resursen, inklusive underdomänen, fullständigt kvalificerade domännamnet (FQDN) med hjälp av den fullständiga `azure network public-ip show` kommando. Offentlig IP-adressresurs har tilldelats logiskt, men en specifik adress har inte tilldelats ännu. Om du vill skaffa en IP-adress kommer du att behöva en belastningsutjämnare som vi inte har skapat.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Utdata:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Skapa en belastningsutjämnare och IP-pooler
När du skapar en belastningsutjämnare kan du distribuera trafik mellan flera virtuella datorer. Det ger också ditt program redundans genom att köra flera virtuella datorer som svarar på begäranden för användare i händelse av underhåll eller tunga belastningar. I följande exempel skapas en belastningsutjämnare med namnet `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Utdata:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Vår belastningsutjämnare är ganska tom, så Låt oss skapa vissa IP-adresspooler. Vi vill skapa två IP-adresspooler för vår belastningsutjämnare, en för klientdelen och en för backend-servern. IP-adresspoolen på klientsidan är synligt offentligt. Det är också den plats som vi tilldelar PIP som vi skapade tidigare. Sedan använder vi backend poolen som en plats för våra virtuella datorer för att ansluta till. På så sätt kan trafiken kan flöda via belastningsutjämnaren till de virtuella datorerna.

Först måste vi skapa vårt IP-adresspool på klientsidan. I följande exempel skapas en adresspool på klientsidan med namnet `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Utdata:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Observera hur vi använde den `--public-ip-name` växel för att skicka in den `myPublicIP` som vi skapade tidigare. Tilldela offentliga IP-adress till belastningsutjämnaren kan du nå dina virtuella datorer via Internet.

Nu ska vi skapa vårt andra IP-pool nu för vår backend-trafik. I följande exempel skapas en serverdelspool med namnet `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Utdata:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Vi kan se hur vår belastningsutjämnaren fungerar genom att söka med `azure network lb show` och undersöka JSON-utdata:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Utdata:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Skapa NAT-regler för belastningsutjämnare
Om du vill hämta trafik som passerar genom våra belastningsutjämnare, som vi behöver skapa nätverksadress regler för nätverksadressöversättning (NAT) som anger inkommande eller utgående åtgärder. Du kan ange protokollet som ska användas och sedan mappa externa portar till interna portar som du vill. Vår där vi skapa några regler som tillåter SSH via vår belastningsutjämnare till våra virtuella datorer. Vi konfigurerar TCP-portarna 4222 och 4223 direkt till TCP-port 22 på våra virtuella datorer (som vi skapar senare). I följande exempel skapas en regel med namnet `myLoadBalancerRuleSSH1` kan mappa TCP-port 4222 till port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Utdata:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Upprepa proceduren för dina andra NAT-regel för SSH. I följande exempel skapas en regel med namnet `myLoadBalancerRuleSSH2` kan mappa TCP-port 4223 till port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nu ska vi också gå vidare och skapa en NAT-regel för TCP-port 80 för webbtrafik, anslutning regeln upp till våra IP-adresspooler. Om vi kopplar in regeln för att en IP-adresspool, i stället för att koppla upp en regel till våra virtuella datorer separat, kan vi lägga till eller ta bort virtuella datorer från IP-adresspool. Belastningsutjämnaren justeras automatiskt trafikflödet. I följande exempel skapas en regel med namnet `myLoadBalancerRuleWeb` kan mappa TCP-port 80 till port 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Utdata:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>skapa en hälsoavsökning för lastbalanseraren
En hälsoavsökning med jämna mellanrum kontroller på de virtuella datorerna som finns bakom våra load balancer för att se till att de fungerar och svara på begäranden som har definierats. Om inte, de har tagits bort från att se till att användare inte dirigeras till dem-åtgärd. Du kan definiera egna kontroller för hälsoavsökningen, tillsammans med intervall och timeout-värden. Läs mer om hälsoavsökningar [Load Balancer kontrollerar](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). I följande exempel skapas en TCP hälsotillstånd avlästes namngivna `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Utdata:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Här kan angav vi ett intervall på 15 sekunder för vår hälsokontroller. Vi kan missa högst fyra avsökningar (en minut) innan belastningsutjämnaren tar hänsyn till att värden inte längre fungerar.

## <a name="verify-the-load-balancer"></a>Kontrollera belastningsutjämnaren
Belastningsutjämningskonfigurationen är nu klar. Här är de steg som du tog:

1. Du har skapat en belastningsutjämnare.
2. Du har skapat en IP-adresspool på klientsidan och tilldelats en offentlig IP-adress.
3. Du har skapat en backend-IP-adresspool som virtuella datorer kan ansluta till.
4. Du har skapat NAT-regler som tillåter SSH till de virtuella datorerna för hantering, tillsammans med en regel som tillåter TCP-port 80 för vår webbapp.
5. Du har lagt till en hälsoavsökning för att regelbundet kontrollera de virtuella datorerna. Den här hälsoavsökning säkerställer att användare inte försöker komma åt en virtuell dator som inte längre fungerar eller hämta innehåll.

Nu ska vi se hur belastningsutjämnaren ser ut nu:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Utdata:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Skapa ett nätverkskort som ska användas med Linux VM
Nätverkskort är tillgängliga programmässigt eftersom du kan använda regler för deras användning. Du kan också ha fler än en. I följande `azure network nic create` kommandot du ansluta nätverkskortet till den belastning backend-IP-adresspoolen och associera det med NAT-regel för att tillåta SSH-trafik.

Ersätt den `#####-###-###` avsnitt med din egen Azure prenumerations-ID. Din prenumeration ID som anges i utdata från `jq` när du undersöker de resurser som du skapar. Du kan också visa ditt prenumerations-ID med `azure account list`.

I följande exempel skapas ett nätverkskort med namnet `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Utdata:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Du kan se information genom att undersöka resursen direkt. Du undersöker resursen med hjälp av den `azure network nic show` kommando:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Utdata:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Nu skapar vi det andra nätverkskortet, anslutning i vår backend-IP-poolen igen. Den här gången andra NAT-regeln tillåter SSH-trafik. I följande exempel skapas ett nätverkskort med namnet `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Skapa en nätverkssäkerhetsgrupp och regler
Nu skapa vi en nätverkssäkerhetsgrupp och de inkommande regler som styr åtkomsten till nätverkskortet. En nätverkssäkerhetsgrupp kan tillämpas på ett nätverkskort eller undernät. Du kan definiera regler för att styra flödet av trafik in och ut ur dina virtuella datorer. I följande exempel skapas en nätverkssäkerhetsgrupp med namnet `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vi lägger till den inkommande regeln för NSG tillåter inkommande anslutningar på port 22 (för att stödja SSH). I följande exempel skapas en regel med namnet `myNetworkSecurityGroupRuleSSH` att tillåta TCP på port 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Nu ska vi lägga till den inkommande regeln för NSG tillåter inkommande anslutningar på port 80 (för Internet-trafik). I följande exempel skapas en regel med namnet `myNetworkSecurityGroupRuleHTTP` att tillåta TCP på port 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Den inkommande regeln är ett filter för inkommande anslutningar. I det här exemplet binda vi NSG till det virtuella nätverkskortet virtuella datorer, vilket innebär att varje begäran till port 22 skickas till nätverkskortet på våra virtuella datorn. Denna regel för inkommande trafik som handlar om en nätverksanslutning och inte om en slutpunkt, vilket är vad den skulle vara om i klassiska distributioner. Öppna en port genom att lämna den `--source-port-range` inställd på '\*”(standardvärdet) att acceptera inkommande begäranden från **alla** begär port. Det finns vanligtvis dynamiska portar.
>
>

## <a name="bind-to-the-nic"></a>Binda till nätverkskortet
Binda NSG: N till nätverkskorten. Vi behöver ansluta vår nätverkskort med vår nätverkssäkerhetsgrupp. Kör bägge kommandona för att koppla samman både våra nätverkskort:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Tillgänglighetsuppsättningar hjälp spridning dina virtuella datorer över feldomäner och uppgraderingsdomäner. Nu ska vi skapa en tillgänglighetsuppsättning för dina virtuella datorer. I följande exempel skapas en tillgänglighetsuppsättning med namnet `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Feldomäner definierar en gruppering av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard indelade de virtuella datorer som har konfigurerats i din tillgänglighetsuppsättning upp till tre feldomäner. Tanken är att inte påverkar maskinvaruproblem i någon av dessa feldomäner varje virtuell dator som kör din app. Azure distribuerar automatiskt virtuella datorer på feldomänerna när de placeras i en tillgänglighetsuppsättning.

Uppgraderingsdomäner ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas på samma gång. Den ordning i vilken uppgraderingsdomäner startas om kanske inte är sekventiella under planerat underhåll, men endast en uppgradering startas i taget. Igen, Azure automatiskt distribuerar dina virtuella datorer mellan uppgraderingsdomäner när de placeras i en tillgänglighet på platsen.

Läs mer om [hantera tillgängligheten för virtuella datorer](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Skapa de virtuella Linux-datorerna
Du har skapat lagrings- och nätverksresurser för Internet-åtkomlig virtuella datorer. Nu ska vi skapa de virtuella datorer och skydda dem med en SSH-nyckel som inte har ett lösenord. I det här fallet ska vi skapa en Ubuntu virtuell dator baserat på senaste LTS. Vi hitta avbildningen informationen med hjälp av `azure vm image list`, enligt beskrivningen i [att söka efter Azure VM-avbildningarna](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vi har valt en avbildning med hjälp av kommandot `azure vm image list westeurope canonical | grep LTS`. I det här fallet använder vi `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Senaste fältet vi skicka `latest` så att i framtiden vi alltid hämta den senaste versionen. (Den sträng som vi använder är `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Den här nästa steg är välkänd för alla som redan har skapat en ssh rsa-offentliga och privata nyckel koppla på Linux eller Mac med hjälp av **ssh-keygen - t rsa -b 2048**. Om du inte har några certifikat nyckelpar din `~/.ssh` directory, kan du skapa dem:

* Automatiskt med hjälp av den `azure vm create --generate-ssh-keys` alternativet.
* Manuellt via [instruktionerna för att skapa dem själv](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan också använda den `--admin-password` metod för att autentisera SSH-anslutningar när den virtuella datorn har skapats. Den här metoden är vanligtvis mindre säker.

Vi skapar den virtuella datorn genom att föra alla våra resurser och information tillsammans med den `azure vm create` kommando:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Utdata:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Du kan ansluta till den virtuella datorn direkt med hjälp av dina standard SSH-nycklar. Se till att du anger rätt port eftersom vi låter via belastningsutjämnaren. (För vår första virtuella dator, vi konfigurera NAT-regeln att vidarebefordra port 4222 till våra VM.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Utdata:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Gå vidare och skapa en andra virtuell dator på samma sätt:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Och nu kan du använda den `azure vm show myResourceGroup myVM1` kommando för att granska vad du har skapat. Nu kan kör du din virtuella Ubuntu-datorer bakom en belastningsutjämnare i Azure som du kan logga in på endast med SSH-nyckelpar (eftersom lösenord är inaktiverade). Du kan installera nginx eller httpd, distribuera en webbapp och se trafiken flow via belastningsutjämnaren till båda de virtuella datorerna.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Utdata:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportera miljön som en mall
Nu när du har skapat i den här miljön, vad händer om du vill skapa en ytterligare utvecklingsmiljö med samma parametrar eller en produktionsmiljö som matchar det? Resource Manager använder JSON-mallar som definierar parametrarna som för din miljö. Du bygga ut hela miljöer genom att referera till den här JSON-mallen. Du kan [bygga JSON-mallar manuellt](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller exportera en befintlig miljö för att skapa JSON-mallen för dig:

```azurecli
azure group export --name myResourceGroup
```

Det här kommandot skapar den `myResourceGroup.json` fil i din aktuella arbetskatalog. När du skapar en miljö från den här mallen kan uppmanas du för alla resursnamn, inklusive namnen för belastningsutjämnaren, nätverksgränssnitt eller virtuella datorer. Du kan fylla i de här namnen i mallfilen genom att lägga till den `-p` eller `--includeParameterDefaultValue` parametern till den `azure group export` kommando som visades tidigare. Redigera din JSON-mall för att ange resursnamn, eller [skapar du en parameters.json fil](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som anger resursnamnen.

Skapa en miljö från mallen:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Du kanske vill läsa [mer om hur du distribuerar från mallar](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Läs mer om hur du inkrementellt uppdatera miljöer, använder du filen parametrar och komma åt mallar från en enda lagringsplats.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att börja arbeta med flera nätverkskomponenter och virtuella datorer. Du kan använda det här exemplet miljö för att bygga ut ditt program med hjälp av kärnkomponenter som introduceras här.
