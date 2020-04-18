---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616843"
---
> [!TIP]
> När du har loggat in visas en lista över prenumerationer som är kopplade till ditt Azure-konto. Prenumerationsinformationen `isDefault: true` med är den aktuella aktiverade prenumerationen för Azure CLI-kommandon. Den här prenumerationen måste vara samma som innehåller din Azure Machine Learning-arbetsyta. Du hittar prenumerations-ID:t från [Azure-portalen](https://portal.azure.com) genom att besöka översiktssidan för din arbetsyta. Du kan också använda SDK för att hämta prenumerations-ID från arbetsytan objektet. Till exempel `Workspace.from_config().subscription_id`.
> 
> Om du vill välja `az account set -s <subscription name or ID>` en annan prenumeration använder du kommandot och anger prenumerationsnamnet eller ID:t som ska växlas till. Mer information om prenumerationsval finns i [Använda flera Azure-prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).