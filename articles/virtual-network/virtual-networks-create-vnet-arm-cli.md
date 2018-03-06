---
title: "Skapa ett virtuellt Azure-nätverk med flera undernät - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk med flera undernät med hjälp av Azure CLI."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: feb8fb218b8bde88ba7fbdc3419e32c9313c7885
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Skapa ett virtuellt nätverk med flera undernät med hjälp av Azure CLI

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser kommunicera med Internet och privat med varandra. Skapa flera undernät i ett virtuellt nätverk kan du segmentera nätverket så att du kan filtrera eller kontrollera trafikflödet mellan undernät. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa ett undernät
> * Testa nätverkskommunikation mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*. Kommandot skapar ett undernät med namnet *offentliga*, med adressprefixet *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Eftersom en plats inte har angetts i det föregående kommandot, Azure skapar det virtuella nätverket på samma plats som den *myResourceGroup* resursgruppen finns i. Den **-adressprefix** och **undernätsprefixet** anges i CIDR-notering. Det angivna adressprefixet innehåller 10.0.0.0-10.0.255.254 för IP-adresser. Prefix som har angetts för undernätet måste finnas inom adressprefixet som definierats för det virtuella nätverket. Azure DHCP tilldelas IP-adresser från ett undernät adressprefix resurser har distribuerats i ett undernät. Azure endast tilldelas adresser 10.0.0.4-10.0.0.254 resurser har distribuerats inom den **offentliga** undernät, eftersom Azure reserverar de första fyra adresserna (10.0.0.0-10.0.0.3 för undernät i det här exemplet) och sist adressen ( 10.0.0.255 för undernät i det här exemplet) i varje undernät.

## <a name="create-a-subnet"></a>Skapa ett undernät

Skapa ett undernät med [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). I följande exempel skapas ett undernät med namnet *privata* inom den *myVirtualNetwork* virtuellt nätverk med adressprefixet *10.0.1.0/24*. Adressprefixet måste vara inom adressprefixet som definierats för det virtuella nätverket och får inte överlappa adressprefixet med andra undernät i det virtuella nätverket.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Innan du distribuerar virtuella Azure-nätverk och undernät för produktion, rekommenderar vi att du noggrant bekanta dig med adressutrymme [överväganden](virtual-network-manage-network.md#create-a-virtual-network) och [virtuellt nätverk gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). När resurser har distribuerats till undernät kräver vissa virtuella nätverk och undernät ändringar, till exempel ändra adressintervall Omdistributionen av befintliga Azure-resurser som distribueras inom undernät.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser kommunicera med Internet och privat med varandra. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator. Skapa två virtuella datorer i det virtuella nätverket så att du kan testa nätverkskommunikation mellan dem och Internet i ett senare steg.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVmWeb* i den *offentliga* undernät. Den `--no-wait` parametern aktiverar Azure för att köra kommandot i bakgrunden så du kan fortsätta med nästa kommando. Ett lösenord används för att förenkla den här kursen. Nycklar används vanligtvis för produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentvidarebefordran för att slutföra stegen. Mer information om SSH-agentvidarebefordran finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Azure tilldelas automatiskt 10.0.0.4 som privata IP-adressen för den virtuella datorn, eftersom 10.0.0.4 är den första tillgängliga IP-adressen i den *offentliga* undernät. 

Skapa en virtuell dator i den *privata* undernät.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn returnerar Azure CLI-utdata liknar följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Exempel på utdata visas i som den **privateIpAddress** är *10.0.1.4*. Azure skapas ett [nätverksgränssnittet](virtual-network-network-interface.md), kopplade till den virtuella datorn, och tilldelas nätverksgränssnittet en privat IP-adress och en **macAddress**. Azure DHCP automatiskt tilldelas 10.0.1.4 nätverksgränssnittet eftersom det är den första tillgängliga IP-adressen i den *privata* undernät. De privata IP- och MAC-adresserna fortsätter vara tilldelade till nätverksgränssnittet tills nätverksgränssnittet tas bort. 

Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn från Internet i ett senare steg. Även om en virtuell dator inte är nödvändigt att ha en offentlig IP-adress som tilldelats, tilldelar Azure en offentlig IP-adress till varje virtuell dator som du skapar som standard. För att kommunicera från Internet till en virtuell dator, måste en offentlig IP-adress tilldelas till den virtuella datorn. Alla virtuella datorer kan kommunicera utgående med Internet, oavsett om en offentlig IP-adress har tilldelats den virtuella datorn. Mer information om utgående Internet-anslutningar i Azure finns [utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

De virtuella datorerna som skapats i den här artikeln har ett [nätverksgränssnittet](virtual-network-network-interface.md) med en IP-adress som tilldelas dynamiskt till nätverksgränssnittet. När du har distribuerat den virtuella datorn, kan du [lägga till flera offentliga och privata IP-adresser eller ändra tilldelningsmetod för IP-adress till statisk](virtual-network-network-interface-addresses.md#add-ip-addresses). Du kan [Lägg till nätverksgränssnitt](virtual-network-network-interface-vm.md#vm-add-nic), upp till den gräns som stöds av den [VM-storlek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att du väljer när du skapar en virtuell dator. Du kan också [aktivera single-root I/O virtualization (SR-IOV)](create-vm-accelerated-networking-cli.md) för en virtuell dator, men bara när du skapar en virtuell dator med en VM-storlek som stöder kapaciteten.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Kommunikation mellan virtuella datorer och internet

Använd följande kommando för att skapa en SSH-session med den *myVmMgmt* virtuella datorn. Ersätt `<publicIpAddress>` med offentliga IP-adressen för den virtuella datorn. I föregående exempel är den offentliga IP-adressen är *13.90.242.231*. När du uppmanas att ange ett lösenord, ange lösenordet du angav i [skapa virtuella datorer](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Av säkerhetsskäl är det vanligt att begränsa antalet virtuella datorer som via fjärranslutning kan anslutas till i ett virtuellt nätverk. I den här självstudiekursen den *myVmMgmt* virtuella används för att hantera den *myVmWeb* virtuell dator i det virtuella nätverket. Använd följande kommando för att SSH till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuell dator:

```bash 
ssh azureuser@myVmWeb
```

Att kommunicera med den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn, anger du följande kommando från en kommandotolk:

```
ping -c 4 myvmmgmt
```

Utdata som liknar följande exempel utdata visas:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Du kan se som adressen till den *myVmMgmt* virtuella datorn är 10.0.1.4. 10.0.1.4 var den första tillgängliga IP-adressen i adressintervallet i *privata* undernät som du har distribuerat den *myVmMgmt* virtuella datorn till i föregående steg.  Du ser att det fullständigt kvalificerade domännamnet för den virtuella datorn är *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Även om den *hxehizax3z1udjnrx1r4gr30pg* del av domännamnet är olika för den virtuella datorn, återstående delar av namnet på en domän är samma. Som standard använder alla virtuella Azure-datorer standard Azure DNS-tjänsten. Alla virtuella datorer i ett virtuellt nätverk kan matcha namnen på alla andra virtuella datorer i samma virtuella nätverk med Azures standard DNS-tjänsten. Du kan använda DNS-servern eller den privata förmågan för Azure DNS-tjänsten istället för att använda Azures standard DNS-tjänsten. Mer information finns i [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) eller [med hjälp av Azure DNS för privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Använd följande kommandon för att installera den nginx-servern på den *myVmWeb* virtuell dator:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

När nginx har installerats kan du stänga den *myVmWeb* SSH-session, vilket lämnar du i Kommandotolken för den *myVmMgmt* virtuella datorn. Ange följande kommando för att hämta nginx välkomstskärmen från den *myVmWeb* virtuella datorn.

```bash
curl myVmWeb
```

Välkomstskärmen nginx returneras.

Stäng SSH-session med den *myVmMgmt* virtuella datorn.

När Azure skapade den *myVmWeb* virtuell dator, en offentlig IP-adress med namnet *myVmWebPublicIP* också skapades och tilldelade till den virtuella datorn. Hämta den offentliga adressen Azure tilldelade med [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

Från din dator, ange följande kommando ersätter `<publicIpAddress>` med adressen som returneras från det föregående kommandot:

```bash
curl <publicIpAddress>
```

Det går inte att försöket att curl nginx välkomstskärmen från din dator. Försöket misslyckas eftersom när de virtuella datorerna har distribuerats Azure skapas en nätverkssäkerhetsgrupp för varje virtuell dator som standard. 

En nätverkssäkerhetsgrupp innehåller säkerhetsregler som tillåter eller nekar inkommande och utgående nätverkstrafik genom porten och IP-adress. Standard-nätverkssäkerhetsgruppen Azure skapas tillåter kommunikation via alla portar mellan resurser i samma virtuella nätverk. Nätverkssäkerhetsgruppen standard nekar all inkommande trafik från Internet via alla portar för Linux virtuella datorer måste acceptera TCP-port 22 (SSH). Därför kan som standard, du kan också skapa en SSH-session direkt till den *myVmWeb* virtuell dator från Internet, även om du inte vill port 22 öppen på en webbserver. Eftersom den `curl` kommandot kommunicerar via port 80, kommunikationen misslyckas från Internet eftersom det finns ingen regel i standard nätverkssäkerhetsgruppen tillåter trafik via port 80.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du distribuerar ett virtuellt nätverk med flera undernät. Du också lära dig att när du skapar en virtuell Linux-dator Azure skapar ett nätverksgränssnitt som den kopplas till den virtuella datorn och skapar en nätverkssäkerhetsgrupp som endast tillåter trafik via port 22, från Internet. Gå vidare till nästa kurs att lära dig att filtrera trafik till undernät i stället för till enskilda virtuella datorer.

> [!div class="nextstepaction"]
> [Filtrera trafik till undernät](./virtual-networks-create-nsg-arm-cli.md)
