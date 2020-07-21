---
title: Med den här Azure Resource Manager mall kommer du att kunna distribuera privat slut punkt för webb program.
description: Lär dig hur du använder ARM-mallen för att distribuera privat slut punkt för din webbapp
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535731"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Skapa en App Service-app och distribuera privat slut punkt med Azure Resource Manager-mall

I den här snabb starten använder du en Azure Resource Manager-mall för att skapa en webbapp och exponera den med en privat slut punkt.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Förutsättning

Du behöver ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Den här mallen skapar en privat slut punkt för en Azure-webbapp.

### <a name="review-the-template"></a>Granska mallen

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Distribuera mallen

Så här distribuerar du Azure Resource Manager-mallen till Azure:

1. Om du vill logga in på Azure och öppna mallen väljer du **distribuera till Azure**. Mallen skapar VNet, webbappen, den privata slut punkten och den privata DNS-zonen.

   [Distribuera till Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp.
3. Skriv namnet på din webbapp, App Service plan, privat slut punkt.
5. Läs villkors satsen. Om du samtycker väljer du jag accepterar villkoren ovan > köpet. Distributionen kan ta flera minuter att slutföra.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med den privata slut punkten tar du bort resurs gruppen. Den privata slut punkten och alla relaterade resurser tas bort.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

- Ytterligare Azure Resource Manager mallar för Azure App Service Web Apps finns i [exemplen på arm-mal len](../samples-resource-manager-templates.md).
