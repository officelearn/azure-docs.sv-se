---
title: Installera och konfigurera Ansible för användning med virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar Ansible för att hantera Azure-resurser på Ubuntu och CentOS SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: iainfou
ms.openlocfilehash: e6fad548eda35d1832cb4ecc2fd9bdabf825f361
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installera och konfigurera Ansible för att hantera virtuella datorer i Azure

Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella datorer (VM) i Azure, samma som någon annan resurs. Den här artikeln beskrivs hur du installerar Ansible och Azure SDK för Python-moduler som krävs för några av de vanligaste Linux-distributioner. Du kan installera Ansible på andra distributioner genom att justera installerade paket så att de passar din viss plattform. Om du vill skapa Azure-resurser på ett säkert sätt du också lära dig hur du skapar och definiera autentiseringsuppgifter för Ansible ska användas.

Fler alternativ för installation och anvisningar för ytterligare plattformar finns i [Ansible installationsguide](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Installera Ansible

En av de enklaste sätten att använda Ansible med Azure är med Azure Cloud Shell Avbrottsfritt webbläsarbaserade gränssnittet för att hantera och utveckla Azure-resurser. Ansible är förinstallerad molnet Shell, så du kan hoppa över instruktioner om hur du installerar Ansible och gå till [skapa Azure-autentiseringsuppgifterna](#create-azure-credentials). En lista över ytterligare verktyg är också tillgängliga i Cloud-gränssnittet, se [funktioner och verktyg för Bash i Azure-molnet gränssnittet](../../cloud-shell/features.md#tools).

I följande anvisningar visar hur du skapar en Linux VM för olika distributioner och installera sedan Ansible. Om du inte behöver skapa ett Linux VM, hoppa över detta steg om du vill skapa en Azure-resursgrupp. Om du behöver skapa en virtuell dator, först skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nu väljer du något av följande distributioner stegvisa instruktioner för hur du skapar en virtuell dator, om det behövs, och sedan installera Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Om det behövs kan du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
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

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Om det behövs kan du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
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

### <a name="sles-12-sp2"></a>SLES 12 SP2

Om det behövs kan du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
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

På värddatorn eller i Azure Cloud-gränssnittet, skapa ett service principal med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac). Autentiseringsuppgifterna som Ansible måste är utdata till skärmen:

```azurecli-interactive
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

Om du vill autentisera till Azure, du måste också hämta din Azure-prenumeration ID med [az konto visa](/cli/azure/account#az_account_show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Du kan använda utdata från dessa två kommandon i nästa steg.

## <a name="create-ansible-credentials-file"></a>Skapa Ansible autentiseringsuppgifter fil

För att tillhandahålla autentiseringsuppgifter till Ansible definiera miljövariabler eller skapa en fil med lokala autentiseringsuppgifter. Mer information om hur du definierar Ansible autentiseringsuppgifter finns [att tillhandahålla autentiseringsuppgifter till Azure-moduler](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

En utvecklingsmiljö kan skapa en *autentiseringsuppgifter* Ansible fil på din Virtuella värddator. Skapa en fil för autentiseringsuppgifter på den virtuella datorn där du installerade Ansible i föregående steg:

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

Spara och stäng filen.

## <a name="use-ansible-environment-variables"></a>Använda Ansible miljövariabler

Om du tänker använda verktyg som Ansible torn eller Jenkins måste du definiera miljövariabler. Det här steget kan hoppas över om du bara ska använda Ansible klienten och autentiseringsuppgifter för Azure-fil som skapats i föregående steg. Miljövariabler definierar samma information som autentiseringsuppgifter för Azure-fil:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Nästa steg

Nu har du Ansible och nödvändiga Azure Python SDK-moduler som installerats och autentiseringsuppgifter som har definierats för Ansible ska användas. Lär dig hur du [skapa en virtuell dator med Ansible](ansible-create-vm.md). Du kan också lära dig hur du [skapa en fullständig Azure-VM och stödja resurser med Ansible](ansible-create-complete-vm.md).
