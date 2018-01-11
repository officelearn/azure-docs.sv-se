---
title: "Skapa en virtuell Azure-dator med snabbare nätverk | Microsoft Docs"
description: "Lär dig hur du skapar en virtuell Linux-dator med snabbare nätverk."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 908d81c363a556917d211e0bcc92188f849fb690
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Skapa en virtuell Linux-dator med snabbare nätverk

> [!IMPORTANT] 
> Virtuella datorer måste skapas med snabbare nätverk aktiverad. Den här funktionen kan inte aktiveras på befintliga virtuella datorer. Du kan följa stegen nedan för att aktivera snabbare nätverk:
>   1. Ta bort den virtuella datorn.
>   2. Återskapa den virtuella datorn med snabbare nätverk aktiverad.
>

I kursen får du lära dig hur du skapar en Linux-dator (VM) med snabbare nätverk. Snabbare nätverksfunktioner kan single-root I/O virtualization (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden dess nätverksprestanda. Den här sökvägen för högpresterande kringgår värden från datapath, vilket minskar latens och jitter CPU-belastningen för användning med de mest krävande nätverksbelastning på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan snabbare nätverksfunktioner:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan snabbare nätverk måste alla nätverkstrafiken till och från den virtuella datorn passerar värden och den virtuella växeln. Den virtuella växeln tillhandahåller alla tvingande, till exempel nätverkssäkerhetsgrupper, åtkomstkontrollistor, isolering och andra virtualiserade nätverkstjänster för nätverkstrafik. Mer information om virtuella växlar på [Hyper-V-nätverksvirtualisering och den virtuella växeln](https://technet.microsoft.com/library/jj945275.aspx) artikel.

Med snabbare nätverksfunktioner trafik anländer till den Virtuella datorns nätverksgränssnitt (NIC) och sedan vidarebefordras till den virtuella datorn. Alla principer för nätverk som gäller för den virtuella växeln avläst nu och tillämpas i maskinvara. Tillämpa principen i den maskinvara som gör det möjligt för nätverkskort för att vidarebefordra trafik direkt till den virtuella datorn, vilket kringgår värden och den virtuella växeln samtidigt som den principen tillämpades på värden.

Fördelarna med snabbare nätverksfunktioner gäller endast för den virtuella datorn som den är aktiverad på. Det är perfekt att aktivera funktionen på minst två virtuella datorer anslutna till samma Azure virtuella nätverk (VNet) för bästa resultat. Vid kommunikation över Vnet eller anslutande lokalt har funktionen minimal inverkan på övergripande svarstiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens / högre paket per sekund (pps):** att ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas inuti den virtuella datorn.
* **Minskar jitter:** virtuella växeln bearbetning beror på mängden principinformation som måste tillämpas och arbetsbelastningen processorkraft som utför bearbetning. Avlastning tvingande principer till maskinvaran som tar bort den variationen genom att leverera paket direkt till den virtuella datorn, ta bort värden till programvara avbrott och kontext växlar för VM-kommunikation och alla.
* **Minskas CPU-användning:** kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

## <a name="supported-operating-systems"></a>Operativsystem som stöds
* **Ubuntu 16.04**: 4.11.0-1013 eller större kernel-version
* **SLES SP3**: 4.4.92-6.18 eller större kernel-version
* **RHEL**: 7.4.2017120423 eller större kernel-version
* **CentOS**: 7.4.20171206 eller större kernel-version

## <a name="supported-vm-instances"></a>VM-instanser som stöds
Snabbare nätverksfunktioner stöds på mest generella och beräknings-optimerad instans storlekar med 4 eller fler vCPUs. På instanser, till exempel D/DSv3 eller E/ESv3 som stöder hypertrådar stöds snabbare nätverk för VM-instanser med 8 eller flera vCPUs.  Stöds serien är: D/DSv2, D/DSv3, E/ESv3, Fsv2-F/Fs och Ms-/ Mms. 

Mer information om VM-instanser finns [Linux VM-storlekar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regioner
Tillgänglig i alla offentliga Azure-regioner med undantag för Östasien.   Azure Government-molnet stöds inte ännu.

## <a name="limitations"></a>Begränsningar
Följande begränsningar gäller när du använder den här funktionen:

* **Network interface skapa:** Accelerated nätverk kan bara aktiveras för en ny nätverkskort. Det går inte att aktivera för en befintlig nätverkskort.
* **Skapa en virtuell dator:** A nätverkskortet med snabbare nätverksfunktioner som är aktiverad kan endast kopplas till en virtuell dator när den virtuella datorn skapas. Nätverkskortet kan inte kopplas till en befintlig virtuell dator. Om du lägger till den virtuella datorn i en befintlig tillgänglighetsuppsättning måste alla virtuella datorer i tillgänglighetsuppsättningen också ha snabbare nätverk som är aktiverad.
* **Distribution via Azure Resource Manager:** virtuella datorer (klassisk) kan inte distribueras med snabbare nätverk.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Installera senaste [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/#login). Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår *myResourceGroup*, *myNic*, och *myVm*.

Skapa en resursgrupp med [az group create](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *centralus* plats:

```azurecli
az group create --name myResourceGroup --location centralus
```

Du måste välja en stöds Linux-region som anges i [Linux snabbare nätverk](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Skapa ett virtuellt nätverk med [az network vnet skapa](/cli/azure/network/vnet#create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med ett undernät:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp med [az nätverket nsg skapa](/cli/azure/network/nsg#create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Nätverkssäkerhetsgruppen innehåller flera standardregler, varav inaktiverar all inkommande åtkomst från Internet. Öppna en port så att SSH-åtkomst till den virtuella datorn med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

## <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverksgränssnitt med snabbare nätverksfunktioner

Skapa en offentlig IP-adress med [az nätverket offentliga IP-skapa](/cli/azure/network/public-ip#az_network_public_ip_create). En offentlig IP-adress krävs inte om du inte åtkomst till den virtuella datorn från Internet, men för att slutföra stegen i den här artikeln, det är obligatoriskt.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Skapa ett nätverksgränssnitt med [az nätverket nic skapa](/cli/azure/network/nic#create) med snabbare nätverksfunktioner som är aktiverad. I följande exempel skapas ett nätverksgränssnitt med namnet *myNic* i den *mySubnet* undernätet för den *myVnet* virtuella nätverk och associerar den  *myNetworkSecurityGroup* nätverkssäkerhetsgruppen för nätverksgränssnittet:

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

## <a name="create-a-vm-and-attach-the-nic"></a>Skapa en virtuell dator och koppla nätverkskortet
När du skapar den virtuella datorn, ange NIC du skapat med `--nics`. Du måste välja en storlek och distribution som anges i [Linux snabbare nätverk](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Skapa en virtuell dator med [az vm create](/cli/azure/vm#create). I följande exempel skapas en virtuell dator med namnet *myVM* med UbuntuLTS avbildningen och storlek som har stöd för snabbare nätverk (*Standard_DS4_v2*):

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

En lista över alla VM-storlekar och egenskaper, se [Linux VM-storlekar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

När den virtuella datorn skapas returneras utdata som liknar följande exempel utdata. Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn i efterföljande steg.

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

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Kontrollera att snabbare nätverk har aktiverats

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt `<your-public-ip-address>` med offentliga IP-adress som tilldelats den virtuella datorn du skapade och Ersätt *azureuser* om du har använt ett annat värde för `--admin-username` när du skapade den virtuella datorn.

```bash
ssh azureuser@<your-public-ip-address>
```

I gränssnittet Bash ange `uname -r` och bekräfta att kernel-version är en av följande versioner eller senare:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Bekräfta Mellanox VF enheten exponeras för den virtuella datorn med den `lspci` kommando. Returnerade utdata liknar följande utdata:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sök efter aktivitet på VF (virtual function) med den `ethtool -S eth0 | grep vf_` kommando. Om du får utdata liknar följande exempel utdata snabbare nätverk är aktiverad och fungerar.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Snabbare nätverksfunktioner har nu aktiverats för den virtuella datorn.
