---
title: Köra Ansible med Bash i Azure Cloudshell
description: Lär dig hur du utför olika uppgifter som Ansible med Bash i Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 6bfac47e4afa41b4c75a8d33b4eea1ff5103296d
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050905"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Köra Ansible med Bash i Azure Cloudshell

Lär dig hur du använder Bash i Cloud Shell för att konfigurera en Azure-prenumeration som arbetsytan Ansible i de här självstudierna. 

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurera Azure Cloud Shell** – Om du inte har använt Azure Cloud Shell tidigare rekommenderar vi att du läser artikeln [Snabbstart för Bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), som beskriver hur du startar och konfigurerar Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

När du loggat in Cloud Shell, autentiserar Ansible med Azure för att hantera infrastrukturen utan någon ytterligare konfiguration. Om du har mer än en prenumeration kan du välja vilken prenumeration som Ansible ska fungera med genom att exportera den `AZURE_SUBSCRIPTION_ID` miljövariabeln. Om du vill visa alla dina Azure-prenumerationer kör du följande kommando:

```azurecli-interactive
az account list
```

Med hjälp av den **id** för den prenumeration som du vill arbeta i **AZURE_SUBSCRIPTION_ID** på följande sätt:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen
Om du vill verifiera lyckad konfiguration du Använd Ansible för att skapa en resursgrupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)