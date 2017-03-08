---
title: "Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0 | Microsoft Azure"
description: "Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0."
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
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0
Den här artikeln beskriver hur du snabbt distribuerar en Linux-baserad virtuell dator på Azure med kommandot [az vm create](/cli/azure/vm#create) med hjälp av Azure CLI 2.0 genom att använda både hanterade diskar och diskar på egna lagringskonton. Du kan också utföra dessa steg med [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Om du vill skapa en virtuell dator behöver du följande: 

* ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad
* vara inloggad på ditt Azure-konto (skriv [az login](/cli/azure/#login))

(Du kan också distribuera en virtuell Linux-dator med hjälp av [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

I följande exempel visas hur du distribuerar en virtuell Debian-dator och ansluter till den med hjälp av en SSH-nyckel (Secure Shell). Dina argument kan se annorlunda ut. Om du vill ha en annan avbildning kan du [söka efter en](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Använda hanterade diskar

För att kunna använda Azure-hanterade diskar måste du använda en region som har stöd för dem. Börja med att skriva [az group create](/cli/azure/group#create) för att skapa din resursgrupp som innehåller alla distribuerade resurser:

```azurecli
 az group create -n myResourceGroup -l westus
```

De utdata som returneras liknar dessa (du kan välja ett annat `--output`-alternativ om du vill se ett annat format):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Skapa en virtuell dator 
Nu kan du skapa din virtuella dator och dess miljö. Kom ihåg att ersätta `--public-ip-address-dns-name`-värdet med ett unikt värde; det nedan kan vara upptaget.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


De utdata som returneras liknar följande. Notera antingen `publicIpAddress`- eller `fqdn`-värdet för **ssh** för den virtuella datorn.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Logga in på din virtuella dator genom att använda antingen den offentliga IP-adressen eller det fullständiga domännamnet (FQDN) som visas i utdata.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

Utdata liknande följande bör visas, beroende på vilken distribution du valde:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Se [Nästa steg](#next-steps) för information om andra saker som du kan göra med en ny virtuell dator med hjälp av hanterade diskar.

## <a name="using-unmanaged-disks"></a>Använda ohanterade diskar 

Virtuella datorer som använder ohanterade lagringsdiskar har ohanterade lagringskonton. Börja med att skriva [az group create](/cli/azure/group#create) för att skapa en resursgrupp som innehåller alla distribuerade resurser:

```azurecli
az group create --name nativedisks --location westus
```

De utdata som returneras liknar följande (du kan välja ett annat `--output`-alternativ om du vill):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Skapa en virtuell dator 

Nu kan du skapa din virtuella dator och dess miljö. Använd flaggan `--use-unmanaged-disk` för att skapa den virtuella datorn med ohanterade diskar. Ett ohanterat lagringskonto skapas också. Kom ihåg att ersätta `--public-ip-address-dns-name`-värdet med ett unikt värde; det nedan kan vara upptaget.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

De utdata som returneras liknar följande. Notera antingen `publicIpAddress`- eller `fqdn`-värdet för **ssh** för den virtuella datorn.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Logga in på den virtuella datorn med hjälp av den offentliga IP-adressen eller det fullständiga domännamnet (FQDN) som listas i utdata ovan.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

Utdata liknande följande bör visas, beroende på vilken distribution du valde:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Nästa steg
`az vm create`-kommandot är ett sätt att snabbt distribuera en virtuell dator så att du kan logga in i ett bash-gränssnitt och komma igång med ditt arbete. Men att använda `az vm create` ger dig inte omfattande kontroll eller gör det möjligt för dig att skapa en mer komplex miljö.  Om du vill distribuera en virtuell Linux-dator som är anpassad efter din infrastruktur kan du följa vilken som helst av dessa artiklar:

* [Skapa en specifik distribution med hjälp av en Azure Resource Manager-mall](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Du kan också [använda Azure-drivrutinen `docker-machine` med olika kommandon för att snabbt skapa en virtuell Linux-dator som en Docker-värd ](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), och om du använder Java kan du prova metoden [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).


