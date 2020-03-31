---
title: Snabbstart - Kör ansible-spelböcker via Bash i Azure Cloud Shell
description: I den här snabbstarten får du lära dig hur du utför olika Ansible-uppgifter med Bash i Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78247882"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Snabbstart: Kör ansible-spelböcker via Bash i Azure Cloud Shell

Azure Cloud Shell är ett interaktivt, webbläsartillgängligt skal för att hantera Azure-resurser. Cloud Shell ger dig möjlighet att använda antingen en Bash eller PowerShell kommandorad. I den här artikeln använder du Bash i Azure Cloud Shell för att köra en Ansible-spelbok.

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurera Azure Cloud Shell** – Om du inte har tidigare i Azure Cloud Shell läser du [Snabbstart för Bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Ansible autentiseras med Azure när du har loggat in på Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. 

När du arbetar med flera prenumerationer anger du den `AZURE_SUBSCRIPTION_ID` prenumeration Ansible som används genom att exportera miljövariabeln. 

Kör följande kommando om du vill visa alla dina Azure-prenumerationer:

```azurecli-interactive
az account list
```

Med ditt Azure-prenumerations-ID ställer du in `AZURE_SUBSCRIPTION_ID` följande:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen
För att verifiera den lyckade konfigurationen använder du Ansible för att skapa en Azure-resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabbstart: Konfigurera virtuell dator i Azure med Ansible](./ansible-create-vm.md)