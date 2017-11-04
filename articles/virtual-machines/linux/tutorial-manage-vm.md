---
title: Skapa och hantera virtuella Linux-datorer med Azure CLI | Microsoft Docs
description: "Självstudiekurs – skapa och hantera virtuella Linux-datorer med Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: bef7f6ef13f6d31c16d40deb46f168ae52a9e61b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-linux-vms-with-the-azure-cli"></a>Skapa och hantera virtuella Linux-datorer med Azure CLI

Virtuella datorer i Azure ger en fullständigt konfigurerbara och flexibel datormiljö. Den här kursen ingår grundläggande virtuella Azure-datorn distribution objekt, till exempel välja en VM-storlek, välja en VM-avbildning och distribuera en virtuell dator. Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välj och Använd VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuell dator


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas innan en virtuell dator. I det här exemplet en resursgrupp med namnet *myResourceGroupVM* skapas i den *eastus* region. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

Resursgruppen har angetts när du skapar eller ändrar en VM som kan ses i hela den här kursen.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med den [az vm skapa](https://docs.microsoft.com/cli/azure/vm#az_vm_create) kommando. 

När du skapar en virtuell dator, är flera alternativ tillgängliga, till exempel operativsystemavbildningen, storlek och administrativa autentiseringsuppgifter för disken. I det här exemplet skapas en virtuell dator med namnet *myVM* kör Ubuntu Server. 

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys
```

Det kan ta några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats, Azure CLI matar ut information om den virtuella datorn. Anteckna den `publicIpAddress`, den här adressen kan användas för åtkomst till den virtuella datorn... 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Ansluta till en virtuell dator

Nu kan du ansluta till den virtuella datorn med SSH i Azure Cloud-gränssnittet eller från den lokala datorn. Ersätt den IP-adressen med den `publicIpAddress` anges i föregående steg.

```bash
ssh 52.174.34.95
```

Efter loggat in till den virtuella datorn kan du installera och konfigurera program. När du är klar stänger du SSH-session som vanligt:

```bash
exit
```

## <a name="understand-vm-images"></a>Förstå VM-avbildningar

Azure marketplace innehåller många avbildningar som kan användas för att skapa virtuella datorer. I de föregående stegen skapades en virtuell dator med en Ubuntu-bild. I det här steget används Azure CLI för att söka marketplace för en avbildning av CentOS, som sedan används för att distribuera en andra virtuell dator.  

Om du vill se en lista över de vanligaste bilder, den [az vm bildlista](/cli/azure/vm/image#list) kommando.

```azurecli-interactive 
az vm image list --output table
```

Kommandoutdata returnerar de mest populära VM-avbildningarna i Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

En fullständig lista kan visas genom att lägga till den `--all` argumentet. Bildlistan kan också filtreras efter `--publisher` eller `–-offer`. I det här exemplet listan filtreras för alla bilder med ett erbjudande som matchar *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Partiella utdata:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Om du vill distribuera en virtuell dator med hjälp av en viss bild, anteckna värdet i den *Urn* kolumn. När du anger bilden, kan det bild versionsnumret ersättas med ”senaste”, som väljs den senaste versionen av distributionen. I detta exempel på `--image` argument används för att ange den senaste versionen av en CentOS 6.5-bild.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Förstå VM-storlekar

Storlek på en virtuell dator bestämmer hur mycket av beräkningsresurser som Processorn och GPU-minne som är tillgängliga för den virtuella datorn. Virtuella datorer måste vara korrekt storlek för den förväntade belastningen. Om belastningen ökar, kan en befintlig virtuell dator ändras.

### <a name="vm-sizes"></a>VM-storlekar

I följande tabell kategoriserar storlekar i användningsfall.  

| Typ                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generellt syfte](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0 7| Belastningsutjämnade CPU-till-minne. Idealiskt för dev / test och små till medelstora lösningar för program och data.  |
| [Beräkningsoptimerad](sizes-compute.md)   | FS, F             | Hög CPU-till-minne. Bra för medelhög trafik program, nätverksinstallationer och batchprocesser.        |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Hög minne-till-core. Perfekt för relationsdatabaser, medelstora till stora cacheminnen och analyser i minnet.                 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)      | Ls                | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| [GPU](sizes-gpu.md)          | NV NC            | Särskilda virtuella datorer som mål för tunga grafisk återgivning och redigering av video.       |
| [Hög prestanda](sizes-hpc.md) | H, A8-11          | Våra mest kraftfulla CPU virtuella datorer med valfritt hög genomströmning nätverksgränssnitt (RDMA). 


### <a name="find-available-vm-sizes"></a>Hitta tillgängliga storlekar på VM

Om du vill se en lista över storlekar på VM tillgängliga i en viss region, Använd den [az lista-storlekar på vm](/cli/azure/vm#list-sizes) kommando. 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Partiella utdata:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Skapa virtuell dator med specifika storlek

I föregående VM skapa exempel har en storlek inte angetts, vilket resulterar i en standardstorlek. En VM-storlek kan väljas i Skapa en gång med hjälp av [az vm skapa](/cli/azure/vm#create) och `--size` argumentet. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator

När en virtuell dator har distribuerats, kan den ändras för att öka eller minska resursallokering. Du kan visa aktuellt av storleken på en virtuell dator med [az vm visa](/cli/azure/vm#show):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Kontrollera om önskad storlek är tillgängligt på den aktuella Azure klustret innan du ändrar storlek på en virtuell dator. Den [az vm-vm-storlek-alternativ för](/cli/azure/vm#list-vm-resize-options) kommando returnerar listan över storlekar. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Om önskad storlek är tillgänglig, kan den virtuella datorn ändras från ett slås på tillstånd, men den startas under åtgärden. Använd den [az vm ändra storlek på]( /cli/azure/vm#resize) kommando för att utföra storlek.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Om önskad storlek inte är i det aktuella klustret måste den virtuella datorn frigörs innan åtgärden Ändra storlek kan ske. Använd den [az vm frigöra]( /cli/azure/vm#deallocate) kommando för att stoppa och ta bort den virtuella datorn. Observera, när den virtuella datorn är påslagen tillbaka, några data på disken för temporär kan tas bort. Den offentliga IP-adressen ändras även om inte en statisk IP-adress används. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

När frigjorts, kan det uppstå storlek. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Efter storleksändringen kan du starta den virtuella datorn.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Energisparfunktioner för VM

En virtuell Azure-dator kan ha en av många energisparfunktioner. Det här tillståndet representerar det aktuella tillståndet för den virtuella datorn för hypervisor-programmet. 

### <a name="power-states"></a>Energisparfunktioner

| Energisparläge | Beskrivning
|----|----|
| Startar | Anger den virtuella datorn startas. |
| Körs | Anger att den virtuella datorn körs. |
| Stoppas | Anger att den virtuella datorn har stoppats. | 
| Stoppad | Anger att den virtuella datorn har stoppats. Virtuella datorer i ett stoppat tillstånd fortfarande avgifter beräkning.  |
| Det har frigjorts | Anger att den virtuella datorn har flyttats. |
| Frigöra | Anger att den virtuella datorn tas bort från hypervisor-programmet men fortfarande tillgängliga i kontrollplan. Virtuella datorer med tillståndet Deallocated inte avgifter beräkning. |
| - | Anger att energisparläge för den virtuella datorn är okänt. |

### <a name="find-power-state"></a>Hitta energiläge

Använd för att hämta tillståndet för en viss virtuell dator i [az vm hämta Instansvy](/cli/azure/vm#get-instance-view) kommando. Se till att ange ett giltigt namn för en virtuell dator och resursgruppen. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Resultat:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Administrativa uppgifter

Under livscykeln för en virtuell dator, kan du vill köra hanteringsuppgifter, till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa skript för att automatisera repetitiva och komplicerade uppgifter. Använda Azure CLI kan många vanliga administrativa uppgifter köras från kommandoraden eller i skript. 

### <a name="get-ip-address"></a>Hämta IP-adress

Det här kommandot returnerar privata och offentliga IP-adresser för en virtuell dator.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Stoppa den virtuella datorn

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Starta den virtuella datorn

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Ta bort resursgrupp

En resursgrupp också tar du bort alla resurser som ingår i, till exempel VM, virtuella nätverk och disk. Den `--no-wait` returnerar parametern kontroll på uppmaningen utan att vänta på att slutföra åtgärden. Den `--yes` parametern bekräftar att du vill ta bort resurser utan en ytterligare behörighet att göra detta.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om grundläggande VM skapande och hantering, till exempel hur du:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välj och Använd VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuell dator

Gå vidare till nästa kurs vill veta mer om Virtuella diskar.  

> [!div class="nextstepaction"]
> [Skapa och hantera Virtuella diskar](./tutorial-manage-disks.md)
