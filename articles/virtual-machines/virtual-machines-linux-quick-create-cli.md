<properties
   pageTitle="Skapa en virtuell Linux-dator i Azure med hjälp av CLI | Microsoft Azure"
   description="Skapa en virtuell Linux-dator i Azure med hjälp av CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# Skapa en virtuell Linux-dator i Azure med hjälp av CLI

I den här artikeln lär du dig hur du snabbt distribuerar en virtuell Linux-dator i Azure med hjälp av kommandot `azure vm quick-create` i Azure CLI. Kommandot `quick-create` distribuerar en virtuell dator med en grundläggande omgivande infrastruktur som du kan använda som prototyp eller för att snabbt testa ett koncept (se det som den snabbaste vägen till ett bash-gränssnitt för Linux).  Artikeln kräver ett Azure-konto ([skaffa en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)) och [Azure CLI](../xplat-cli-install.md) inloggad (`azure login`) och i resurshanteringsläge (`azure config mode arm`).  Du kan också snabbt distribuera en virtuell Linux-dator med hjälp av [Azure Portal](virtual-machines-linux-quick-create-portal.md).

## Snabb kommandosammanfattning

Ett kommando för att distribuera en virtuell CoreOS-dator och koppla SSH-nyckeln:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Distribuera den virtuella Linux-datorn

Följande exempel använder samma kommando som ovan och visar varje kommandoprompt jämte det förväntade resultatet, men använder RHEL-aliaset för att skapa en virtuell dator med RedHat Enterprise Linux 7.2.  

## Använda ett ImageURN-alias

Kommandot `quick-create` i Azure CLI har alias som är mappade till de vanligaste operativsystemdistributioner. I följande tabell visas alla tillgängliga distributionsalias (från och med Azure CLI version 0.10).  Alla distributioner som kör `quick-create` använder som standard SSD-lagring för virtuella datorer, vilket ger en miljö med höga prestanda.

| Alias     | Utgivare | Erbjudande        | SKU         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | senaste  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | senaste  |
| Debian    | credativ  | Debian       | 8           | senaste  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | senaste  |
| RHEL      | Redhat    | RHEL         | 7.2         | senaste  |
| SLES      | SLES      | SLES         | 12-SP1      | senaste  |
| UbuntuLTS | Canonical | UbuntuServer | 14.04.4-LTS | senaste  |



För alternativet **ImageURN** (`-Q`) använder vi `RHEL` för att distribuera en virtuell dator med RedHat Enterprise Linux 7.2. (Dessa sju alias representerar ett fåtal av de tillgängliga operativsystemen i Azure. Om du vill hitta andra avbildningar kan du [söka efter en avbildning](virtual-machines-linux-cli-ps-findimage.md) på Marketplace. Du kan också [ladda upp en egen anpassad avbildning](virtual-machines-linux-create-upload-generic.md).)

I följande kommandogenomgång ersätter du prompterna med värden från din miljö. Vi använder exempelvärden.  

Följ anvisningarna och ange egna namn

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

Resultatet bör likna följande utdata.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Nu kan du koppla SSH till den virtuella datorn med SSH-standardporten 22 och det fullständigt kvalificerade domännamnet (FQDN) som anges i resultatet ovan. (Du kan också använda IP-adressen som anges.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
Inloggningen bör se ut ungefär så här:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Nästa steg

`azure vm quick-create` är ett sätt att snabbt distribuera en virtuell dator så att du kan logga in i ett bash-gränssnitt och komma igång med ditt arbete. `vm quick-create` ger inte de ytterligare fördelarna med en komplex miljö.  Om du vill distribuera en virtuell Linux-dator som är anpassad efter din infrastruktur följer du relevant artikel nedan.

- [Skapa en specifik distribution med hjälp av en Azure Resource Manager-mall](virtual-machines-linux-cli-deploy-templates.md)
- [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](virtual-machines-linux-create-cli-complete.md).
- [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Dessa artiklar hjälper dig att komma igång med att skapa en Azure-infrastruktur och valfritt antal distributions-, konfigurations- och hanteringsverktyg, såväl proprietära som med öppen källkod.



<!--HONumber=Jun16_HO2-->


