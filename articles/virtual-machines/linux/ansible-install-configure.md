---
title: Installera Ansible på virtuella Azure-datorer
description: Lär dig hur du installerar och konfigurerar Ansible för att hantera Azure-resurser på Ubuntu, CentOS och SLES
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 38a1ffdc815b357f7bb7ebe2c337b55a738fb6b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60187738"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Installera Ansible på virtuella Azure-datorer

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella datorer (VM) i Azure på samma sätt som för alla andra resurser. Den här artikeln beskriver hur du installerar Ansible och de Azure Python SDK-moduler som krävs för några av de vanligaste Linux-distributionerna. Du kan installera Ansible för andra distributioner genom att justera de installerade paketen så att de passar din specifika plattform. För att skapa Azure-resurser på ett säkert sätt lär du dig även hur du skapar och definierar autentiseringsuppgifter som Ansible ska använda. En lista över ytterligare verktyg som är tillgängliga i Cloud Shell finns i avsnittet om [funktioner och verktyg för Bash i Azure Cloud Shell](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Åtkomst till Linux eller en virtuell Linux-dator** – om du inte har en Linux-dator skapar du en [virtuell Linux-dator](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Azure-tjänstens huvudnamn**: Följ anvisningarna i avsnittet **Skapa huvudnamn för tjänsten** i artikeln [Skapa Azure-tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Observera värdena för **appId**, **displayName**, **password** (lösenord) och **tenant** (klientorganisation).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installera Ansible på en virtuell Linux-dator för Azure

Logga in på Linux-datorn och välj någon av följande distributioner för att få stegvisa instruktioner för hur du installerar Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Installera de nödvändiga paketen för Azure Python SDK-modulerna och Ansible genom att ange följande kommandon i en terminal eller ett Bash-fönster:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Följ de anvisningar som beskrivs i avsnittet om att [skapa Azure-autentiseringsuppgifter](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Installera de nödvändiga paketen för Azure Python SDK-modulerna och Ansible genom att ange följande kommandon i en terminal eller ett Bash-fönster:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Följ de anvisningar som beskrivs i avsnittet om att [skapa Azure-autentiseringsuppgifter](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Installera de nödvändiga paketen för Azure Python SDK-modulerna och Ansible genom att ange följande kommandon i en terminal eller ett Bash-fönster:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Följ de anvisningar som beskrivs i avsnittet om att [skapa Azure-autentiseringsuppgifter](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter

Kombinationen av prenumerations-ID och den information som returneras från skapandet av tjänstens huvudnamn används för att konfigurera Ansible-autentiseringsuppgifter på ett av två sätt:

- [Skapa en fil för Ansible-autentiseringsuppgifter](#file-credentials)
- [Använda Ansible-miljövariabler](#env-credentials)

Om du tänker använda verktyg som Ansible Tower eller Jenkins behöver du använda alternativet att deklarera värden för tjänstens huvudnamn som miljövariabler.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Skapa en fil för Ansible-autentiseringsuppgifter

I det här avsnittet beskrivs hur du skapar en lokal fil med autentiseringsuppgifter för att tillhandahålla autentiseringsuppgifter till Ansible. Mer information om hur du definierar Ansible-autentiseringsuppgifter finns i avsnittet om att [tillhandahålla autentiseringsuppgifter till Azure-moduler](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

För en utvecklingsmiljö skapar du en fil med *autentiseringsuppgifter* för Ansible på den virtuella värddatorn enligt följande:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Infoga följande rader i filen med *autentiseringsuppgifter* – ersätt platshållarna med informationen skapandet av tjänstens huvudnamn.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Spara och stäng filen.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Använda Ansible-miljövariabler

I det här avsnittet beskrivs hur du konfigurerar dina Ansible-autentiseringsuppgifter genom att exportera dem som miljövariabler.

I en terminal eller ett Bash-fönster anger du följande kommandon:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen
För att verifiera en lyckad konfiguration kan du nu använda Ansible till att skapa en resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Använda Ansible för att skapa en virtuell Linux-dator i Azure](./ansible-create-vm.md)
