---
title: Skapa en fullständig Linux-miljö med Azures klassiska CLI | Microsoft Docs
description: Skapa lagring, en virtuell Linux-dator, ett virtuellt nätverk och undernät, en belastningsutjämnare, ett nätverkskort, en offentlig IP-adress och en nätverks säkerhets grupp, allt från grunden med hjälp av den klassiska Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: aaf91aa81be5fc4c5944dde804798a61ceffc5a6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083721"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Skapa en fullständig Linux-miljö med Azures klassiska CLI
I den här artikeln skapar vi ett enkelt nätverk med en belastningsutjämnare och ett par med virtuella datorer som är användbara för utveckling och enkel data behandling. Vi går igenom process kommandot efter kommando tills du har två fungerande, säkra virtuella Linux-datorer som du kan ansluta från var som helst på Internet. Sedan kan du gå vidare till mer komplexa nätverk och miljöer.

Under tiden får du lära dig om den beroende hierarkin som distributions modellen för Resource Manager ger dig, och om hur mycket ström den erbjuder. När du ser hur systemet har skapats kan du återskapa det snabbare med hjälp av [Azure Resource Manager mallar](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När du har lärt dig hur delarna i din miljö passar ihop blir det enklare att skapa mallar för att automatisera dem.

Miljön innehåller:

* Två virtuella datorer i en tillgänglighets uppsättning.
* En belastningsutjämnare med en belastnings Utjämnings regel på port 80.
* Regler för nätverks säkerhets gruppen (NSG) för att skydda den virtuella datorn från oönskad trafik.

Om du vill skapa den här anpassade miljön behöver du den senaste [Azure Classic CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i Resource Manager`azure config mode arm`-läge (). Du behöver också ett JSON-parsningsfel. I det här exemplet används [JQ](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- Den [klassiska Azure CLI](#quick-commands) – vår CLI för distributions modellerna klassisk och Resource Management (den här artikeln)
- [Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributions modellen resurs hantering


## <a name="quick-commands"></a>Snabbkommandon
Om du snabbt behöver utföra uppgiften kan du använda följande avsnitt för att ladda upp en virtuell dator till Azure. Mer detaljerad information och kontext för varje steg finns i resten av dokumentet, med början [här](#detailed-walkthrough).

Kontrol lera att Azures [klassiska CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) är inloggad och Använd Resource Manager-läge:

```azurecli
azure config mode arm
```

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn `myResourceGroup`är `mystorageaccount`, och `myVM`.

Skapa resursgruppen. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Verifiera resurs gruppen med hjälp av JSON-parsern:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Skapa lagrings kontot. I följande exempel skapas ett lagrings konto `mystorageaccount`med namnet. (Lagrings kontots namn måste vara unikt, så ange ditt eget unika namn.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verifiera lagrings kontot med hjälp av JSON-parser:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Skapa det virtuella nätverket. I följande exempel skapas ett virtuellt nätverk med `myVnet`namnet:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Skapa ett undernät. I följande exempel skapas ett undernät med `mySubnet`namnet:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verifiera det virtuella nätverket och under nätet med hjälp av JSON-parsern:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Skapa en offentlig IP-adress. I följande exempel skapas en offentlig IP- `myPublicIP` adress med namnet med DNS `mypublicdns`-namnet. (DNS-namnet måste vara unikt, så ange ditt eget unika namn.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Skapa belastningsutjämnaren. I följande exempel skapas en belastningsutjämnare med namnet `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Skapa en IP-adresspool på klient sidan för belastningsutjämnaren och associera den offentliga IP-adressen. I följande exempel skapas en frontend-IP-pool med `mySubnetPool`namnet:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Skapa server delens IP-pool för belastningsutjämnaren. I följande exempel skapas en backend-IP-pool med `myBackEndPool`namnet:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Skapa SSH-regler för inkommande Network Address Translation (NAT) för belastningsutjämnaren. I följande exempel skapas två regler `myLoadBalancerRuleSSH1` för belastnings utjämning och: `myLoadBalancerRuleSSH2`

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Skapa de webb ingående NAT-reglerna för belastningsutjämnaren. I följande exempel skapas en belastnings Utjämnings regel `myLoadBalancerRuleWeb`med namnet:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Skapa belastnings utjämningens hälso avsökning. I följande exempel skapas en TCP-avsökning med namnet `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verifiera belastningsutjämnare, IP-pooler och NAT-regler med hjälp av JSON-parsern:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Skapa det första nätverks gränssnitts kortet (NIC). `#####-###-###` Ersätt avsnitten med ditt eget ID för Azure-prenumeration. Ditt prenumerations-ID anges i resultatet av **JQ** när du granskar de resurser som du skapar. Du kan också visa ditt prenumerations- `azure account list`ID med.

I följande exempel skapas ett nätverkskort med `myNic1`namnet:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Skapa det andra NÄTVERKSKORTet. I följande exempel skapas ett nätverkskort med `myNic2`namnet:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verifiera de två nätverkskorten med hjälp av JSON-parser:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Skapa nätverks säkerhets gruppen. I följande exempel skapas en nätverks säkerhets grupp med `myNetworkSecurityGroup`namnet:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Lägg till två regler för inkommande trafik för nätverks säkerhets gruppen. I följande exempel skapas två regler, `myNetworkSecurityGroupRuleSSH` och `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifiera nätverks säkerhets gruppen och inkommande regler med hjälp av JSON-parsern:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Bind nätverks säkerhets gruppen till de två nätverkskorten:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Skapa tillgänglighets uppsättningen. I följande exempel skapas en tillgänglighets uppsättning `myAvailabilitySet`med namnet:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Skapa den första virtuella Linux-datorn. I följande exempel skapas en virtuell dator `myVM1`med namnet:

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

Skapa den andra virtuella Linux-datorn. I följande exempel skapas en virtuell dator `myVM2`med namnet:

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

Använd JSON-parsern för att kontrol lera att allt som har skapats:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportera din nya miljö till en mall för att snabbt återskapa nya instanser:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång
De detaljerade stegen nedan beskriver vad varje kommando gör när du bygger ut din miljö. Dessa begrepp är användbara när du skapar egna anpassade miljöer för utveckling eller produktion.

Kontrol lera att Azures [klassiska CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) är inloggad och Använd Resource Manager-läge:

```azurecli
azure config mode arm
```

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn `myResourceGroup`är `mystorageaccount`, och `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Skapa resurs grupper och välj distributions platser
Azures resurs grupper är logiska distributions enheter som innehåller konfigurations information och metadata för att aktivera den logiska hanteringen av resurs distributioner. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Resultat:

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
Du behöver lagrings konton för dina virtuella dator diskar och för eventuella ytterligare data diskar som du vill lägga till. Du skapar lagrings konton nästan omedelbart efter att du har skapat resurs grupper.

Här använder `azure storage account create` vi kommandot för att skicka kontots plats, resurs gruppen som styr det och vilken typ av lagrings stöd du vill ha. I följande exempel skapas ett lagrings konto `mystorageaccount`med namnet:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Resultat:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

För att undersöka vår resurs grupp med hjälp `azure group show` av-kommandot, ska vi använda [JQ](https://stedolan.github.io/jq/) - `--json` verktyget tillsammans med alternativet Azure CLI. (Du kan använda **jsawk** eller valfritt språk bibliotek som du föredrar för att parsa JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Resultat:

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

Om du vill undersöka lagrings kontot med hjälp av CLI måste du först ange konto namn och nycklar. Ersätt namnet på lagrings kontot i följande exempel med ett namn som du väljer:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Sedan kan du enkelt visa din lagrings information:

```azurecli
azure storage container list
```

Resultat:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät
Härnäst måste du skapa ett virtuellt nätverk som körs i Azure och ett undernät där du kan skapa dina virtuella datorer. I följande exempel skapas ett virtuellt nätverk med `myVnet` namnet `192.168.0.0/16` med adressprefixet:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Resultat:

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

Nu ska vi använda alternativet--JSON för `azure group show` och `jq` för att se hur vi bygger våra resurser. Nu har vi en `storageAccounts` resurs och en `virtualNetworks` resurs.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Resultat:

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

Nu ska vi skapa ett undernät i det `myVnet` virtuella nätverk där de virtuella datorerna distribueras. Vi använder `azure network vnet subnet create` kommandot, tillsammans med de resurser som vi redan har skapat `myResourceGroup` : resurs gruppen och det `myVnet` virtuella nätverket. I följande exempel lägger vi till under nätet med namnet `mySubnet` med under nätets `192.168.1.0/24`adressprefix:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Resultat:

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

Eftersom under nätet är logiskt inne i det virtuella nätverket letar vi efter under näts information med ett något annorlunda kommando. Kommandot vi använder är `azure network vnet show`, men vi fortsätter att undersöka JSON-utdata `jq`med.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Resultat:

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
Nu ska vi skapa den offentliga IP-adressen (PIP) som vi tilldelar belastningsutjämnaren. Det gör att du kan ansluta till dina virtuella datorer från Internet med hjälp `azure network public-ip create` av kommandot. Eftersom standard adressen är dynamisk skapar vi en namngiven DNS-post i **cloudapp.Azure.com** -domänen med hjälp `--domain-name-label` av alternativet. I följande exempel skapas en offentlig IP- `myPublicIP` adress med namnet med DNS `mypublicdns`-namnet. Eftersom DNS-namnet måste vara unikt anger du ditt eget unika DNS-namn:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Resultat:

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

Den offentliga IP-adressen är också en resurs på den översta nivån, så att du kan `azure group show`se den med.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Resultat:

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

Du kan undersöka mer resursinformation, inklusive det fullständigt kvalificerade domän namnet (FQDN) för under domänen genom att använda kommandot fullständig `azure network public-ip show` . Den offentliga IP-adressresursen har allokerats logiskt, men en speciell adress har ännu inte tilldelats. För att få en IP-adress måste du ha en belastningsutjämnare som vi ännu inte har skapat.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Resultat:

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
När du skapar en belastningsutjämnare kan du distribuera trafik över flera virtuella datorer. Den ger också redundans till ditt program genom att köra flera virtuella datorer som svarar på användar förfrågningar i händelse av underhåll eller tung belastning. I följande exempel skapas en belastningsutjämnare med namnet `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Resultat:

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

Vår belastningsutjämnare är ganska tom, så vi skapar vissa IP-pooler. Vi vill skapa två IP-pooler för vår belastningsutjämnare, en för klient delen och en för Server delen. IP-poolen på klient sidan är allmänt synlig. Det är också den plats där vi tilldelar den PIP som vi skapade tidigare. Sedan använder vi backend-poolen som en plats för våra virtuella datorer för att ansluta till. På så sätt kan trafiken flöda genom belastningsutjämnaren till de virtuella datorerna.

Först ska vi skapa vår IP-pool på klient sidan. I följande exempel skapas en klient dels grupp med namnet `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Resultat:

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

Observera hur vi använde `--public-ip-name` växeln för att skicka in den `myPublicIP` som vi skapade tidigare. Genom att tilldela den offentliga IP-adressen till belastningsutjämnaren kan du komma åt dina virtuella datorer via Internet.

Nu ska vi skapa vår andra IP-pool, den här gången för vår server dels trafik. I följande exempel skapas en backend-pool med namnet `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Resultat:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Vi kan se hur vår belastningsutjämnare gör genom att titta `azure network lb show` på och undersöka JSON-utdata:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Resultat:

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
För att få trafik som passerar genom vår belastningsutjämnare måste vi skapa Network Address Translation (NAT) regler som anger antingen inkommande eller utgående åtgärder. Du kan ange vilket protokoll som ska användas och sedan mappa externa portar till interna portar efter behov. I vår miljö skapar vi några regler som tillåter SSH via vår belastningsutjämnare till våra virtuella datorer. Vi konfigurerar TCP-portarna 4222 och 4223 för att dirigera till TCP-port 22 på våra virtuella datorer (som vi skapar senare). I följande exempel skapas en regel med `myLoadBalancerRuleSSH1` namnet för att mappa TCP-port 4222 till port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Resultat:

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

Upprepa proceduren för din andra NAT-regel för SSH. I följande exempel skapas en regel med `myLoadBalancerRuleSSH2` namnet för att mappa TCP-port 4223 till port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nu ska vi gå vidare och skapa en NAT-regel för TCP-port 80 för webb trafik, och ansluta regeln upp till våra IP-pooler. Om vi kopplar samman regeln till en IP-pool, i stället för att ansluta regeln till våra virtuella datorer, kan vi lägga till eller ta bort virtuella datorer från IP-poolen. Belastningsutjämnaren justerar automatiskt trafik flödet. I följande exempel skapas en regel med `myLoadBalancerRuleWeb` namnet för att mappa TCP-port 80 till port 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Resultat:

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
En hälso avsökning kontrollerar regelbundet på de virtuella datorer som finns bakom vår belastningsutjämnare för att se till att de är igång och svarar på begär Anden enligt definitionen. Om de inte är det tas de bort från åtgärden för att se till att användarna inte dirigeras till dem. Du kan definiera anpassade kontroller för hälso avsökningen, tillsammans med intervall och tids gräns värden. Mer information om hälso avsökningar finns i [Load Balancer](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)avsökningar. I följande exempel skapas en TCP-hälso avsökning `myHealthProbe`med namnet:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Resultat:

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

Här angav vi ett intervall på 15 sekunder för våra hälso kontroller. Vi kan sakna maximalt fyra avsökningar (en minut) innan belastningsutjämnaren anser att värden inte längre fungerar.

## <a name="verify-the-load-balancer"></a>Verifiera belastningsutjämnaren
Nu är konfigurationen av belastnings utjämning färdig. Här är de steg du vidtog:

1. Du har skapat en belastningsutjämnare.
2. Du har skapat en IP-adresspool på klient sidan och tilldelat en offentlig IP-adress till den.
3. Du har skapat en backend-IP-pool som virtuella datorer kan ansluta till.
4. Du har skapat NAT-regler som tillåter SSH till de virtuella datorerna för hantering, tillsammans med en regel som tillåter TCP-port 80 för webb programmet.
5. Du har lagt till en hälso avsökning för att regelbundet kontrol lera de virtuella datorerna. Den här hälso avsökningen säkerställer att användare inte försöker komma åt en virtuell dator som inte längre fungerar eller betjänar innehåll.

Nu ska vi se hur belastningsutjämnaren ser ut så här:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Resultat:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Skapa ett nätverkskort som ska användas med den virtuella Linux-datorn
Nätverkskort är program mässigt tillgängliga på grund av att du kan tillämpa regler på deras användning. Du kan också ha fler än en. I följande `azure network nic create` kommando kopplar du nätverkskortet till belastnings-IP-poolen och kopplar det till NAT-regeln för att tillåta SSH-trafik.

`#####-###-###` Ersätt avsnitten med ditt eget ID för Azure-prenumeration. Ditt prenumerations-ID anges i resultatet av `jq` när du granskar de resurser som du skapar. Du kan också visa ditt prenumerations- `azure account list`ID med.

I följande exempel skapas ett nätverkskort med `myNic1`namnet:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Resultat:

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

Du kan se informationen genom att undersöka resursen direkt. Du undersöker resursen med hjälp `azure network nic show` av kommandot:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Resultat:

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

Nu ska vi skapa det andra NÄTVERKSKORTet, ansluta till vår backend-IP-pool igen. Den här gången tillåter den andra NAT-regeln SSH-trafik. I följande exempel skapas ett nätverkskort med `myNic2`namnet:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Skapa en nätverks säkerhets grupp och regler
Nu skapar vi en nätverks säkerhets grupp och de regler för inkommande trafik som styr åtkomsten till NÄTVERKSKORTet. En nätverks säkerhets grupp kan tillämpas på ett nätverkskort eller undernät. Du definierar regler för att styra trafik flödet i och ut ur dina virtuella datorer. I följande exempel skapas en nätverks säkerhets grupp med `myNetworkSecurityGroup`namnet:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Nu ska vi lägga till regeln för inkommande trafik för NSG för att tillåta inkommande anslutningar på port 22 (som stöd för SSH). I följande exempel skapas en regel med `myNetworkSecurityGroupRuleSSH` namnet att tillåta TCP på port 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Nu ska vi lägga till regeln för inkommande trafik för NSG för att tillåta inkommande anslutningar på port 80 (för att stödja webb trafik). I följande exempel skapas en regel med `myNetworkSecurityGroupRuleHTTP` namnet att tillåta TCP på port 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Regeln för inkommande trafik är ett filter för inkommande nätverks anslutningar. I det här exemplet binder vi NSG till virtuella datorer för virtuella datorer, vilket innebär att alla förfrågningar till port 22 skickas till NÄTVERKSKORTet på den virtuella datorn. Den här inkommande regeln gäller om en nätverks anslutning och inte en slut punkt, vilket är vad den skulle göra i de klassiska distributionerna. Om du vill öppna en port måste du lämna `--source-port-range` inställningen\*till (standardvärdet) om du vill acceptera inkommande begär Anden från **alla** begär ande portar. Portar är vanligt vis dynamiska.
>
>

## <a name="bind-to-the-nic"></a>Binda till NÄTVERKSKORTet
Bind NSG till nätverkskorten. Vi måste ansluta våra nätverkskort till vår nätverks säkerhets grupp. Använd båda kommandona för att koppla samman båda nätverkskorten:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Tillgänglighets uppsättningar hjälper till att sprida dina virtuella datorer över fel domäner och uppgraderings domäner. Nu ska vi skapa en tillgänglighets uppsättning för dina virtuella datorer. I följande exempel skapas en tillgänglighets uppsättning `myAvailabilitySet`med namnet:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Fel domäner definierar en gruppering av virtuella datorer som delar en gemensam ström källa och nätverks växel. Som standard är de virtuella datorer som är konfigurerade i din tillgänglighets uppsättning åtskilda över upp till tre fel domäner. Tanken är att ett maskin varu problem i någon av dessa fel domäner inte påverkar varje virtuell dator som kör din app. Azure distribuerar automatiskt virtuella datorer över fel domäner när de placeras i en tillgänglighets uppsättning.

Uppgraderings domäner indikerar grupper av virtuella datorer och underliggande fysisk maskin vara som kan startas om samtidigt. Den ordning i vilken uppgraderings domäner startas om kanske inte är sekventiell under planerat underhåll, men endast en uppgradering startas om i taget. Azure distribuerar automatiskt dina virtuella datorer i uppgraderings domäner när de placeras på en tillgänglighets plats.

Läs mer om [att hantera tillgängligheten för virtuella datorer](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Skapa virtuella Linux-datorer
Du har skapat lagrings-och nätverks resurser som stöder Internet-tillgängliga virtuella datorer. Nu ska vi skapa de virtuella datorerna och skydda dem med en SSH-nyckel som inte har ett lösen ord. I det här fallet ska vi skapa en virtuell Ubuntu-dator som baseras på den senaste LTS. Vi hittar avbildnings informationen med hjälp `azure vm image list`av, enligt beskrivningen i [hitta Azure VM](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-avbildningar.

Vi har valt en avbildning med hjälp av `azure vm image list westeurope canonical | grep LTS`kommandot. I det här fallet använder `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`vi. För det sista fältet passerar `latest` vi så att vi alltid får den senaste versionen i framtiden. (Den sträng som vi använder `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`är).

Nästa steg är att känna till alla som redan har skapat ett offentligt och privat SSH-nyckelpar i Linux eller Mac med **ssh-keygen-t RSA-b 2048**. Om du inte har några certifikat nyckel par i `~/.ssh` katalogen kan du skapa dem:

* Automatiskt med hjälp `azure vm create --generate-ssh-keys` av alternativet.
* Manuellt genom att följa [anvisningarna för att skapa dem själv](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan också använda `--admin-password` -metoden för att autentisera dina SSH-anslutningar när den virtuella datorn har skapats. Den här metoden är vanligt vis mindre säker.

Vi skapar den virtuella datorn genom att ta med alla våra resurser och information `azure vm create` tillsammans med kommandot:

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

Resultat:

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

Du kan ansluta till din virtuella dator direkt genom att använda dina standard SSH-nycklar. Se till att du anger rätt port eftersom vi passerar genom belastningsutjämnaren. (För den första virtuella datorn ställer vi in NAT-regeln för att vidarebefordra port 4222 till vår virtuella dator.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Resultat:

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

Gå vidare och skapa den andra virtuella datorn på samma sätt:

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

Du kan nu använda `azure vm show myResourceGroup myVM1` kommandot för att undersöka vad du har skapat. Nu kör du dina virtuella Ubuntu-datorer bakom en belastningsutjämnare i Azure som du bara kan logga in på med SSH-nyckelpar (eftersom lösen ord är inaktiverade). Du kan installera nginx eller httpd, distribuera en webbapp och se trafikflödet via belastningsutjämnaren till båda de virtuella datorerna.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Resultat:

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
Nu när du har skapat den här miljön, vad gör du om du vill skapa en ytterligare utvecklings miljö med samma parametrar eller en produktions miljö som matchar den? Resource Manager använder JSON-mallar som definierar alla parametrar för din miljö. Du skapar hela miljöer genom att referera till den här JSON-mallen. Du kan skapa JSON- [mallar manuellt](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller exportera en befintlig miljö för att skapa en JSON-mall åt dig:

```azurecli
azure group export --name myResourceGroup
```

Det här kommandot skapar `myResourceGroup.json` filen i din aktuella arbets katalog. När du skapar en miljö från den här mallen uppmanas du att ange alla resurs namn, inklusive namnen på belastningsutjämnaren, nätverks gränssnitt eller virtuella datorer. Du kan fylla i namnen i mallfilen genom att lägga till `-p` parametern `azure group export` eller `--includeParameterDefaultValue` i kommandot som visades tidigare. Redigera din JSON-mall för att ange resurs namnen eller [skapa en Parameters. JSON-fil](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som anger resurs namnen.

Så här skapar du en miljö från mallen:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Du kanske vill läsa [mer om hur du distribuerar från mallar](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Lär dig mer om hur du stegvis uppdaterar miljöer, använder parameter filen och får åtkomst till mallar från en enda lagrings plats.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att börja arbeta med flera nätverks komponenter och virtuella datorer. Du kan använda den här exempel miljön för att bygga upp ditt program genom att använda kärn komponenterna som introduceras här.
