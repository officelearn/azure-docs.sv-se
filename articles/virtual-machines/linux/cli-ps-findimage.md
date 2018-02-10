---
title: "Välj Linux VM-avbildningar med Azure CLI | Microsoft Docs"
description: "Lär dig hur du använder Azure CLI för att fastställa utgivare, erbjudande, SKU och version för Marketplace VM-avbildningar."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79eb69b83e4ffc0a4ad7c2631ce4d1306a1e335c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Hur du hittar Linux VM avbildningar i Azure Marketplace med Azure CLI
Det här avsnittet beskriver hur du använder Azure CLI 2.0 för att hitta VM-avbildningar i Azure Marketplace. Använd informationen för att ange en Marketplace-avbildning när du skapar en Linux VM.

Kontrollera att du har installerat senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och är inloggad på ett Azure-konto (`az login`).

## <a name="terminology"></a>Terminologi

Marketplace-bilder identifieras i CLI och andra Azure-verktyg enligt en hierarki:

* **Publisher** -organisationen som skapade avbildningen. Exempel: kanoniska
* **Erbjuder** – en grupp av relaterade bilder som har skapats av en utgivare. Exempel: Ubuntu Server
* **SKU** - en instans av ett erbjudande, till exempel en högre version av en distributionsplats. Exempel: 16.04-LTS
* **Version** -versionsnumret för en bild SKU. När du anger avbildningen, kan du ersätta versionsnumret med ”senaste”, som väljs den senaste versionen av distributionen.

Om du vill ange en Marketplace-avbildning, använder du vanligtvis bilden *URN*. URN kombinerar dessa värden, avgränsade med kolon (:): *Publisher*:*erbjuder*:*Sku*:*Version*. 


## <a name="list-popular-images"></a>Lista över populära bilder

Kör den [az vm bildlista](/cli/azure/vm/image#az_vm_image_list) kommandot, utan de `--all` alternativet om du vill se en lista över populära VM-avbildningar i Azure Marketplace. Till exempel köra följande kommando för att visa en cachelagrad lista över populära bilder i tabellformat:

```azurecli
az vm image list --output table
```

Utdata innehåller URN (värdet i den *Urn* kolumn), där du anger du vilken avbildning. När du skapar en virtuell dator med någon av dessa populära Marketplace-avbildningar, kan du alternativt ange URN-alias som *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Söka efter specifika avbildningar

Använd för att hitta en specifik VM-avbildning i Marketplace den `az vm image list` kommandot med de `--all` alternativet. Den här versionen av kommandot tar en stund att slutföra och kan returnera långa utdata så att du vanligtvis filtrera listan efter `--publisher` eller en annan parameter. 

Till exempel följande kommando visar alla Debian erbjudanden (Kom ihåg att utan den `--all` växla, söks endast den lokala cachen gemensamma nodbilder):

```azurecli
az vm image list --offer Debian --all --output table 

```

Partiella utdata: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Liknande filter med det `--location`, `--publisher`, och `--sku` alternativ. Du kan även utföra delmatchningar på ett filter, till exempel söker efter `--offer Deb` att hitta alla Debian bilder.

Om du inte anger en viss plats med den `--location` alternativ, värdena för `westus` returneras som standard. (Ange en annan standardsökväg genom att köra `az configure --defaults location=<location>`.)

Till exempel följande kommando visar alla Debian 8 SKU: er i `westeurope`:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Partiella utdata:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Navigera avbildningar 
Ett annat sätt att hitta en bild på en plats är att köra den [az vm avbildningen lista-utgivare](/cli/azure/vm/image#az_vm_image_list_publishers), [az vm avbildningen lista-erbjudanden](/cli/azure/vm/image#az_vm_image_list_offers), och [az vm avbildningen lista-SKU: er](/cli/azure/vm/image#az_vm_image_list_skus) kommandon i sekvens. Med dessa kommandon bestämma dessa värden:

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.


Till exempel visar följande kommando bilden utgivare i USA, västra plats:

```azurecli
az vm image list-publishers --location westus --output table
```

Partiella utdata:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Använd den här informationen kan hitta från en viss utgivare. Till exempel om Canonical är en avbildningens utgivare i USA, västra platsen kan hitta sina erbjudanden genom att köra `azure vm image list-offers`. Ange platsen och utgivare som i följande exempel:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Resultat:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Du ser att i regionen USA, västra Canonical publicerar den **UbuntuServer** erbjudande på Azure. Men vad finns det för SKU:er? För att få dessa värden, köra `azure vm image list-skus` och ange den plats och utgivare som du har identifierat:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Resultat:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Använd slutligen den `az vm image list` kommando som söker efter en viss version av SKU: N som du vill **16.04 LTS**:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Resultat:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```
## <a name="next-steps"></a>Nästa steg
Du kan nu välja exakt den avbildning som du vill använda genom att anteckna värdet för URN. Skicka detta värde med den `--image` parameter när du skapar en virtuell dator med den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. Kom ihåg att du kan ersätta versionsnumret i URN med ”senaste”. Den här versionen är alltid den senaste versionen av distributionen. För att snabbt skapa en virtuell dator med hjälp av URN information, se [skapa och hantera virtuella Linux-datorer med Azure CLI](tutorial-manage-vm.md).
