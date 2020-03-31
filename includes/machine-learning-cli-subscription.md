---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296883"
---
När du har loggat in visas en lista över prenumerationer som är kopplade till ditt Azure-konto. Prenumerationsinformationen `isDefault: true` med är den aktuella aktiverade prenumerationen för Azure CLI-kommandon. Den här prenumerationen måste vara samma som innehåller din Azure Machine Learning-arbetsyta. Du hittar prenumerations-ID:t från [Azure-portalen](https://portal.azure.com) genom att besöka översiktssidan för din arbetsyta. Du kan också använda SDK för att hämta prenumerations-ID från arbetsytan objektet. Till exempel `Workspace.from_config().subscription_id`.
    
Om du vill välja en annan prenumeration använder du kommandot [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) med prenumerations-ID:n för att växla till. Mer information om prenumerationsval finns i [Använda flera Azure-prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).