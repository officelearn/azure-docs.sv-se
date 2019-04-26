---
title: Skapa definition av Azure-hanterat program | Microsoft Docs
description: Visar hur du skapar ett Azure-hanterat program som är avsett för medlemmar i din organisation.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1f80d7e63d994f0e3eb3733b99afaa1b056f4686
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252402"
---
# <a name="publish-an-azure-managed-application-definition"></a>Publicera en definition av Azure-hanterat program

Den här snabbstarten ger en introduktion till att arbeta med hanterade program. Du lägger till en hanterad programdefinition till en intern katalog för användare i din organisation. Vi har redan skapat filerna för det hanterade programmet för att förenkla introduktionen. Filerna är tillgängliga via GitHub. Du lär dig hur du skapar dessa filer i självstudien [Create service catalog application](publish-service-catalog-app.md) (Skapa tjänstkatalogprogram).

När du är klar har du en resursgrupp med namnet **appDefinitionGroup** som har definitionen av det hanterade programmet.

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

* **resursgrupp**: Namnet på resursgruppen där definitionen för hanterade programmet skapas.
* **lock-level**: Typ av Lås placeras på den hanterade resursgruppen. Det förhindrar kunden från att utföra oönskade åtgärder på den här resursgruppen. ReadOnly stöds för närvarande endast på låsnivå. När ReadOnly har angivits kan kunden endast läsa resurser som finns i den hanterade resursgruppen. Utgivaridentiteter som beviljas åtkomst till den hanterade resursgruppen är undantagna från låset.
* **auktoriseringar**: Beskriver huvudkontots ID och rolldefinitions-ID som används för att ge behörighet till den hanterade resursgruppen. Det anges i formatet `<principalId>:<roleDefinitionId>`. Om det krävs fler än ett värde anger du dem i formatet `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Värdena avgränsas med ett blanksteg.
* **package-file-uri**: Platsen för ett ZIP-paket som innehåller filerna som krävs. Paketet måste ha filerna **mainTemplate.json** och **createUiDefinition.json**. **mainTemplate.json** definierar de Azure-resurser som skapas som en del av det hanterade programmet. Mallen skiljer sig inte från en vanlig Resource Manager-mall. **createUiDefinition.json** genererar användargränssnittet för användare som skapar det hanterade programmet via portalen.

## <a name="next-steps"></a>Nästa steg

Du har publicerat definitionen av det hanterade programmet. Nu ska du lära dig hur du distribuerar en instans av den definitionen.

> [!div class="nextstepaction"]
> [Snabbstart: Distribuera service catalog-app](deploy-service-catalog-quickstart.md)