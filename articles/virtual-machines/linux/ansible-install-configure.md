---
title: Installera och konfigurera Ansible för användning med Azure virtual machines | Microsoft Docs
description: Lär dig att installera och konfigurera Ansible för att hantera Azure-resurser på Ubuntu och CentOS SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930077"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installera och konfigurera Ansible och hantera virtuella datorer i Azure

Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella datorer (VM) i Azure, samma precis som alla andra resurser. Den här artikeln beskriver hur du installerar Ansible och de Azure Python SDK-modulerna som krävs för några av de vanligaste Linux-distributioner. Du kan installera Ansible för andra distributioner genom att justera installerade paket så att de passar din specifika plattform. Azure-resurser på ett säkert sätt skapar du också lära dig hur du skapar och definiera autentiseringsuppgifter för Ansible att använda.

Fler installationsalternativ och stegen för att ytterligare plattformar finns i den [Ansible installera guiden](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Installera Ansible

En av de enklaste sätten att använda Ansible med Azure är med Azure Cloud Shell en webbläsarbaserad skalupplevelse att hantera och utveckla Azure-resurser. Ansible är förinstallerad i Cloud Shell så att du kan gå vidare instruktioner om hur du installerar Ansible och gå till [skapa Azure-autentiseringsuppgifter](#create-azure-credentials). En lista över ytterligare verktyg som är även tillgängligt i Cloud Shell, se [funktioner och verktyg för Bash i Azure Cloud Shell](../../cloud-shell/features.md#tools).

Följande instruktioner visar hur du skapar en Linux-VM för olika distributioner och installera sedan Ansible. Om du inte behöver skapa en Linux VM kan du hoppa över det här första steget för att skapa en Azure-resursgrupp. Om du behöver skapa en virtuell dator, först skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nu väljer du något av följande distributioner stegvisa instruktioner för hur du skapar en virtuell dator, om det behövs och sedan installera Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Om det behövs skapar du en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med den `publicIpAddress` anges i utdata från den virtuella datorn för att skapa:

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

Nu gå vidare till [skapa Azure-autentiseringsuppgifter](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Om det behövs skapar du en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med den `publicIpAddress` anges i utdata från den virtuella datorn för att skapa:

```bash
ssh azureuser@<publicIpAddress>
```

På den virtuella datorn att installera de nödvändiga paketen för Azure Python SDK-moduler och Ansible på följande sätt:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Nu gå vidare till [skapa Azure-autentiseringsuppgifter](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Om det behövs skapar du en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med den `publicIpAddress` anges i utdata från den virtuella datorn för att skapa:

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

Nu gå vidare till [skapa Azure-autentiseringsuppgifter](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Skapa autentiseringsuppgifter för Azure

Ansible kommunicerar med Azure med ett användarnamn och lösenord eller ett huvudnamn för tjänsten. Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som Ansible. Du kontrollerar och definiera behörigheter om vilka åtgärder som tjänstens huvudnamn kan utföra i Azure. För att förbättra säkerheten över bara ange användarnamn och lösenord, kan det här exemplet skapar en grundläggande tjänst huvudnamn.

På värddatorn eller i Azure Cloud Shell, skapa ett tjänstobjekt med [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). De autentiseringsuppgifter som Ansible behöver visas skärmen:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Ett exempel på utdata från föregående kommandon är följande:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Om du vill autentisera till Azure måste du också behöva hämta ID: T Azure-prenumerationen med [az konto show](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Du kan använda utdata från dessa två kommandon i nästa steg.

## <a name="create-ansible-credentials-file"></a>Skapa autentiseringsuppgiftsfilen för Ansible

För att tillhandahålla autentiseringsuppgifter till Ansible, definiera miljövariabler eller skapa en fil med lokala autentiseringsuppgifter. Mer information om hur du definierar Ansible autentiseringsuppgifter finns i [att tillhandahålla autentiseringsuppgifter till Azure-moduler](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

För en utvecklingsmiljö, skapar du en *autentiseringsuppgifter* filen för Ansible på din Virtuella värddator. Skapa en fil för autentiseringsuppgifter på den virtuella datorn där du installerade Ansible i föregående steg:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Den *autentiseringsuppgifter* själva filen om du kombinerar prenumerations-ID med utdata för att skapa ett huvudnamn för tjänsten. Utdata från föregående [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) kommandot är samma som behövs för *client_id*, *hemlighet*, och *klient*. I följande exempel *autentiseringsuppgifter* filen visar dessa värden som matchar föregående utdata. Ange dina egna värden enligt följande:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Spara och stäng filen.

## <a name="use-ansible-environment-variables"></a>Använda Ansible-miljövariabler

Om du tänker använda verktyg som Ansible Tower eller Jenkins, måste du definiera miljövariabler. Det här steget kan hoppas över om du bara ska använda Ansible-klienten och autentiseringsuppgifter för Azure-filen som skapades i föregående steg. Miljövariabler definiera samma information som autentiseringsuppgifter för Azure-fil:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Nästa steg

Nu har du Ansible och nödvändiga Azure Python SDK-moduler som installerats och autentiseringsuppgifter som definierats för Ansible att använda. Lär dig hur du [skapa en virtuell dator med Ansible](ansible-create-vm.md). Du kan också lära dig hur du [skapa en hel Azure virtuell dator och stödresurser med Ansible](ansible-create-complete-vm.md).
