---
title: Snabbstart - Installera Ansible på virtuella Linux-datorer i Azure
description: I den här snabbstarten kan du lära dig hur du installerar och konfigurerar Ansible för hantering av Azure-resurser på Ubuntu, CentOS och SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202412"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Snabbstart: Installera Ansible på virtuella Linux-datorer i Azure

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du konfigurerar Ansible för några av de vanligaste Linux-distributionerna. Om du vill installera Ansible på andra distributioner justerar du de installerade paketen för just din plattform. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Åtkomst till Linux eller en virtuell Linux-dator** – om du inte har en Linux-dator skapar du en [virtuell Linux-dator](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installera Ansible på en virtuell Linux-dator för Azure

Logga in på Linux-datorn och välj någon av följande distributioner för att få stegvisa instruktioner för hur du installerar Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

I det här avsnittet konfigurerar du CentOS för att använda Ansible.

1. Öppna ett terminalfönster.

1. Ange följande kommando för att installera de nödvändiga paketen för Azure Python SDK-modulerna:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Ange följande kommando för att installera de nödvändiga paketen Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Skapa Azure-autentiseringsuppgifterna](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

I det här avsnittet konfigurerar du Ubuntu för att använda Ansible.

1. Öppna ett terminalfönster.

1. Ange följande kommando för att installera de nödvändiga paketen för Azure Python SDK-modulerna:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Ange följande kommando för att installera de nödvändiga paketen Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Skapa Azure-autentiseringsuppgifterna](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

I det här avsnittet konfigurerar du SLES för att använda Ansible.

1. Öppna ett terminalfönster.

1. Ange följande kommando för att installera de nödvändiga paketen för Azure Python SDK-modulerna:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Ange följande kommando för att installera de nödvändiga paketen Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Ange följande kommando för att ta bort python-krypteringspaketet i konflikt:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Skapa Azure-autentiseringsuppgifterna](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter

Om du vill konfigurera Ansible-autentiseringsuppgifterna behöver du följande information:

* Ditt Azure-prenumerations-ID 
* Tjänstens huvudvärden

Om du använder Ansible Tower eller Jenkins deklarerar du tjänstens huvudvärden som miljövariabler.

Konfigurera Ansible-autentiseringsuppgifterna med någon av följande tekniker:

- [Skapa en fil för Ansible-autentiseringsuppgifter](#file-credentials)
- [Använda miljövariabler för ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Skapa en fil för Ansible-autentiseringsuppgifter

I det här avsnittet skapar du en lokal autentiseringsfil för att ge autentiseringsuppgifter till Ansible. 

Mer information om hur du definierar autentiseringsuppgifter för Ansible finns i [Tillhandahålla autentiseringsuppgifter till Azure-moduler](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Skapa en fil med namnet `credentials` på den virtuella värddatorn för en utvecklingsmiljö:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Infoga följande rader i filen. Ersätt platshållarna med tjänstens huvudvärden.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Spara och stäng filen.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Använda Ansible-miljövariabler

I det här avsnittet exporterar du tjänstens huvudvärden för att konfigurera ansible-autentiseringsuppgifterna.

1. Öppna ett terminalfönster.

1. Exportera tjänstens huvudvärden:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen

För att verifiera den lyckade konfigurationen använder du Ansible för att skapa en Azure-resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabbstart: Konfigurera en virtuell Linux-dator i Azure med Ansible](./ansible-create-vm.md)