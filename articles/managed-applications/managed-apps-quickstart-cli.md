---
title: Skapa Azure-hanterat program med Azure CLI | Microsoft Docs
description: Visar hur du skapar ett Azure-hanterat program som är avsett för medlemmar i din organisation.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: f84fdd421ec6857cd940108546a16eb47770c766
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/18/2018
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Skapa och distribuera Azure-hanterat program med Azure CLI

Den här artikeln ger en introduktion till att arbeta med hanterade program. Du lägger till en hanterad programdefinition till en intern katalog för användare i din organisation. Sedan distribuerar du det hanterade programmet till din prenumeration. Vi har redan skapat filerna för det hanterade programmet för att förenkla introduktionen. En fil definierar infrastrukturen för lösningen. En andra fil definierar användargränssnittet för att distribuera det hanterade programmet via portalen. Filerna är tillgängliga via GitHub. Du lär dig hur du skapar dessa filer i självstudien [Create service catalog application](publish-service-catalog-app.md) (Skapa tjänstkatalogprogram).

När du är klar har du tre resursgrupper som innehåller olika delar av det hanterade programmet.

| Resursgrupp | Contains | Beskrivning |
| -------------- | -------- | ----------- |
| appDefinitionGroup | Definitionen för det hanterade programmet. | Utgivaren skapar den här resursgruppen och definitionen för det hanterade programmet. Alla med tillgång till definitionen för det hanterade programmet kan distribuera det. |
| applicationGroup | Instansen för det hanterade programmet. | Konsumenten skapar den här resursgruppen och instansen för det hanterade programmet. Konsumenten kan uppdatera det hanterade programmet via den här instansen. |
| infrastructureGroup | Resurser för det hanterade programmet. | Den här resursgruppen skapas automatiskt när det hanterade programmet skapas. Utgivaren har åtkomst till den här resursgruppen för att hantera programmet. Konsumenten har begränsad åtkomst till resursgruppen. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Skapa en resursgrupp för definitionen

Din definition för det hanterade programmet finns i en resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Använd följande kommando för att skapa en resursgrupp:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

När du definierar det hanterade programmet så väljer du en användare, grupp eller ett program som hanterar resurser för kunden. Den här identiteten har behörigheter på den hanterade resursgruppen enligt den roll som är tilldelad. Normalt skapar du en Azure Active Directory-grupp om du vill hantera resurser. I den här artikeln använder du dock din egen identitet.

Ange ditt UPN-namn i följande kommando för att hämta objekt-ID för din identitet:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Sedan måste du använda rolldefinitions-ID för den inbyggda RBAC-rollen som du vill bevilja användaren åtkomst till. Följande kommando visar hur du hämtar rolldefinitions-ID:t för ägarrollen:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Skapa nu definitionsresursen för det hanterade programmet. Det hanterade programmet innehåller endast ett lagringskonto.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

När kommandot har slutförts har du en definition för det hanterade programmet i resursgruppen. 

Några av de parametrar som användes i det föregående exemplet är:

* **resursgrupp**: Namnet på resursgruppen där definitionen för det hanterade programmet skapas.
* **låsnivå**: Typ av lås som placerats på den hanterade resursgruppen. Det förhindrar kunden från att utföra oönskade åtgärder på den här resursgruppen. ReadOnly stöds för närvarande endast på låsnivå. När ReadOnly har angivits kan kunden endast läsa resurser som finns i den hanterade resursgruppen. Utgivaridentiteter som beviljas åtkomst till den hanterade resursgruppen är undantagna från låset.
* **auktorisering**: Beskriver huvudkontots ID och rolldefinitions-ID som används för att ge behörigheter till den hanterade resursgruppen. Det anges i formatet `<principalId>:<roleDefinitionId>`. Flera värden kan också anges för den här egenskapen. Om flera värden krävs anges de i formatet `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Flera värden avgränsas med ett blanksteg.
* **uri för paketfil**: Platsen för ett zip-paket som innehåller de filer som krävs. Som minst innehåller paketet filerna **mainTemplate.json** och **createUiDefinition.json**. **mainTemplate.json** definierar de Azure-resurser som tillhandahålls som en del av det hanterade programmet. Mallen skiljer sig inte från en vanlig Resource Manager-mall. **createUiDefinition.json** genererar användargränssnittet för användare som skapar det hanterade programmet via portalen.

## <a name="create-resource-group-for-managed-application"></a>Skapa resursgrupp för hanterat program

Nu är du redo att distribuera det hanterade programmet. 

Du behöver en resursgrupp för att lagra det distribuerade hanterade programmet. Använd **westcentralus** som plats.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Distribuera det hanterade programmet

Du kan distribuera programmet med följande kommandon:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Några av de parametrar som användes i det föregående exemplet är:

* **managedapp-definition-id**: ID för definitionen som du skapade tidigare i den här artikeln.
* **managed-rg-id**: ID för resursgruppen för resurser som är associerade med det hanterade programmet. Kommandot skapar den här resursgruppen. Den **får inte finnas innan du kör kommandot**. Den här resursgruppen hanteras av utgivaren. 
* **resursgrupp**: Resursgruppen där resursen för det hanterade programmet skapas.
* **parametrar**: Parametrarna som behövs för resurser som är associerade med det hanterade programmet.

När distributionen har slutförts visas det hanterade programmet som skapats i applicationGroup. StorageAccount-resursen skapas i infrastructureGroup.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempel på filer finns i [Managed application samples](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) (Exempel på hanterade program).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.
