---
title: Skapa en Linux-VM med hjälp av Azure CLI för klassiska | Microsoft Docs
description: Skapa en Linux-VM på Azure med hjälp av den klassiska Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: 569e90c7908ce435689a80f7917b20275703f537
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473747"
---
# <a name="create-a-linux-vm-using-the-azure-classic-cli"></a>Skapa en Linux-VM med hjälp av den klassiska Azure CLI

I den här artikeln lär du dig hur du snabbt distribuerar en virtuell Linux-dator (VM) i Azure med hjälp av kommandot `azure vm quick-create` i Azures kommandoradsgränssnitt (CLI). Kommandot `quick-create` distribuerar en virtuell dator inuti en grundläggande, säker infrastruktur som du kan använda som prototyp eller för att snabbt testa ett koncept.

> [!NOTE]
> För att skapa en virtuell dator med Azure CLI, se [skapa en virtuell dator med Azure CLI](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan också snabbt distribuera en virtuell Linux-dator med hjälp av [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Artikeln kräver ett [SSH offentliga och privata nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Snabbkommandon

I följande exempel visas hur du distribuerar en CoreOS VM och kopplar din SSH(Secure Shell)-nyckel (dina argument kan vara annorlunda):

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

Den här genomgången distribuerar en virtuell UbuntuLTS-dator, steg för steg, med förklaringar om vad varje steg gör.

## <a name="vm-quick-create-aliases"></a>VM-snabbregistrering av alias

Ett snabbt sätt att välja en distribution är att använda Azure CLI-alias som mappas till de vanligaste OS-distributionerna. I följande tabell visas tillgängliga alias (från och med Azure CLI version 0.10). Alla distributioner som använder `quick-create` virtuella datorer som backas upp av lagring på solid state-hårddisk (SSD), som ger snabbare etablering och högpresterande åtkomst till disken som standard. (Dessa alias representerar en bråkdel av de tillgängliga distributionerna i Azure. Du kan hitta fler bilder på Azure Marketplace genom att [söka efter en bild i PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [på internet](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) eller [ladda upp en egen anpassad bild](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | Utgivare | Erbjudande | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |senaste |
| CoreOS |CoreOS |CoreOS |Stable |senaste |
| Debian |credativ |Debian |8 |senaste |
| openSUSE |SUSE |openSUSE |13.2 |senaste |
| RHEL |Red Hat |RHEL |7.2 |senaste |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |senaste |

I de följande avsnitten används `UbuntuLTS` alias för **ImageURN**-alternativet (`-Q`) för att distribuera en Ubuntu 14.04.4 LTS Server.

Det tidigare `quick-create` exemplet anropade bara `-M` flaggan för att identifiera den offentliga SSH-nyckeln att överföra vid inaktivering av SSH-lösenord, så uppmanas du ange följande argument:

* resursgruppens namn (valfri sträng är vanligtvis bra för din första Azure-resursgrupp)
* VM-namn
* plats (`westus` eller `westeurope` är lämpliga standardvärden)
* linux (så att Azure vet vilka operativsystem du vill ha)
* användarnamn

Följande exempel anger alla värden så att ingen ytterligare uppmaning krävs. Så länge som du har en `~/.ssh/id_rsa.pub` som offentlig nyckelfil i ssh-rsaformat, fungerar det.

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

Resultatet bör likna följande utdata:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Logga in på den nya virtuella datorn
Logga in på din virtuella dator med den offentliga IP-adressen listad i utdata. Du kan också använda det fullständigt kvalificerade domännamnet (FQDN) som är listat:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

Inloggningsprocessen bör se ut ungefär så här:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Nästa steg
`azure vm quick-create`-kommandot är ett sätt att snabbt distribuera en virtuell dator så att du kan logga in i ett bash-gränssnitt och komma igång med ditt arbete. Men att använda `vm quick-create` ger dig inte omfattande kontroll eller gör det möjligt för dig att skapa en mer komplex miljö.  Om du vill distribuera en virtuell Linux-dator som är anpassad efter din infrastruktur kan du följa vilken som helst av dessa artiklar:

* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Du kan också [använda `docker-machine` Azure-drivrutinen med olika kommandon för att snabbt skapa en virtuell Linux-dator som Docker-värd](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
