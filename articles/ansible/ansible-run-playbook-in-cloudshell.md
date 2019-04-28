---
title: Snabbstart – köra Ansible-spelböcker via Bash i Azure Cloud Shell | Microsoft Docs
description: I den här snabbstarten lär du dig hur du utför olika uppgifter som Ansible med Bash i Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: a0d0544601bfc6ce0c7ef642f576ed780c8642ba
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763358"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Snabbstart: Köra Ansible-spelböcker via Bash i Azure Cloud Shell

Azure Cloud Shell är en interaktiv, tillgängligt via webbläsaren gränssnitt för att hantera Azure-resurser. Cloudshell tillhandahåller kan du använda ett Bash- eller Powershell-kommandoraden. I den här artikeln får använda du Bash i Azure Cloud Shell för att köra en Ansible-spelbok.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurera Azure Cloud Shell** – om du inte har använt Azure Cloud Shell, se [Snabbstart för Bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Ansible autentiseras med Azure när du har loggat in på Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. 

När du arbetar med flera prenumerationer kan du ange den prenumeration som Ansible använder genom att exportera den `AZURE_SUBSCRIPTION_ID` miljövariabeln. 

Kör följande kommando om du vill visa alla dina Azure-prenumerationer:

```azurecli-interactive
az account list
```

Med hjälp av ditt Azure-prenumerations-ID, ange den `AZURE_SUBSCRIPTION_ID` på följande sätt:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen
Om du vill verifiera lyckad konfiguration du Använd Ansible för att skapa en Azure-resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabbstart: Konfigurera virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)