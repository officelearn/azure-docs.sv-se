---
title: "Virtuella Azure-nätverk och virtuella Linux-datorer | Microsoft Docs"
description: "Självstudiekurs – hantera virtuella Azure-nätverk och virtuella Linux-datorer med Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a49b4c2d4ddd6d686675cee53d46cd4dd6ad3811
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Hantera virtuella Azure-nätverk och virtuella Linux-datorer med Azure CLI

Azure virtual machines använder Azure-nätverk för interna och externa nätverkskommunikation. Den här kursen går igenom hur du distribuerar två virtuella datorer och konfigurera Azure nätverk för dessa virtuella datorer. Exemplen i den här kursen förutsätter att de virtuella datorerna är värd för ett webbprogram med en-databas, men ett program inte har distribuerats i självstudiekursen. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och undernät
> * Skapa en offentlig IP-adress
> * Skapa en frontend virtuell dator
> * Skydda nätverkstrafik
> * Skapa en backend-virtuell dator

Du kan se dessa resurser som skapas när den här kursen:

![Virtuellt nätverk med två undernät](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* -det virtuella nätverket som de virtuella datorerna använder för att kommunicera med varandra och internet.
- *myFrontendSubnet* -undernät i *myVNet* används av frontend resurserna.
- *myPublicIPAddress* -offentlig IP-adress som används för att få åtkomst till *myFrontendVM* från internet.
- *myFrontentNic* -nätverksgränssnittet som används av *myFrontendVM* att kommunicera med *myBackendVM*.
- *myFrontendVM* -den virtuella datorns används för att kommunicera mellan internet och *myBackendVM*.
- *myBackendNSG* -nätverkssäkerhetsgruppen som styr kommunikationen mellan den *myFrontendVM* och *myBackendVM*.
- *myBackendSubnet* -undernätet med *myBackendNSG* och används av backend-resurser.
- *myBackendNic* -nätverksgränssnittet som används av *myBackendVM* att kommunicera med *myFrontendVM*.
- *myBackendVM* -den virtuella datorns som använder port 22 och 3306 för att kommunicera med *myFrontendVM*.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="vm-networking-overview"></a>VM-nätverk – översikt

Virtuella Azure-nätverk kan säkra nätverksanslutningar mellan virtuella datorer, internet och andra Azure-tjänster, till exempel Azure SQL-databas. Virtuella nätverk är uppdelad i logiska segment som kallas subnät. Undernät används för flödeskontroll för nätverk och som en säkerhetsgräns. När du distribuerar en virtuell dator innehåller vanligtvis ett virtuellt nätverksgränssnitt som är ansluten till ett undernät.

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och undernät

Ett enda virtuellt nätverk skapas med två undernät för den här kursen. Ett frontend undernät som värd för ett webbprogram och ett backend-undernät som värd för en databasserver.

Innan du kan skapa ett virtuellt nätverk, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myRGNetwork* eastus plats.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Använd den [az network vnet skapa](/cli/azure/network/vnet#create) kommando för att skapa ett virtuellt nätverk. I det här exemplet är nätverket heter *mvVNet* och ges en adressprefixet *10.0.0.0/16*. Dessutom skapas ett undernät med namnet *myFrontendSubnet* och prefixet *10.0.1.0/24*. Senare i den här självstudiekursen är frontend VM anslutet till det här undernätet. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Skapa ett undernät

Ett nytt undernät har lagts till i det virtuella nätverket med hjälp av den [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#create) kommando. I det här exemplet undernätet med namnet *myBackendSubnet* och ges en adressprefixet *10.0.2.0/24*. Det här undernätet används med alla backend-tjänster.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Ett nätverk har nu skapats och upp i två undernät, en för frontend-tjänster och en för backend-tjänster. I nästa avsnitt, virtuella datorer skapas och anslutna till dessa undernät.

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

En offentlig IP-adress kan Azure-resurser ska vara tillgänglig på internet. Allokeringsmetod för offentliga IP-adressen kan konfigureras som dynamiska eller statiska. Som standard tilldelas dynamiskt offentlig IP-adress. Dynamiska IP-adresser släpps när en virtuell dator har frigjorts. Detta medför IP-adressen ändras under åtgärder som innehåller en VM-flyttningen.

Allokeringsmetoden kan anges som statisk, vilket garanterar att IP-adressen fortfarande kopplad till en virtuell dator, även under en frigjord tillstånd. Den IP-adressen kan inte anges när du använder en statiskt tilldelade IP-adress. Istället tilldelas den från en pool med tillgängliga adresser.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

När du skapar en virtuell dator med den [az vm skapa](/cli/azure/vm#create) kommandot offentliga IP-adress allokering standardmetoden är dynamisk. När du skapar en virtuell dator med hjälp av den [az vm skapa](/cli/azure/vm#create) kommandot, innehåller den `--public-ip-address-allocation static` argumentet att tilldela en statisk offentlig IP-adress. Den här åtgärden visas inte i den här självstudiekursen, men i nästa avsnitt ändras dynamiskt allokerade IP-adress till ett statiskt allokerade adressen. 

### <a name="change-allocation-method"></a>Ändra allokeringsmetod

Allokeringsmetod för IP-adress kan ändras med den [az nätverket offentliga ip-uppdatering](/cli/azure/network/public-ip#update) kommando. I det här exemplet ändras allokeringsmetoden av frontend VM IP-adress till statisk.

Ta bort den virtuella datorn först.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Använd den [az nätverket offentliga ip-uppdatering](/cli/azure/network/public-ip#update) kommando för att uppdatera allokeringsmetoden. I det här fallet den `--allocation-method` anges till *statiska*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Starta den virtuella datorn.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Ingen offentlig IP-adress

Ofta behöver inte en virtuell dator som är tillgängliga via internet. Om du vill skapa en virtuell dator utan en offentlig IP-adress, använder den `--public-ip-address ""` argument med en tom uppsättning med dubbla citattecken. Den här konfigurationen visas längre fram i den här kursen.

## <a name="create-a-front-end-vm"></a>Skapa en frontend virtuell dator

Använd den [az vm skapa](/cli/azure/vm#create) kommando för att skapa den virtuella datorn med namnet *myFrontendVM* med *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Skydda nätverkstrafik

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). NSG: er kan vara kopplad till undernät eller individuella nätverksgränssnitt. När en NSG är associerad med ett nätverksgränssnitt gäller bara den associera virtuella datorn. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. 

### <a name="network-security-group-rules"></a>Regler för nätverkssäkerhetsgrupper

NSG-regler definiera nätverk portar under vilken trafik tillåts eller nekas. Regler kan innehålla käll- och IP-adressintervall så att trafik styrs mellan specifika system eller undernät. NSG-regler kan även innehålla en prioritet (mellan 1 – och 4096). Reglerna utvärderas prioritsordning. En regel med en prioritet på 100 utvärderas innan en regel med prioritet 200.

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar.

- **Virtuellt nätverk** - trafik med ursprung och slutar med ett virtuellt nätverk tillåts både i inkommande och utgående riktningar.
- **Internet** - utgående trafik tillåts, men inkommande trafik blockeras.
- **Belastningsutjämnaren** -Tillåt Azure belastningsutjämnare avsökning hälsotillståndet för dina virtuella datorer och rollinstanser. Om du inte använder en belastningsutjämnad uppsättning, kan du åsidosätta den här regeln.

### <a name="create-network-security-groups"></a>Skapa säkerhetsgrupper för nätverk

En nätverkssäkerhetsgrupp kan skapas samtidigt som en virtuell dator med hjälp av den [az vm skapa](/cli/azure/vm#create) kommando. När du gör det NSG: N är kopplad till nätverksgränssnitt för virtuella datorer och en NSG-regel är skapade för att tillåta trafik på port automatiskt *22* från andra källor. Tidigare i den här självstudien har frontend NSG: N skapats automatiskt med frontend VM. En regel för NSG har också automatiskt skapat för port 22. 

I vissa fall kan vara det bra att skapa en NSG till exempel när standardreglerna för SSH inte skapas eller när NSG: N ska kopplas till ett undernät. 

Använd den [az nätverket nsg skapa](/cli/azure/network/nsg#create) kommando för att skapa en nätverkssäkerhetsgrupp.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

I stället för att koppla NSG till ett nätverksgränssnitt, är den associerad med ett undernät. I den här konfigurationen ärver någon virtuell dator som är kopplad till undernätet NSG-regler.

Uppdatera befintliga undernätet med namnet *myBackendSubnet* med nya NSG: N.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Skydda inkommande trafik

När frontend VM skapades, skapades en NSG-regel för att tillåta inkommande trafik på port 22. Den här regeln kan SSH-anslutningar till den virtuella datorn. I det här exemplet trafik även ska tillåtas på port *80*. Den här konfigurationen kan ett program som kan nås på den virtuella datorn.

Använd den [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#create) kommando för att skapa en regel för port *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Frontend VM är endast tillgänglig på port *22* och port *80*. All annan inkommande trafik blockeras på nätverkssäkerhetsgruppen. Det kan vara bra att visualisera regelkonfigurationer NSG. Returnera NSG regelkonfigurationen med den [az nätverket regellistan](/cli/azure/network/nsg/rule#list) kommando. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Säker VM VM-trafik

Regler för nätverkssäkerhetsgrupper kan också använda mellan virtuella datorer. I det här exemplet frontend VM behöver kommunicera med backend-VM på port *22* och *3306*. Den här konfigurationen tillåter SSH-anslutningar från frontend VM och även att ett program på den frontend virtuella datorn kan kommunicera med en backend-MySQL-databas. All annan trafik ska blockeras mellan frontend- och virtuella datorer.

Använd den [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#create) kommando för att skapa en regel för port 22. Observera att den `--source-address-prefix` argumentet anger ett värde för *10.0.1.0/24*. Den här konfigurationen säkerställer att endast trafik från undernätet som frontend tillåts via NSG: N.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Lägg till regel för MySQL-trafik på port 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Slutligen: eftersom NSG: er har en standardregel som tillåter all trafik mellan virtuella datorer i samma virtuella nätverk, en regel skapas för backend-NSG: er att blockera all trafik. Observera som den `--priority` ges värdet *300*, vilket är lägre som både MySQL och NSG-regler. Den här konfigurationen garanterar att SSH och MySQL trafiken fortfarande tillåts NSG: N.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Skapa backend-VM

Nu skapa en virtuell dator som är ansluten till den *myBackendSubnet*. Observera att den `--nsg` argumentet har värdet tomt dubbla citattecken. En NSG behöver inte skapas med den virtuella datorn. Den virtuella datorn är ansluten till backend-undernät, som är skyddat med backend-förskapade NSG: N. Den här NSG gäller för den virtuella datorn. Observera också här som den `--public-ip-address` argumentet har värdet tomt dubbla citattecken. Den här konfigurationen skapar en virtuell dator utan en offentlig IP-adress. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Backend-VM är endast tillgänglig på port *22* och port *3306* från frontend undernät. All annan inkommande trafik blockeras på nätverkssäkerhetsgruppen. Det kan vara bra att visualisera regelkonfigurationer NSG. Returnera NSG regelkonfigurationen med den [az nätverket regellistan](/cli/azure/network/nsg/rule#list) kommando. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skapas och skyddas av Azure-nätverk som är relaterade till virtuella datorer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och undernät
> * Skapa en offentlig IP-adress
> * Skapa en frontend virtuell dator
> * Skydda nätverkstrafik
> * Skapa backend-VM

Gå vidare till nästa kurs att lära dig att skydda data på virtuella datorer med Azure backup. 

> [!div class="nextstepaction"]
> [Säkerhetskopiera virtuella Linux-datorer i Azure](./tutorial-backup-vms.md)
