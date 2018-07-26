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
ms.openlocfilehash: dd111c33cbd348a05ed0f0c04f7325347612e54d
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259972"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Använda Azure CLI för att hantera Azure-resurser och resursgrupper

I den här artikeln får du lära dig hur du hanterar dina lösningar med Azure CLI och Azure Resource Manager. Om du inte är bekant med Resource Manager finns i [översikt över Resource Manager](resource-group-overview.md). Den här artikeln fokuserar på administrativa uppgifter. Du kommer att:

1. Skapa en resursgrupp
2. Lägg till en resurs till resursgruppen
3. Lägga till en tagg till resursen
4. Skicka frågor till resurser baserat på namn eller taggvärden
5. Tillämpa och ta bort ett lås för resursen
6. Ta bort en resursgrupp

Den här artikeln visar inte hur du distribuerar en Resource Manager-mall till din prenumeration. Den informationen finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

För att installera och använda CLI lokalt, se [installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Set-prenumeration

Om du har mer än en prenumeration kan växla du till en annan prenumeration. Först ska vi se alla prenumerationer för ditt konto.

```azurecli-interactive
az account list
```

Den returnerar en lista över aktiverade och inaktiverade prenumerationer.

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

Observera att en prenumeration är markerad som standard. Den här prenumerationen har den aktuella kontexten för åtgärder. Om du vill växla till en annan prenumeration, ange prenumerationsnamnet på med den **az-kontogrupper** kommando.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

För att visa den aktuella prenumerationskontexten, använda **az konto show** utan parametrar:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du distribuerar resurser till din prenumeration, måste du skapa en resursgrupp som innehåller resurserna.

Du skapar en resursgrupp med kommandot **az group create**. Kommandot använder den **namn** parametern för att ange ett namn för resursgruppen och **plats** parametern för att ange dess plats.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Utdata ser i följande format:

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

Om du vill hämta resursgruppen senare använder du följande kommando:

```azurecli-interactive
az group show --name TestRG1
```

Hämta alla resursgrupper i din prenumeration med:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Lägga till resurser i en resursgrupp

Du kan använda för att lägga till en resurs till resursgruppen, den **az resource skapa** kommando eller ett kommando som är specifik för typ av resurs som du skapar (som **az storage-konto skapar**). Det kan vara lättare att använda ett kommando som är specifika för en resurstyp eftersom den innehåller parametrar för egenskaper som krävs för den nya resursen. Att använda **az resource skapa**, måste du känna till alla egenskaper för att ställa in utan att behöva ange de.

Lägga till en resurs via skript kan orsaka förvirring för framtida eftersom den nya resursen inte finns i en Resource Manager-mall. Mallar kan du distribuera din lösning på ett tillförlitligt sätt och upprepade gånger.

Följande kommando skapar ett lagringskonto. Ange ett unikt namn för storage-konto istället för att använda det namn som visas i exemplet. Namnet måste vara mellan 3 och 24 tecken långt och använder endast siffror och gemener. Om du använder det namn som visas i det här exemplet kan felmeddelande ett eftersom namnet redan används.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Om du vill hämta den här resursen senare använder du följande kommando:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Lägga till en tagg

Taggar kan du organisera dina resurser enligt olika egenskaper. Du kan till exempel ha flera resurser i olika resursgrupper som tillhör samma avdelning. Du kan använda en avdelning tagg och ett värde till dessa resurser för att markera dem som tillhör samma kategori. Eller så kan du markera om en resurs ska användas i en produktions- och miljö. I den här artikeln får du lägga till taggar till endast en resurs, men i din miljö det mest sannolika klokt att lägga till taggar till alla dina resurser.

Följande kommando gäller två taggar för ditt lagringskonto:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Taggar uppdateras som ett enda objekt. Lägg till en tagg till en resurs som redan innehåller taggar genom att hämta de befintliga taggarna. Lägg till ny tagg till det objekt som innehåller de befintliga taggarna och tillämpa alla taggar till resursen.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Sök efter resurser

Använd den **az resurslistan** kommando för att hämta resurser för olika sökvillkor.

* Om du vill ha en resurs med namnet, ange den **namn** parameter:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Om du vill hämta alla resurser i en resursgrupp, ange den **resursgrupp** parameter:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Om du vill hämta alla resurser med en taggnamnet och Taggvärdet, ange den **taggen** parameter:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Alla resurser med en viss resurstyp, ange den **resurstypen** parameter:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Hämta resurs-ID

Många kommandona tar ett resurs-ID som en parameter. Hämta ID: T för en resurs och store i en variabel med:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Låsa en resurs

När du behöver se till att en kritisk resurs tas inte bort av misstag eller ändras kan du använda ett lås för resursen. Du kan ange antingen en **CanNotDelete** eller **ReadOnly**.

För att skapa eller ta bort hanteringslås, måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. Av de inbyggda rollerna beviljas endast ägare och administratör för användaråtkomst dessa åtgärder.

Om du vill använda ett lås, använder du följande kommando:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Låst resursen i föregående exempel kan inte tas bort förrän låset tas bort. Ta bort ett lås med:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Läs mer om inställningen Lås [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Ta bort resurser och resursgrupper
Du kan ta bort en resurs eller resursgrupp. När du tar bort en resursgrupp kan bort du även alla resurser i resursgruppen.

* Använd kommandot delete för den resurstyp som du tar bort för att ta bort en resurs från resursgruppen. Kommandot tar bort resursen, men ta bort inte resursgruppen.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Ta bort en resursgrupp och alla dess resurser genom att köra kommandot **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

För båda kommandon uppmanas du att bekräfta att du vill ta bort resursen eller resursgruppen.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skapar Resource Manager-mallar i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Läs om hur du distribuerar mallar i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy-cli.md).
* Du kan flytta befintliga resurser till en ny resursgrupp. Exempel finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).