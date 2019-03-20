---
title: Köra Ansible med Bash i Azure Cloud Shell
description: Lär dig hur du utför olika Ansible-uppgifter med Bash i Azure Cloud Shell
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: quickstart
ms.openlocfilehash: 37426f1cdc51734c28b2c9739e26ad35f08e606f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791400"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Köra Ansible med Bash i Azure Cloud Shell

Lär dig hur du använder Bash i Cloud Shell för att konfigurera en Azure-prenumeration som en Ansible-arbetsyta i den här självstudien. 

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurera Azure Cloud Shell** – Om du inte har använt Azure Cloud Shell tidigare rekommenderar vi att du läser artikeln [Snabbstart för Bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), som beskriver hur du startar och konfigurerar Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Ansible autentiseras med Azure när du har loggat in på Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. Om du har mer än en prenumeration kan du välja vilken prenumeration som ska användas med Ansible genom att exportera miljövariabeln `AZURE_SUBSCRIPTION_ID`. Kör följande kommando om du vill visa alla dina Azure-prenumerationer:

```azurecli-interactive
az account list
```

Använd det **id** för den prenumeration som du vill arbeta i och ange **AZURE_SUBSCRIPTION_ID** så här:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen
För att verifiera en lyckad konfiguration kan du nu använda Ansible till att skapa en resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)