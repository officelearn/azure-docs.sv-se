---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025916"
---
> [!TIP]
> När du har loggat in visas en lista över prenumerationer som är associerade med ditt Azure-konto. Prenumerations informationen med `isDefault: true` är den aktuella aktiverade prenumerationen för Azure CLI-kommandon. Den här prenumerationen måste vara samma som innehåller din Azure Machine Learning-arbetsyta. Du hittar prenumerations-ID: t från [Azure Portal](https://portal.azure.com) genom att gå till översikts sidan för din arbets yta. Du kan också använda SDK: n för att hämta prenumerations-ID: t från arbets ytans objekt. Exempelvis `Workspace.from_config().subscription_id`.
> 
> Om du vill välja en annan prenumeration använder du `az account set -s <subscription name or ID>` kommandot och anger det prenumerations namn eller-ID som du vill växla till. Mer information om val av prenumeration finns i [använda flera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).