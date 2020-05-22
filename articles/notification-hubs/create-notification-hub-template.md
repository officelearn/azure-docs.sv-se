---
title: Skapa en Azure Notification Hub med Azure Resource Manager-mall
description: Lär dig hur du skapar en Azure Notification Hub med hjälp av en Azure Resource Manager-mall.
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 05/15/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: aefccb831fe35898962893a173c5bd1125877def
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743532"
---
# <a name="quickstart-create-a-notification-hub-using-an-azure-resource-manager-template"></a>Snabb start: skapa en Notification Hub med en Azure Resource Manager-mall

Azure Notification Hubs tillhandahåller en lättanvänd och skalad push-motor som gör att du kan skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle osv.) från alla Server delar (moln eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs](notification-hubs-push-notification-overview.md).

I den här snabb starten används en Azure Resource Manager-mall för att skapa ett Azure Notification Hubs-namnområde och en Notification-hubb med namnet "MyHub" inom det namn området.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Inga.

## <a name="create-a-notification-hubs-namespace-and-hub"></a>Skapa ett Notification Hubs namn område och hubb

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json" range="1-45" highlight="22-40":::

* [Microsoft. NotificationHubs/namnrymder](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces)
* [Microsoft. NotificationHubs/Namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen använder ett Notification Hubs namn områdes namn som parameter. Mallen skapar sedan ett namn område med det namnet och en Notification Hub med namnet **MyHub** inom denna namnrymd.

[![Distribuera till Azure](./media/create-notification-hub-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrol lera de distribuerade resurserna eller använda Azure CLI eller Azure PowerShell skript för att visa en lista över det distribuerade Notification Hubs namn området och hubben:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första Azure Resource Manager-mall](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
