---
title: Skapa en Linux-miljö med Azure CLI
description: Skapa lagring, en virtuell Linux-dator, ett virtuellt nätverk och undernät, en belastningsutjämnare, ett nätverkskort, en offentlig IP och en nätverkssäkerhetsgrupp, allt från grunden med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 7ee4674f5e7c04709256459c3417a1379a65aedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969551"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Skapa en komplett virtuell Linux-dator med Azure CLI
Om du snabbt vill skapa en virtuell dator (VM) i Azure kan du använda ett enda Azure CLI-kommando som använder standardvärden för att skapa nödvändiga stödresurser. Resurser som ett virtuellt nätverk, offentlig IP-adress och nätverkssäkerhetsgruppsregler skapas automatiskt. Om du vill ha mer kontroll över din miljö i produktionsanvändning kan du skapa dessa resurser i förväg och sedan lägga till dina virtuella datorer i dem. Den här artikeln guidar dig genom hur du skapar en virtuell dator och var och en av de stödjande resurserna en efter en.

Kontrollera att du har installerat den senaste [Azure CLI](/cli/azure/install-az-cli2) och loggat in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderar *myResourceGroup,* *myVnet*och *myVM*.

## <a name="create-resource-group"></a>Skapa resursgrupp
En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före en virtuell dator och stödja virtuella nätverksresurser. Skapa resursgruppen med [az-gruppge](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Som standard finns utdata från Azure CLI-kommandon i JSON (JavaScript Object Notation). Om du vill ändra standardutdata till en lista eller tabell, till exempel, använder [du az configure --output](/cli/azure/reference-index). Du kan `--output` också lägga till alla kommandon för en engångsändring i utdataformatet. I följande exempel visas JSON-utdata från `az group create` kommandot:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät
Därefter skapar du ett virtuellt nätverk i Azure och ett undernät där du kan skapa dina virtuella datorer. Använd [az-nätverksvnet skapa](/cli/azure/network/vnet) för att skapa ett virtuellt nätverk med namnet *myVnet* med *192.168.0.0/16* adressprefix. Du lägger också till ett undernät med namnet *mySubnet* med adressprefixet *192.168.1.0/24:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Utdata visar att undernätet logiskt skapas i det virtuella nätverket:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
Nu ska vi skapa en offentlig [IP-adress med az nätverk public-ip skapa](/cli/azure/network/public-ip). Med den här offentliga IP-adressen kan du ansluta till dina virtuella datorer från Internet. Eftersom standardadressen är dynamisk skapar du `--domain-name-label` en namngiven DNS-post med parametern. I följande exempel skapas en offentlig IP med namnet *myPublicIP* med DNS-namnet *mypublicdns*. Eftersom DNS-namnet måste vara unikt anger du ditt eget unika DNS-namn:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Resultat:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Om du vill styra trafikflödet till och från dina virtuella datorer använder du en nätverkssäkerhetsgrupp på ett virtuellt nätverkskort eller undernät. I följande exempel används [az network nsg create](/cli/azure/network/nsg) för att skapa en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Du definierar regler som tillåter eller nekar specifik trafik. Om du vill tillåta inkommande anslutningar på port 22 (för att aktivera SSH-åtkomst) skapar du en inkommande regel med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRuleSSH:*

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Om du vill tillåta inkommande anslutningar på port 80 (för webbtrafik) lägger du till en annan regel för nätverksskyddsgrupper. I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRuleHTTP:*

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Undersök nätverkssäkerhetsgruppen och regler med [az network nsg show:](/cli/azure/network/nsg)

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Resultat:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Skapa ett virtuellt nätverkskort
Nätverkskort (Virtual Network Interface Cards) är programmatiskt tillgängliga eftersom du kan tillämpa regler för deras användning. Beroende på storleken på den [virtuella datorn](sizes.md)kan du koppla flera virtuella nätverkskort till en virtuell dator. I följande kommando [för az-nätverksetisk nic skapar](/cli/azure/network/nic) du ett nätverkskort med namnet *myNic* och associerar det med nätverkssäkerhetsgruppen. Den offentliga IP-adressen *myPublicIP* är också associerad med det virtuella nätverkskortet.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Resultat:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Tillgänglighetsuppsättningar hjälper till att sprida dina virtuella datorer över feldomäner och uppdatera domäner. Även om du bara skapar en virtuell dator just nu är det bäst att använda tillgänglighetsuppsättningar för att göra det enklare att expandera i framtiden. 

Feldomäner definierar en gruppering av virtuella datorer som delar en gemensam strömkälla och nätverksväxel. Som standard är de virtuella datorer som är konfigurerade i din tillgänglighetsuppsättning separerade över upp till tre feldomäner. Ett maskinvaruproblem i en av dessa feldomäner påverkar inte alla virtuella datorer som kör appen.

Uppdateringsdomäner anger grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Under planerat underhåll kanske den ordning i vilken uppdateringsdomäner startas om inte sekventiell, men endast en uppdateringsdomän startas om åt gången.

Azure distribuerar automatiskt virtuella datorer över fel- och uppdateringsdomänerna när de placeras i en tillgänglighetsuppsättning. Mer information finns [i hantera tillgängligheten för virtuella datorer](manage-availability.md).

Skapa en tillgänglighetsuppsättning för den virtuella datorn med [az vm-tillgänglighetsuppsättning skapa](/cli/azure/vm/availability-set). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Feldomänerna och uppdateringsdomänerna för utdata noterar:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Skapa en virtuell dator
Du har skapat nätverksresurserna för att stödja virtuella datorer som är tillgängliga för Internet. Skapa nu en virtuell dator och säkra den med en SSH-nyckel. I det här exemplet ska vi skapa en Ubuntu-virtuell dator baserat på den senaste LTS.In this example, let's create an Ubuntu VM based on the recent LTS. Du kan hitta ytterligare avbildningar med [az vm-avbildningslista](/cli/azure/vm/image), enligt beskrivningen i [att hitta Azure VM-avbildningar](cli-ps-findimage.md).

Ange en SSH-nyckel som ska användas för autentisering. Om du inte har ett SSH-offentligt nyckelpar kan `--generate-ssh-keys` du skapa [dem](mac-create-ssh-keys.md) eller använda parametern för att skapa dem åt dig. Om du redan har ett nyckelpar använder `~/.ssh`den här parametern befintliga nycklar i .

Skapa den virtuella datorn genom att föra alla resurser och information tillsammans med kommandot [az vm create.](/cli/azure/vm) I följande exempel skapas en virtuell dator med namnet *myVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till din virtuella dator med den DNS-post som du angav när du skapade den offentliga IP-adressen. Detta `fqdn` visas i utdata när du skapar din virtuella dator:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Resultat:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Du kan installera NGINX och se trafikflödet till den virtuella datorn. Installera NGINX enligt följande:

```bash
sudo apt-get install -y nginx
```

Om du vill se den vanliga NGINX-webbplatsen i aktion öppnar du webbläsaren och anger din FQDN:

![Standard-NGINX-webbplats på den virtuella datorn](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportera som mall
Vad händer om du nu vill skapa en ytterligare utvecklingsmiljö med samma parametrar eller en produktionsmiljö som matchar den? Resource Manager använder JSON-mallar som definierar alla parametrar för din miljö. Du skapar hela miljöer genom att referera till den här JSON-mallen. Du kan [skapa JSON-mallar manuellt](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller exportera en befintlig miljö för att skapa JSON-mallen åt dig. Använd [az-gruppexport](/cli/azure/group) för att exportera resursgruppen enligt följande:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Med det här `myResourceGroup.json` kommandot skapas filen i den aktuella arbetskatalogen. När du skapar en miljö från den här mallen uppmanas du att ange alla resursnamn. Du kan fylla i dessa namn i `--include-parameter-default-value` mallfilen `az group export` genom att lägga till parametern i kommandot. Redigera JSON-mallen för att ange resursnamnen eller [skapa en parameters.json-fil](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som anger resursnamnen.

Om du vill skapa en miljö från mallen använder du [az-gruppdistribution skapa](/cli/azure/group/deployment) enligt följande:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Du kanske vill läsa [mer om hur du distribuerar från mallar](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Lär dig mer om hur du stegvis uppdaterar miljöer, använder parameterfilen och kommer åt mallar från en enda lagringsplats.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att börja arbeta med flera nätverkskomponenter och virtuella datorer. Du kan använda den här exempelmiljön för att bygga ut ditt program med hjälp av de kärnkomponenter som introduceras här.
