---
title: Köra Ansible med Bash i Azure Cloudshell
description: Lär dig hur du utför olika uppgifter som Ansible med Bash i Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 9928f646905dd0da4b15166ec55e5d8a183cb210
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054332"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Köra Ansible med Bash i Azure Cloudshell

Lär dig hur du använder Bash i Cloud Shell för att konfigurera en Azure-prenumeration som arbetsytan Ansible i de här självstudierna. 

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** – om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurera Azure Cloud Shell** – om du är nybörjare på Azure Cloud Shell, i artikeln [Snabbstart för Bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), visar hur du kan starta och konfigurera Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfigurationen av automatisk autentiseringsuppgifterna

När du loggat in Cloud Shell, autentiserar Ansible med Azure för att hantera infrastrukturen utan någon ytterligare konfiguration. Om du har mer än en prenumeration kan du välja vilken prenumeration som Ansible ska fungera med genom att exportera den `AZURE_SUBSCRIPTION_ID` miljövariabeln. Om du vill visa alla dina Azure-prenumerationer kör du följande kommando:

```azurecli-interactive
az account list
```

Med hjälp av den **id** för den prenumeration som du vill arbeta i **AZURE_SUBSCRIPTION_ID** på följande sätt:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Kontrollera att konfigurationen
Om du vill verifiera lyckad konfiguration du Använd Ansible för att skapa en resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)