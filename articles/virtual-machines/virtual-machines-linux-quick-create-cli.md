---
title: "Skapa en Linux-baserad virtuell dator med hjälp av Azure CLI 2.0 Preview | Microsoft Azure"
description: "Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0 Preview (förhandsversion)."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Skapa en virtuell Linux-dator med Azure CLI 2.0 Preview (az.py)
Den här artikeln beskriver hur du snabbt distribuerar en Linux-baserad virtuell dator (VM) i Azure med kommandot [az vm create](/cli/azure/vm#create) med hjälp av Azure CLI 2.0 Preview. 

> [!NOTE] 
> Azure CLI 2.0 Preview är vår nästa generations CLI för flera plattformar. [Prova.](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> I resten av våra dokument används befintliga Azure CLI. Om du vill skapa en virtuell dator med hjälp av befintliga Azure CLI 1.0 och inte CLI 2.0 Preview läser du [Skapa en virtuell dator med Azure CLI](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Om du vill skapa en virtuell dator behöver du följande: 

* ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI v. 2.0 Preview](/cli/azure/install-az-cli2) installerat
* vara inloggad på ditt Azure-konto (skriv [az login](/cli/azure/#login))

(Du kan också snabbt distribuera en virtuell Linux-dator via [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

Följande exempel visar hur du distribuerar en Debian-baserad virtuell dator och hur du kopplar din SSH-nyckel (Secure Shell). (Du kanske använder andra argument; om du vill använda en annan avbildning [kan du söka efter en](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Börja med att skriva [az group create](/cli/azure/group#create) för att skapa din resursgrupp som innehåller alla distribuerade resurser:

```azurecli
az group create -n myResourceGroup -l westus
```

De utdata som returneras liknar följande (du kan välja ett annat `--output`-alternativ om du vill):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Skapa en virtuell dator med hjälp av den senaste Debian-avbildningen

Nu kan du skapa din virtuella dator och dess miljö. Kom ihåg att ersätta `----public-ip-address-dns-name`-värdet med ett unikt värde; det nedan kan vara upptaget.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


De utdata som returneras liknar följande. Notera antingen `publicIpAddress`- eller `fqdn`-värdet för **ssh** för den virtuella datorn.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Logga in på din virtuella dator med den offentliga IP-adressen listad i utdata. Du kan också använda det fullständigt kvalificerade domännamnet (FQDN) som är listat.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

Utdata liknande följande bör visas, beroende på vilken distribution du valde:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Nästa steg
`az vm create`-kommandot är ett sätt att snabbt distribuera en virtuell dator så att du kan logga in i ett bash-gränssnitt och komma igång med ditt arbete. Men att använda `az vm create` ger dig inte omfattande kontroll eller gör det möjligt för dig att skapa en mer komplex miljö.  Om du vill distribuera en virtuell Linux-dator som är anpassad efter din infrastruktur kan du följa vilken som helst av dessa artiklar:

* [Skapa en specifik distribution med hjälp av en Azure Resource Manager-mall](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Du kan också [använda Azure-drivrutinen `docker-machine` med olika kommandon för att snabbt skapa en virtuell Linux-dator som en Docker-värd ](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), och om du använder Java kan du prova metoden [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Jan17_HO1-->


