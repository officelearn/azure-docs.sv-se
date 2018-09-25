---
title: Skapa en Azure-dator med Accelererat nätverk | Microsoft Docs
description: Lär dig hur du skapar en Linux-dator med Accelererat nätverk aktiverat.
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
ms.date: 01/02/2018
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: b6aaf98ca3b5581691b6c70783be5250b506056c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990968"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Skapa en Linux-dator med Accelererat nätverk

I den här självstudien får du lära dig hur du skapar en Linux-dator (VM) med Accelererat nätverk. Om du vill skapa en virtuell Windows-dator med Accelererat nätverk [skapa en virtuell Windows-dator med Accelererat nätverk](create-vm-accelerated-networking-powershell.md). Accelererat nätverk gör det möjligt för enskild rot i/o-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden nätverkets prestanda. Den här sökvägen för högpresterande kringgår värden från datapath, vilket minskar latens och jitter CPU-belastningen för användning med de mest krävande arbetsbelastningarna på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan accelererat nätverk:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Alla nätverkstrafiken och från den virtuella datorn måste passera värden och den virtuella växeln utan accelererat nätverk. Den virtuella växeln tillhandahåller alla tvingande, till exempel nätverkssäkerhetsgrupper, åtkomstkontrollistor, isolering och andra virtualiserade nätverkstjänster nätverkstrafik. Mer information om virtuella växlar den [Hyper-V-nätverksvirtualisering och virtuella växeln](https://technet.microsoft.com/library/jj945275.aspx) artikeln.

Med accelererat nätverk trafik anländer till den Virtuella datorns nätverksgränssnitt (NIC) och sedan vidarebefordras till den virtuella datorn. Alla nätverksprinciper som gäller för den virtuella växeln Avlastade nu och tillämpas i maskinvara. Tillämpa principen i maskinvara kan nätverkskort för att vidarebefordra trafik direkt till den virtuella datorn, vilket kringgår värden och den virtuella växeln, samtidigt som den principen som det tillämpas på värden.

Fördelarna med accelererat nätverk gäller endast för den virtuella datorn som den är aktiverad på. För bästa resultat är det perfekt för att aktivera den här funktionen på minst två virtuella datorer är anslutna till samma Azure Virtual Network (VNet). Vid kommunikation mellan virtuella nätverk eller ansluta lokala har den här funktionen minimal påverkan på Total svarstid.

## <a name="benefits"></a>Fördelar
* **Lägre latens / högre paket per sekund (pps):** ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas i den virtuella datorn.
* **Minskar jitter:** virtuella växeln bearbetning beror på mängden principinformation som måste installeras och arbetsbelastningen processorkraft som klarar bearbetningen. Avlastning av principtillämpning till maskinvara tar bort den variationen genom att tillhandahålla paket direkt till den virtuella datorn, ta bort värden till VM-kommunikation och all programvara avbrott och kontext växlar.
* **Minskar CPU-användning:** kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure-galleriet: 
* **Ubuntu 16.04** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian ”utvidga” med backportar kernel**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Begränsningar och villkor

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererat nätverk stöds i de flesta generella och beräkningsoptimerade instansstorlekar med minst 2 virtuella processorer.  Dessa alternativ som stöds är: D/DSv2 och F/Fs

På-instanser som har stöd för hypertrådning stöds Accelererat nätverk för VM-instanser med 4 eller fler virtuella processorer. Stöds-serier är: D/DSv3, E/ESv3, Fsv2 och Ms-/ Mms.

Läs mer på VM-instanser, [Linux VM-storlekar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regioner
Tillgängligt i alla offentliga Azure-regioner samt Azure Government-moln.

### <a name="network-interface-creation"></a>Skapa en Network interface 
Accelererat nätverk kan bara aktiveras för ett nytt nätverkskort. Det går inte att aktivera för ett befintligt nätverkskort.
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivering av Accelererat nätverk på en aktiv virtuell dator
En VM-storlek som stöds utan accelererat nätverk aktiverat kan bara ha funktionen aktiverad när den stoppas och frigörs.  
### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassiska) kan inte distribueras med Accelererat nätverk.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Skapa en virtuell Linux-dator med Azure Accelererat nätverk

Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererat nätverk med hjälp av Azure CLI, du kan också [skapa en virtuell dator med accelererat nätverk med Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen under **inställningar**väljer **aktiverad**under **Accelerated networking**. Möjlighet att aktivera accelererat nätverk inte visas i portalen om du inte har valt en [operativsystem som stöds](#supported-operating-systems) och [VM-storlek](#supported-vm-instances). När den virtuella datorn skapas, måste du slutföra anvisningarna i [bekräfta att accelererat nätverk är aktiverat](#confirm-that-accelerated-networking-is-enabled).

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Installera senast [Azure CLI](/cli/azure/install-azure-cli) och logga in på Azure med hjälp av [az-inloggning](/cli/azure/reference-index#az_login). I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen ingår *myResourceGroup*, *myNic*, och *myVm*.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *centralus* plats:

```azurecli
az group create --name myResourceGroup --location centralus
```

Välj en region som stöds av Linux som anges i [Linux nätverksaccelerering](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med ett undernät:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp med [az network nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Nätverkssäkerhetsgruppen innehåller flera standardregler, varav inaktiverar all inkommande åtkomst från Internet. Öppna en port för att tillåta SSH-åtkomst till den virtuella datorn med [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverksgränssnitt med accelererat nätverk

Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). En offentlig IP-adress krävs inte om du inte planerar att få åtkomst till den virtuella datorn från Internet, men för att slutföra stegen i den här artikeln, det krävs.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Skapa ett nätverksgränssnitt med [az network nic skapa](/cli/azure/network/nic#az_network_nic_create) med aktiverat accelererat nätverk. I följande exempel skapas ett nätverksgränssnitt med namnet *myNic* i den *mySubnet* undernät för den *myVnet* virtuellt nätverk och ingår i  *myNetworkSecurityGroup* nätverkssäkerhetsgrupp kopplad till nätverksgränssnittet:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Skapa en virtuell dator och kopplar nätverkskortet
När du skapar den virtuella datorn, anger du nätverkskortet du skapade med `--nics`. Välj en storlek och distribution som anges i [Linux nätverksaccelerering](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM* med UbuntuLTS-avbildningen och en storlek som stöder Accelerated Networking (*Standard_DS4_v2*):

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

En lista över alla storlekar för Virtuella datorer och egenskaper finns i [Linux VM-storlekar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Utdata som liknar följande Exempelutdata returneras när den virtuella datorn har skapats. Anteckna **publicIpAddress**. Den här adressen används för att få åtkomst till den virtuella datorn i efterföljande steg.

```azurecli
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

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt `<your-public-ip-address>` med offentliga IP-adress som tilldelats den virtuella dator du skapade och Ersätt *azureuser* om du har använt ett annat värde för `--admin-username` när du skapade den virtuella datorn.

```bash
ssh azureuser@<your-public-ip-address>
```

Från Bash-gränssnittet och ange `uname -r` och bekräfta att kernel-versionen är en av följande versioner eller senare:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Bekräfta Mellanox VF-enheten visas den virtuella datorn med den `lspci` kommando. Den returnerade utdatan liknar följande utdata:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sök efter aktivitet på VF (virtual function) med den `ethtool -S eth0 | grep vf_` kommando. Om du får utdata liknar följande exempel utdata, snabbare nätverksfunktioner är aktiverad och fungerar.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Accelererat nätverk har nu aktiverats för den virtuella datorn.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera Accelererat nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan Accelererat nätverk, är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste ha stöd för Accelererat nätverk genom att uppfylla följande krav som också beskrivs ovan:

* Den virtuella datorn måste vara en storlek som stöds för Accelererat nätverk
* Den virtuella datorn måste vara stöds Azure Gallery image (och kernel-version för Linux)
* Alla virtuella datorer i en tillgänglighetsuppsättning eller VMSS måste vara stoppad/frigjord innan du aktiverar Accelererat nätverk på en NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>Ange enskilda virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning
Först stoppa/frigör den virtuella datorn eller om en Tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Viktigt,. Observera att om den virtuella datorn skapades individuellt, utan en tillgänglighetsuppsättning du bara behöva stoppa/frigör den enskilda virtuella datorn om du vill aktivera Accelererat nätverk.  Om den virtuella datorn skapades med en tillgänglighetsuppsättning måste alla virtuella datorer i tillgänglighetsuppsättningen är stoppad/frigjord innan du aktiverar Accelererat nätverk på något av nätverkskorten. 

Stoppat, aktivera Accelererat nätverk på nätverkskortet för den virtuella datorn:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Starta om virtuell dator eller, om i en Tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen och bekräfta att Accelererat nätverk är aktiverat: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS skiljer sig men följer samma arbetsflöde.  Stoppa först de virtuella datorerna:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

När de virtuella datorerna har stoppats, uppdatera egenskapen Accelererat nätverk under nätverksgränssnittet:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Observera att en VMSS har du VM-uppgraderingar som gäller uppdateringar med hjälp av tre olika inställningar, automatiskt, löpande och manuellt.  I dessa anvisningar är principen inställd på automatisk så att VMSS tar över ändringarna direkt efter omstart.  Ange den till automatisk så att ändringarna omedelbart plockas upp: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Slutligen ska du starta om VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

När du startar om, vänta på att uppgraderingen ska slutföras, men när det har slutförts, VF visas inuti den virtuella datorn.  (Kontrollera att du använder en OS- och VM-storlek som stöds.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med Accelererat nätverk

Virtuella datorer med Accelererat nätverk aktiverat kan bara ändras till virtuella datorer som har stöd för Accelererat nätverk.  

En virtuell dator med Accelererat nätverk aktiverat kan inte ändras till en VM-instans som inte har stöd för Accelererat nätverk med hjälp av storleksändring-åtgärd.  I stället att ändra storlek på en av dessa virtuella datorer: 

* Stoppa/frigöra den virtuella datorn eller om du är i tillgänglighet set/VMSS, stoppa/Frigör alla virtuella datorer i uppsättningen/VMSS.
* Accelererat nätverk måste inaktiveras på nätverkskortet för den virtuella datorn eller om de finns i en tillgänglighet set/VMSS, alla virtuella datorer i uppsättningen/VMSS.
* VM-tillgänglighet set/VMSS kan flyttas till en ny storlek som inte har stöd för Accelererat nätverk och startas om när Accelererat nätverk är inaktiverat.  

