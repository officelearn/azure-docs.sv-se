---
title: Skapa en virtuell Azure-dator med accelererade nätverk med Azure CLI
description: Lär dig hur du skapar en virtuell Linux-dator med accelerated networking aktiverat.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 05f8430efa31b39d49025fb8456108da229d3d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239816"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Skapa en virtuell Linux-dator med accelererat nätverk med Azure CLI

I den här självstudien får du lära dig hur du skapar en virtuell Linux-dator (VM) med accelererat nätverkande. En dator för Windows med accelererat nätverk finns i [Skapa en virtuell Windows-dator med accelererat nätverk.](create-vm-accelerated-networking-powershell.md) Accelererad nätverk möjliggör enkel rot-I/O-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt förbättrar dess nätverksprestanda. Den här banan med hög prestanda kringgår värden från datasökvägen, vilket minskar svarstiden, jitter- och CPU-användningen, för användning med de mest krävande nätverksarbetsbelastningarna på vm-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan accelererat nätverkande:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan accelererade nätverk måste all nätverkstrafik in och ut ur den virtuella datorn passera värden och den virtuella växeln. Den virtuella växeln innehåller alla principefterlevnad, till exempel nätverkssäkerhetsgrupper, åtkomstkontrolllistor, isolering och andra virtuella nätverkstjänster till nätverkstrafik. Mer information om virtuella växlar finns i [hyper-v-nätverksvirtualisering och](https://technet.microsoft.com/library/jj945275.aspx) virtuell växelartikel.

Med accelererad nätverksanslutning anländer nätverkstrafik till den virtuella datorns nätverksgränssnitt (NIC) och vidarebefordras sedan till den virtuella datorn. Alla nätverksprinciper som den virtuella växeln gäller är nu avinläst och tillämpas i maskinvara. Genom att tillämpa principen i maskinvaran kan nätverkskortet vidarebefordra nätverkstrafik direkt till den virtuella datorn, förbi värden och den virtuella växeln, samtidigt som alla principer som tillämpas i värden bibehålls.

Fördelarna med accelererade nätverk gäller endast för den virtuella datorn som den är aktiverad på. För bästa resultat är det idealiskt att aktivera den här funktionen på minst två virtuella datorer som är anslutna till samma virtuella Azure-nätverk (VNet). När du kommunicerar över virtuella nätverk eller ansluter lokalt har den här funktionen minimal inverkan på den totala svarstiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens / Högre paket per sekund (pps):** Om du tar bort den virtuella växeln från datasökvägen tas tidspaketen i värden för principbearbetning bort och antalet paket som kan bearbetas inuti den virtuella datorn ökar.
* **Minskad jitter:** Virtuell växelbearbetning beror på hur mycket princip som måste tillämpas och arbetsbelastningen för processorn som utför bearbetningen. Avlastning av principövernsättningen till maskinvaran tar bort den variabiliteten genom att leverera paket direkt till den virtuella datorn, ta bort värden till VM-kommunikation och alla programvaruavbrott och kontextväxlar.
* **Minskad CPU-användning:** Att kringgå den virtuella växeln i värden leder till mindre CPU-användning för bearbetning av nätverkstrafik.

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure Gallery: 
* **Ubuntu 14.04 med linux-azure-kärnan**
* **Ubuntu 16,04 eller senare** 
* **SLES12 SP3 eller senare** 
* **RHEL 7.4 eller senare**
* **CentOS 7.4 eller senare**
* **CoreOS Linux**
* **Debian "Stretch" med backports kärna**
* **Oracle Linux 7.4 och senare med Red Hat Kompatibel Kernel (RHCK)**
* **Oracle Linux 7.5 och senare med UEK version 5**
* **FreeBSD 10,4, 11,1 & 12,0**

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererad nätverk stöds på de flesta allmänna ändamål och beräkningsoptimerade instansstorlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/Fs

På instanser som stöder hypertrådning stöds accelererad nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien stöds är: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms och Ms/Mmsv2.

Mer information om VM-instanser finns i [Storlekar på virtuella linux-datorer](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Anpassade avbildningar
Om du använder en anpassad avbildning och avbildningen stöder Accelerated Networking, se till att ha de drivrutiner som krävs för att arbeta med Mellanox ConnectX-3 och ConnectX-4 Lx-nätverkskort på Azure.

### <a name="regions"></a>Regioner
Tillgänglig i alla offentliga Azure-regioner samt Azure Government Clouds.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivera accelererat nätverk på en virtuell dator som körs
En vm-storlek som stöds utan att accelererade nätverk är aktiverat kan bara ha funktionen aktiverad när den stoppas och frigörs.  
### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassiska) kan inte distribueras med accelerated networking.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Skapa en virtuell Linux-dator med Azure Accelerated Networking
## <a name="portal-creation"></a>Skapa portal
Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererade nätverk med Hjälp av Azure CLI, kan du också [skapa en virtuell dator med accelererade nätverk med Hjälp av Azure-portalen](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen väljer du fliken **Nätverk** i bladet **Skapa en virtuell dator.**  På den här fliken finns det ett alternativ för **Accelererat nätverk .**  Om du har valt ett operativsystem och [vm-storlek](#supported-vm-instances) [som stöds](#supported-operating-systems) fylls det här alternativet automatiskt i till "På".  Om inte, kommer det att fylla "Off" alternativet för accelererade nätverk och ge användaren en anledning till varför det inte är aktiverat.   

* *Anm.:* Endast operativsystem som stöds kan aktiveras via portalen.  Om du använder en anpassad avbildning och avbildningen stöder Accelerated Networking skapar du den virtuella datorn med CLI eller PowerShell. 

När den virtuella datorn har skapats kan du bekräfta att Accelererat nätverk är aktiverat genom att följa instruktionerna i [bekräfta att accelererade nätverk är aktiverat](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>CLI skapande
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Installera den senaste [Azure CLI](/cli/azure/install-azure-cli) och logga in på ett Azure-konto med [az login](/cli/azure/reference-index). I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderade *myResourceGroup*, *myNic*och *myVm*.

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *centralus-platsen:*

```azurecli
az group create --name myResourceGroup --location centralus
```

Välj en Linux-region som stöds i [Linux-accelererade nätverk](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med ett undernät:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Nätverkssäkerhetsgruppen innehåller flera standardregler, varav en inaktiverar all inkommande åtkomst från Internet. Öppna en port för att tillåta SSH-åtkomst till den virtuella datorn med [az-nätverks nsg-regel skapa:](/cli/azure/network/nsg/rule)

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverksgränssnitt med accelererade nätverk

Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). En offentlig IP-adress krävs inte om du inte planerar att komma åt den virtuella datorn från Internet, men för att slutföra stegen i den här artikeln krävs det.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Skapa ett nätverksgränssnitt med [az-nätverksnic create](/cli/azure/network/nic) med accelererade nätverk aktiverade. I följande exempel skapas ett nätverksgränssnitt med namnet *myNic* i *mittSubnets* undernät i *myVnets* virtuella nätverk och associerar nätverkssäkerhetsgruppen *myNetworkSecurityGroup* till nätverksgränssnittet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Skapa en virtuell dator och koppla nätverkskortet
När du skapar den virtuella datorn anger `--nics`du det nätverkskort som du skapade med . Välj en storlek och distribution som anges i [Linux accelererade nätverk](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM* med UbuntuLTS-avbildningen och en storlek som stöder accelererat nätverk *(Standard_DS4_v2):*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

En lista över alla vm-storlekar och egenskaper finns i [Storlekar på virtuella datorer i Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

När den virtuella datorn har skapats returneras utdata som liknar följande exempelutdata. Anteckna **publicIpAddress**. Den här adressen används för att komma åt den virtuella datorn i efterföljande steg.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Bekräfta att accelererat nätverk är aktiverat

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt `<your-public-ip-address>` med den offentliga IP-adress som tilldelats den virtuella datorn som du `--admin-username` skapade och ersätt *azureuser* om du använde ett annat värde för när du skapade den virtuella datorn.

```bash
ssh azureuser@<your-public-ip-address>
```

Från Bash-skalet `uname -r` anger och bekräftar du att kärnversionen är en av följande versioner eller senare:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Bekräfta att Mellanox VF-enheten är `lspci` exponerad för den virtuella datorn med kommandot. Den returnerade utdata liknar följande utdata:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sök efter aktivitet på VF (virtuell `ethtool -S eth0 | grep vf_` funktion) med kommandot. Om du får utdata som liknar följande exempelutdata aktiveras och arbete med accelererade nätverk.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Accelererad nätverk är nu aktiverat för din virtuella dator.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Hantera dynamisk bindning och återkallande av virtuell funktion 
Program måste köras över det syntetiska nätverkskortet som visas i vm. Om programmet körs direkt över VF-nätverkskortet tar det inte emot **alla** paket som är avsedda för den virtuella datorn, eftersom vissa paket visas över det syntetiska gränssnittet.
Om du kör ett program över det syntetiska nätverkskortet garanterar det att programmet tar emot **alla** paket som är avsedda för det. Det säkerställer också att programmet fortsätter att köras, även om VF återkallas när värden servas. Ansökningar som är bindande för det syntetiska nätverkskortet är ett **obligatoriskt** krav för alla tillämpningar som utnyttjar **Accelerated Networking**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera accelererat nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan accelererat nätverk är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste stödja accelererade nätverk genom att uppfylla följande förutsättningar som också beskrivs ovan:

* Den virtuella datorn måste vara en storlek som stöds för accelererade nätverk
* Den virtuella datorn måste vara en Azure Gallery-avbildning som stöds (och kärnversion för Linux)
* Alla virtuella datorer i en tillgänglighetsuppsättning eller VMSS måste stoppas/frigöras innan accelererad nätverk aktiveras på alla nätverkskort

### <a name="individual-vms--vms-in-an-availability-set"></a>Enskilda virtuella datorer & virtuella datorer i en tillgänglighetsuppsättning
Första stopp/deallocate den virtuella datorn eller, om en tillgänglighetsuppsättning, alla virtuella datorer i setet:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Viktigt, observera, om din virtuella dator skapades individuellt, utan en tillgänglighetsuppsättning, behöver du bara stoppa/deallocate den enskilda virtuella datorn för att aktivera accelererat nätverk.  Om den virtuella datorn skapades med en tillgänglighetsuppsättning måste alla virtuella datorer som ingår i tillgänglighetsuppsättningen stoppas/frigöras innan accelererade nätverk aktiveras på någon av nätverkskorten. 

Aktivera Accelererat nätverk på nätverkskortet för den virtuella datorn när det har stoppats:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Starta om den virtuella datorn eller, om alla virtuella datorer i uppsättningen är aktiverade i en tillgänglighetsuppsättning: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS är något annorlunda men följer samma arbetsflöde.  Stoppa först de virtuella datorerna:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

När de virtuella datorerna har stoppats uppdaterar du egenskapen Accelerated Networking under nätverksgränssnittet:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Observera att en VMSS har VM-uppgraderingar som tillämpar uppdateringar med tre olika inställningar, automatisk, rullande och manuell.  I dessa instruktioner är principen inställd på automatisk så att VMSS hämtar ändringarna omedelbart efter omstart.  Så här ställer du in den automatiskt så att ändringarna omedelbart plockas upp: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Starta slutligen om VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

När du startar om, vänta tills uppgraderingarna är klara men när de är klara visas VF inuti den virtuella datorn.  (Kontrollera att du använder en os- och vm-storlek som stöds.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med accelererat nätverk

Virtuella datorer med accelererat nätverk aktiverat kan endast ändra storlek på virtuella datorer som stöder accelererat nätverkande.  

En virtuell dator med aktiverat accelererat nätverk kan inte ändra storlek på en VM-instans som inte stöder accelererat nätverk med hjälp av storleksåtgärden.  Om du vill ändra storlek på en av dessa virtuella datorer: 

* Stoppa/frigöra den virtuella datorn eller om du i en tillgänglighetsuppsättning/VMSS stoppar/frigör alla virtuella datorer i set/VMSS.
* Accelererad nätverk måste inaktiveras på nätverkskortet för den virtuella datorn eller om alla virtuella datorer i set/VMSS i en tillgänglighetsuppsättning/VMSS.
* När accelererade nätverk har inaktiverats kan VM/availability set/VMSS flyttas till en ny storlek som inte stöder accelererat nätverk och startas om.  

