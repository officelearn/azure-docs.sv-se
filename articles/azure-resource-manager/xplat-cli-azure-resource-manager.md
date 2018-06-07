---
title: Hantera resurser med Azure CLI | Microsoft Docs
description: Använda Azure-kommandoradsgränssnittet (CLI) för att hantera Azure-resurser och grupper
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: 4111d1d990f3e9efb78fae0476b027d5bae013af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603254"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Använda Azure CLI för att hantera Azure-resurser och resursgrupper

I den här artikeln lär du dig att hantera dina lösningar med Azure CLI och Azure Resource Manager. Om du inte är bekant med Resource Manager finns [översikt över Resource Manager](resource-group-overview.md). Den här artikeln fokuserar på hanteringsuppgifter. Du kommer att:

1. Skapa en resursgrupp
2. Lägg till en resurs i resursgruppen.
3. Lägga till en etikett till resursen
4. Fråga resurser baserat på namn eller värden
5. Använda och ta bort ett lås på resursen
6. Ta bort en resursgrupp

Den här artikeln visar inte hur du distribuerar en Resource Manager-mall till din prenumeration. Den här informationen finns [distribuera resurser med Resource Manager-mallar och Azure CLI](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt, se [installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Set-prenumeration

Om du har mer än en prenumeration kan växla du till en annan prenumeration. Först ska vi se alla prenumerationer för ditt konto.

```azurecli-interactive
az account list
```

Returnerar en lista över aktiverade och inaktiverade prenumerationer.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Observera att en prenumeration är markerad som standard. Den här prenumerationen är den aktuella kontexten för åtgärder. Ange om du vill växla till en annan prenumeration prenumerationsnamn med den **az inställt** kommando.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Använd för att visa den aktuella kontexten prenumerationer **az konto visa** utan parametrar:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du distribuerar några resurser till din prenumeration, måste du skapa en resursgrupp som innehåller resurser.

Du skapar en resursgrupp med kommandot **az group create**. Kommandot använder den **namn** parametern för att ange ett namn för resursgruppen och **plats** parametern för att ange dess plats.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Utdata är i följande format:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Om du behöver hämta resursgruppen senare använder du följande kommando:

```azurecli-interactive
az group show --name TestRG1
```

Om du vill hämta alla resursgrupper i din prenumeration, använder du:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Lägg till resurser i en resursgrupp

Om du vill lägga till en resurs i resursgruppen som du kan använda den **az resurs skapa** kommando eller ett kommando som är specifika för typ av resurs som du skapar (t.ex. **az storage-konto skapar**). Det kan vara enklare att använda ett kommando som är specifika för en resurstyp eftersom den innehåller parametrar för de egenskaper som behövs för den nya resursen. Att använda **az resurs skapa**, måste du känna till alla egenskaperna för att ange utan som efterfrågas.

Lägga till en resurs via skript kan orsaka förvirring för framtida eftersom den nya resursen inte finns i en Resource Manager-mall. Mallar kan du distribuerar din lösning på ett tillförlitligt sätt och flera gånger.

Följande kommando skapar ett lagringskonto. Ange ett unikt namn för storage-konto i stället för med hjälp av namnet som visas i exemplet. Namnet måste vara mellan 3 och 24 tecken långt och innehålla endast siffror och gemener. Om du använder det namn som visas i exemplet felmeddelande ett eftersom namnet redan används.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Om du behöver hämta den här resursen senare använder du följande kommando:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Lägga till en tagg

Taggar kan du organisera dina resurser efter olika egenskaper. Du kan till exempel ha flera resurser i olika resursgrupper som tillhör samma avdelning. Du kan använda en avdelning taggen och ett värde till dessa resurser för att markera dem som tillhör samma kategori. Eller så kan du markera om en resurs används i en produktionsmiljö eller testmiljö. Du lägger till taggar till en resurs i den här artikeln, men i din miljö det mest sannolika klokt att lägga till taggar för dina resurser.

Följande kommando använder två taggar till ditt lagringskonto:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Taggar uppdateras som ett enskilt objekt. Om du vill lägga till en tagg i en resurs som redan omfattar taggar, att hämta befintliga taggar. Lägg till ny tagg i det objekt som innehåller befintliga taggar och tillämpa alla taggar till resursen.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Sök efter resurser

Använd den **az resurslistan** kommando för att hämta resurser för olika sökvillkor.

* För att få en resurs med namnet, ange den **namn** parameter:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Om du vill hämta alla resurser i en resursgrupp, ange den **resursgrupp** parameter:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Om du vill hämta alla resurser med taggnamn och värde, ange den **taggen** parameter:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Alla resurser med en viss resurstyp, ange den **resurstypen** parameter:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Hämta resurs-ID

Många kommandon ta en resurs-ID som en parameter. Om du vill hämta ID för en resurs och lagra i en variabel, använder du:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Låsa en resurs

När du behöver se till att en kritisk resurs tas inte bort av misstag eller ändras kan tillämpa ett lås på resursen. Du kan ange antingen en **CanNotDelete** eller **ReadOnly**.

För att skapa eller ta bort management lås, måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. I de inbyggda rollerna beviljas endast ägare och administratör för användaråtkomst dessa åtgärder.

Om du vill använda ett lås, använder du följande kommando:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Låst resursen i föregående exempel kan inte tas bort förrän låset tas bort. Ta bort ett lås med:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Mer information om inställningen Lås finns [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Ta bort resurser eller resursgrupp
Du kan ta bort en resurs eller en resursgrupp. När du tar bort en resursgrupp kan du också ta bort alla resurser i resursgruppen.

* Använda delete-kommandot för att ta bort en resurs från resursgruppen för den resurstyp som du vill ta bort. Kommandot tar bort resursen, men tar inte bort resursgruppen.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Ta bort en resursgrupp och alla dess resurser genom att köra kommandot **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

För båda kommandon uppmanas du att bekräfta att du vill ta bort resurs eller resursgrupp.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skapar Resource Manager-mallar i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Läs om hur du distribuerar mallar i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy-cli.md).
* Du kan flytta befintliga resurser till en ny resursgrupp. Exempel finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).