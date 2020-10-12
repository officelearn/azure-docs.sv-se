---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81616843"
---
> [!TIP]
> När du har loggat in visas en lista över prenumerationer som är associerade med ditt Azure-konto. Prenumerations informationen med `isDefault: true` är den aktuella aktiverade prenumerationen för Azure CLI-kommandon. Den här prenumerationen måste vara samma som innehåller din Azure Machine Learning-arbetsyta. Du hittar prenumerations-ID: t från [Azure Portal](https://portal.azure.com) genom att gå till översikts sidan för din arbets yta. Du kan också använda SDK: n för att hämta prenumerations-ID: t från arbets ytans objekt. Exempelvis `Workspace.from_config().subscription_id`.
> 
> Om du vill välja en annan prenumeration använder du `az account set -s <subscription name or ID>` kommandot och anger det prenumerations namn eller-ID som du vill växla till. Mer information om val av prenumeration finns i [använda flera Azure-prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).