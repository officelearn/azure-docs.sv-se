---
title: "Med hjälp av interna DNS för namnmatchning för virtuell dator i Azure | Microsoft Docs"
description: "Med hjälp av interna DNS för namnmatchning för virtuell dator på Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: bfba2cf38a0624e8480a32bf153f391d820da5a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Med hjälp av interna DNS för namnmatchning för virtuell dator på Azure

Den här artikeln visar hur du ställer statiska interna DNS-namn för Linux virtuella datorer med hjälp av virtuella nätverkskort (tillval) (VNic) och DNS-etikettnamn. Statisk DNS-namn används för permanenta infrastrukturtjänster som en Jenkins build-server, som används för det här dokumentet eller en Git-server.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-commands) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="quick-commands"></a>Snabbkommandon

Om du behöver utföra aktiviteten i följande avsnitt finns information de kommandon som krävs. Mer detaljerad information och kontext för varje steg finns resten av dokumentet [startar här](#detailed-walkthrough).  

Krav: Resursgrupp, VNet, NSG med SSH inkommande, undernät.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Skapa ett virtuellt nätverkskort med ett statiskt interna DNS-namn

Den `-r` cli-flaggan är för att ställa in DNS-etikett, vilket ger statisk DNS-namn för VNic.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Distribuera den virtuella datorn till VNet, NSG och ansluta VNic

Den `-N` ansluter VNic till den nya virtuella datorn under distributionen till Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

En fullständig kontinuerlig integrering och kontinuerlig distribution (CiCd) infrastrukturen i Azure kräver vissa servrar vara statiska eller långlivade servrar.  Du rekommenderas att Azure tillgångar som den virtuella nätverk (Vnet) och Nätverkssäkerhetsgrupper (NSG: er) måste vara statiska och resurser som distribueras sällan kvar längre.  När ett virtuellt nätverk har distribuerats, kan den återanvändas av nya distributioner utan någon negativ påverkar i infrastrukturen.  Lägga till i den här statiska nätverk en Git-lagringsplatsen server och en Jenkins automationsserver ger CiCd till utvecklings- och testmiljöer.  

Interna DNS-namn är bara lösas i Azure-nätverk.  Eftersom DNS-namn är internt, är de inte matchas till utanför internet, vilket ger ytterligare säkerhet i infrastrukturen.

_Ersätt alla exempel med egna namn._

## <a name="create-the-resource-group"></a>Skapa resursgruppen.

En resursgrupp behövs för att organisera allt vi skapa i den här genomgången.  Läs mer om Azure-resursgrupper [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Skapa VNet

Det första steget är att skapa ett virtuellt nätverk för att starta de virtuella datorerna till.  Det virtuella nätverket innehåller ett undernät för den här genomgången.  Mer information om virtuella Azure-nätverk finns [skapa ett virtuellt nätverk med hjälp av Azure CLI](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Skapa NSG: N

Undernätet bygger bakom en befintlig säkerhetsgrupp för nätverk, så vi skapa NSG: N innan undernätet.  Azure NSG: er är likvärdiga med en brandvägg på nätverksnivå.  Mer information om Azure NSG: er finns [hur du skapar NSG: er i Azure CLI](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Lägg till regel för att tillåta en inkommande SSH

Linux VM behöver åtkomst från internet, så det behövs en regel som tillåter inkommande port 22 trafik som skickas via nätverket till port 22 på Linux-VM.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Lägg till ett undernät i VNet

Virtuella datorer inom VNet måste finnas i ett undernät.  Varje virtuellt nätverk kan ha flera undernät.  Skapa undernätet och associera undernätet med NSG: N för att lägga till en brandvägg i undernätet.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Undernätet är nu läggs till i VNet och som är associerade med NSG: N och NSG-regler.

## <a name="creating-static-dns-names"></a>Skapa statiska DNS-namn

Azure är flexibla, men om du vill använda DNS-namn för namnmatchning för virtuella datorer, måste du skapa dem som virtuella nätverkskort (VNics) med hjälp av DNS-etiketter.  VNics är viktigt eftersom du kan återanvända dem genom att ansluta dem till olika virtuella datorer, så att VNic som en statisk resurs medan de virtuella datorerna kan vara tillfälligt.  Med hjälp av DNS-etiketter på VNic, kommer du att aktivera enkel namnmatchning från andra virtuella datorer i VNet.  Med matchas namn kan andra virtuella datorer att ansluta till automatiseringsservern med DNS-namn `Jenkins` eller Git-server som `gitrepo`.  Skapa ett virtuellt nätverkskort och associera det med undernät som skapats i föregående steg.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Distribuera den virtuella datorn till VNet och NSG

Nu har vi ett VNet, ett undernät i det virtuella nätverket och en NSG som fungerar som en brandvägg för att skydda våra undernät genom att blockera all inkommande trafik utom port 22 för SSH.  Den virtuella datorn kan nu distribueras i det här befintliga nätverksinfrastruktur.

Med hjälp av Azure CLI och `azure vm create` kommandot Linux VM har distribuerats till den befintliga Azure-resursgrupp, virtuella nätverk, undernät och virtuellt nätverkskort.  Mer information om med hjälp av CLI för att distribuera en fullständig VM finns [skapa en fullständig Linux-miljö med hjälp av Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Med hjälp av CLI-flaggor för att anropa befintliga resurser, ber vi Azure för att distribuera den virtuella datorn i det befintliga nätverket.  Om du vill upprepar när ett VNet och undernät som har distribuerats, kan de förbli statisk eller permanenta resurser i Azure-region.  

## <a name="next-steps"></a>Nästa steg
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-VM på Azure med hjälp av mallar](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
