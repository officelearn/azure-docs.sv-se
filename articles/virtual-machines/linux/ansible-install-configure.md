---
title: "Installera och konfigurera Ansible för användning med virtuella Azure-datorer | Microsoft Docs"
description: "Lär dig hur du installerar och konfigurerar Ansible för att hantera Azure-resurser på Ubuntu och CentOS SLES"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 91173a14d40f8259927af720986a4efbc9c573ce
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installera och konfigurera Ansible för att hantera virtuella datorer i Azure
Den här artikeln beskrivs hur du installerar Ansible och Azure SDK för Python-moduler som krävs för några av de vanligaste Linux-distributioner. Du kan installera Ansible på andra distributioner genom att justera installerade paket så att de passar din viss plattform. Om du vill skapa Azure-resurser på ett säkert sätt du också lära dig hur du skapar och definiera autentiseringsuppgifter för Ansible ska användas. 

Fler alternativ för installation och anvisningar för ytterligare plattformar finns i [Ansible installationsguide](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Installera Ansible
Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupAnsible* i den *eastus* plats:

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

Nu kan skapa en virtuell dator och installera Ansible för en av följande distributioner önskat:

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till virtuell dator med hjälp av den `publicIpAddress` anges i utdata från den virtuella datorn Skapa-åtgärd:

```bash
ssh azureuser@<publicIpAddress>
```

På den virtuella datorn att installera de nödvändiga paketen för Azure Python SDK-moduler och Ansible på följande sätt:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Nu gå vidare till [skapa Azure-autentiseringsuppgifterna](#create-azure-credentials).


### <a name="centos-73"></a>CentOS 7.3
Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till virtuell dator med hjälp av den `publicIpAddress` anges i utdata från den virtuella datorn Skapa-åtgärd:

```bash
ssh azureuser@<publicIpAddress>
```

På den virtuella datorn att installera de nödvändiga paketen för Azure Python SDK-moduler och Ansible på följande sätt:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Nu gå vidare till [skapa Azure-autentiseringsuppgifterna](#create-azure-credentials).


### <a name="sles-12-sp2"></a>SLES 12 SP2
Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till virtuell dator med hjälp av den `publicIpAddress` anges i utdata från den virtuella datorn Skapa-åtgärd:

```bash
ssh azureuser@<publicIpAddress>
```

På den virtuella datorn att installera de nödvändiga paketen för Azure Python SDK-moduler och Ansible på följande sätt:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Nu gå vidare till [skapa Azure-autentiseringsuppgifterna](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Skapa autentiseringsuppgifter för Azure
Ansible kommunicerar med Azure med ett användarnamn och lösenord eller ett huvudnamn för tjänsten. En Azure-tjänstens huvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och verktyg för automatisering som Ansible. Du styr och definiera behörigheter för vilka åtgärder som tjänstens huvudnamn kan utföra i Azure. Om du vill förbättra säkerheten bara att ange ett användarnamn och lösenord, kan det här exemplet skapar en grundläggande service principal.

Skapa ett huvudnamn för tjänsten på värddatorn med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac) och utdata autentiseringsuppgifterna som Ansible måste:

```azurecli
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Ett exempel på utdata från föregående kommandona är följande:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Om du vill autentisera till Azure måste du också behöva hämta ditt Azure prenumerations-ID med [az konto visa](/cli/azure/account#az_account_show):

```azurecli
az account show --query "{ subscription_id: id }"
```

Du kan använda utdata från dessa två kommandon i nästa steg.


## <a name="create-ansible-credentials-file"></a>Skapa Ansible autentiseringsuppgifter fil
För att tillhandahålla autentiseringsuppgifter till Ansible definiera miljövariabler eller skapa en fil med lokala autentiseringsuppgifter. Mer information om hur du definierar Ansible autentiseringsuppgifter finns [att tillhandahålla autentiseringsuppgifter till Azure-moduler](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules). 

En utvecklingsmiljö kan skapa en *autentiseringsuppgifter* filen för Ansible på din Virtuella värddator, enligt följande:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Den *autentiseringsuppgifter* själva filen kombinerar prenumerations-ID med utdata för att skapa ett huvudnamn för tjänsten. Utdata från den tidigare [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac) kommandot är samma som behövs för *client_id*, *hemlighet*, och *klient*. I följande exempel *autentiseringsuppgifter* filen visar värdena matchar föregående utdata. Ange egna värden enligt följande:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Använda Ansible miljövariabler
Om du tänker använda verktyg som Ansible torn eller Jenkins kan du definiera miljövariabler på följande sätt. Dessa variabler kombinera prenumerations-ID med utdata från att skapa en tjänst huvudnamn. Utdata från den tidigare [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac) kommandot är samma ordning som behövs för *AZURE_CLIENT_ID*, *AZURE_SECRET*, och *AZURE_TENANT*. 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Nästa steg
Nu har du Ansible och nödvändiga Azure Python SDK-moduler som installerats och autentiseringsuppgifter som har definierats för Ansible ska användas. Lär dig hur du [skapa en virtuell dator med Ansible](ansible-create-vm.md). Du kan också lära dig hur du [skapa en fullständig Azure-VM och stödja resurser med Ansible](ansible-create-complete-vm.md).
