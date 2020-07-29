---
title: Azure CLI-referenser för Azure Data-resurs
description: Landnings sida för Azure CLI-referens för Azure Data Share
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298511"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure CLI för Azure Data Share

Azure Command Line Interface ([Azure CLI](/cli/azure/what-is-azure-cli)) är en uppsättning kommandon som används för att skapa och hantera Azure-resurser.  Det finns i många Azure-tjänster, inklusive Azure Data Share.  Det finns över 65 olika kommandon för data resurs!  Dessa kommandon ger dig möjlighet att arbeta effektivt med tjänsten från en kommando rad.

## <a name="references-for-data-share"></a>Referenser för data resurs

Alla Azure CLI-kommandon för Azure Data Share är för närvarande tillägg till Azure CLI.  Ett tillägg ger dig till gång till experiment-och för hands versions kommandon.  Lär dig mer om tilläggs referenser i [använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

|Azure CLI-referens |Beskrivning
|-|-|-|
| [AZ datashare](/cli/azure/ext/datashare/datashare) | Alla kommandon för att hantera Azure-dataresursen.
| [AZ datashare-konto](/cli/azure/ext/datashare/datashare/account) | Kommandon för att hantera data resurs konton i Azure.
| [AZ datashare-konsument](/cli/azure/ext/datashare/datashare/consumer) | Kommandon som användare kan använda för att hantera Azure-dataresursen.
| [AZ datashare-datauppsättning](/cli/azure/ext/datashare/datashare/dataset) | Kommandon för providrar för att hantera data källor för Azure Data Share.
| [AZ datashare-inbjudan](/cli/azure/ext/datashare/datashare/invitation) | Kommandon som användare kan använda för att hantera Azure Data resurs inbjudningar.
| [AZ datashare-Provider – resurs-prenumeration](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Kommandon för providrar för att hantera Azure Data Share-prenumerationer.
| [AZ datashare-synkronisering](/cli/azure/ext/datashare/datashare/synchronization)  | Kommandon för att hantera synkronisering av Azure data-resurser.
| [AZ datashare-synkronisering – inställning](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Kommandon för providrar för att hantera synkroniseringsinställningar för Azure Data resurs.

## <a name="reference-examples"></a>Referens exempel

Exempel finns i varje Azure CLI-referens. Även om du kan utföra dessa uppgifter via Azure Portal måste du använda Azure CLI för att använda en enda kommando rad.  Här följer några kodblock för att ge dig en uppfattning om hur enkelt det är att använda Azure CLI.

Om du vill arbeta med Azure Data Resource behöver du först en resurs grupp.  Azures resurs grupper är enkla att skapa och hantera med Azure CLI.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

Det är lika enkelt att skapa ett data resurs konto.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Se även

* [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli) och lär dig mer om installation och inloggning.

* Identifiera ytterligare [Core](/cli/azure/reference-index) -och [tilläggs](/cli/azure/azure-cli-extensions-list) referenser i Azure CLI-dokumentationen.
