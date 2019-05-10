---
title: Snabbstart – installera Ansible på Linux-datorer i Azure | Microsoft Docs
description: I den här snabbstarten lär du dig hur du installerar och konfigurerar Ansible för att hantera Azure-resurser på Ubuntu och CentOS SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 60cefe24feab9de4262e81eb1bc313aeadc7eb05
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409243"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Snabbstart: Installera Ansible på Linux-datorer i Azure

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du konfigurerar Ansible för några av de vanligaste Linux-distributioner. Om du vill installera Ansible för andra distributioner, justerar du installerade paket för din specifika plattform. 

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Åtkomst till Linux eller en virtuell Linux-dator** – om du inte har en Linux-dator skapar du en [virtuell Linux-dator](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installera Ansible på en virtuell Linux-dator för Azure

Logga in på Linux-datorn och välj någon av följande distributioner för att få stegvisa instruktioner för hur du installerar Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

I det här avsnittet konfigurerar du CentOS om du vill använda Ansible.

1. Öppna ett terminalfönster.

1. Ange följande kommando för att installera de nödvändiga paketen för Azure Python SDK-moduler:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Ange följande kommando för att installera de nödvändiga paketen Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Skapa autentiseringsuppgifter för Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

I det här avsnittet konfigurerar du Ubuntu om du vill använda Ansible.

1. Öppna ett terminalfönster.

1. Ange följande kommando för att installera de nödvändiga paketen för Azure Python SDK-moduler:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Ange följande kommando för att installera de nödvändiga paketen Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Skapa autentiseringsuppgifter för Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

I det här avsnittet konfigurerar du SLES om du vill använda Ansible.

1. Öppna ett terminalfönster.

1. Ange följande kommando för att installera de nödvändiga paketen för Azure Python SDK-moduler:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Ange följande kommando för att installera de nödvändiga paketen Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Ange följande kommando för att ta bort motstridiga kryptografipaketet för Python:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Skapa autentiseringsuppgifter för Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter

Om du vill konfigurera Ansible-autentiseringsuppgifter, behöver du följande information:

* Ditt Azure-prenumerations-ID 
* Tjänstens huvudnamn värden

Om du använder Ansible Tower eller Jenkins deklarera service principal värdena som miljövariabler.

Konfigurera Ansible-autentiseringsuppgifter med någon av följande metoder:

- [Skapa en fil för Ansible-autentiseringsuppgifter](#file-credentials)
- [Använda Ansible-miljövariabler](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Skapa en fil för Ansible-autentiseringsuppgifter

I det här avsnittet skapar du en fil med lokala autentiseringsuppgifter för att tillhandahålla autentiseringsuppgifter till Ansible. 

Mer information om hur du definierar Ansible autentiseringsuppgifter finns i [att tillhandahålla autentiseringsuppgifter till Azure-moduler](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Skapa en fil med namnet för en utvecklingsmiljö `credentials` på den virtuella värddatorn:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Infoga följande rader i filen. Ersätt platshållarna med värdena tjänstens huvudnamn.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Spara och stäng filen.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Använda Ansible-miljövariabler

I det här avsnittet ska exportera du service principal värdena för att konfigurera Ansible-autentiseringsuppgifter.

1. Öppna ett terminalfönster.

1. Exportera service principal värdena:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen

Om du vill verifiera lyckad konfiguration du Använd Ansible för att skapa en Azure-resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabbstart: Konfigurera en Linux-dator i Azure med Ansible](./ansible-create-vm.md)