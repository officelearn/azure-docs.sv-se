---
title: "Låsa Azure-resurser ska kunna ändras | Microsoft Docs"
description: "Förhindra användare från att uppdatera eller ta bort viktiga Azure-resurser genom att använda ett lås för alla användare och roller."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: d7b091f4a437781547610624007ac1d7f22fed61
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Låsa resurser för att förhindra oväntade ändringar 
Som administratör kan behöva du låsa en prenumeration, resursgrupp eller resurs för att förhindra andra användare i din organisation av misstag tas bort eller ändra viktiga resurser. Du kan ange låset för **CanNotDelete** eller **ReadOnly**. 

* **CanNotDelete** innebär behöriga användare kan läsa och ändra en resurs fortfarande, men de kan inte ta bort resursen. 
* **ReadOnly** innebär att behöriga användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Tillämpa den här Lås liknar begränsa alla behöriga användare till de behörigheter som utfärdats av den **Reader** roll. 

## <a name="how-locks-are-applied"></a>Hur Lås tillämpas

När du använder ett lås på en överordnad omfattning, ärver alla resurser i omfattningen samma låset. Även resurser som du senare lägger till ärver låset från överordnat. Det mest restriktiva låset i ärvda företräde.

Till skillnad från rollbaserad åtkomstkontroll använder du management Lås för att tillämpa en begränsning för alla användare och roller. Läs om hur du anger behörigheter för användare och roller i [Azure rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md).

Hanteraren för filserverresurser Lås gäller endast för åtgärder som sker i management-plan, som består av åtgärder som skickas till `https://management.azure.com`. Lås begränsar inte hur resurser utföra sina egna funktioner. Resursändringar är begränsad, men resursåtgärder är inte begränsade. Till exempel en ReadOnly-lås på en SQL-databas hindrar dig från att ta bort eller ändra databasen, men det förhindrar inte du att skapa, uppdatera eller ta bort data i databasen. Data transaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

Tillämpa **ReadOnly** kan leda till oväntade resultat eftersom vissa åtgärder som ser ut som att läsa operations faktiskt kräver ytterligare åtgärder. Till exempel placera en **ReadOnly** lås på ett lagringskonto som förhindrar att alla användare lista nycklarna. Listan med nycklar operationen hanteras via en POST-begäran eftersom returnerade nycklar är tillgängliga för skrivåtgärder. För ett annat exempel är att placera en **ReadOnly** lås på en App Service-resurs som förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivåtkomst.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Vem kan skapa eller ta bort lås i din organisation
För att skapa eller ta bort management lås, måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. I de inbyggda rollerna, endast **ägare** och **administratör för användaråtkomst** beviljas dessa åtgärder.

## <a name="portal"></a>Portalen
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Mall
I följande exempel visas en mall som skapar ett lås på ett lagringskonto. Storage-konto som ska tillämpas låset tillhandahålls som en parameter. Namnet på låset har skapats genom att resursnamnet med **/Microsoft.Authorization/** och namnet på Lås i det här fallet **myLock**.

Typen som är specifika för resurstypen. Ange till ”Microsoft.Storage/storageaccounts/providers/locks” för lagring.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="powershell"></a>PowerShell
Du Lås distribueras resurser med Azure PowerShell med hjälp av den [ny AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) kommando.

Ange namnet på resursen och dess resurstypen dess resursgruppens namn om du vill låsa en resurs.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite `
  -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Om du vill låsa en resursgrupp, ange namnet på resursgruppen.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete `
  -ResourceGroupName exampleresourcegroup
```

För att få information om ett lås använda [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Om du vill hämta alla Lås i din prenumeration, använder du:

```powershell
Get-AzureRmResourceLock
```

Om du vill hämta alla låsningar för en resurs, använder du:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla låsningar för en resursgrupp, använder du:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Azure PowerShell innehåller andra kommandon för att arbeta lås som [Set AzureRmResourceLock](/powershell/module/azurerm.resources/set-azurermresourcelock) att uppdatera ett lås och [ta bort AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) att ta bort ett lås.

## <a name="azure-cli"></a>Azure CLI

Du Lås distribueras resurser med Azure CLI med hjälp av den [az Lås skapa](/cli/azure/lock#create) kommando.

Ange namnet på resursen och dess resurstypen dess resursgruppens namn om du vill låsa en resurs.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete \
  --resource-group exampleresourcegroup --resource-name examplesite \
  --resource-type Microsoft.Web/sites
```

Om du vill låsa en resursgrupp, ange namnet på resursgruppen.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete \
  --resource-group exampleresourcegroup
```

För att få information om ett lås använda [az Lås listan](/cli/azure/lock#list). Om du vill hämta alla Lås i din prenumeration, använder du:

```azurecli
az lock list
```

Om du vill hämta alla låsningar för en resurs, använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite \
  --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla låsningar för en resursgrupp, använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Azure CLI finns andra kommandon för att arbeta lås som [az Lås uppdatering](/cli/azure/lock#update) att uppdatera ett lås och [az Lås ta bort](/cli/azure/lock#delete) att ta bort ett lås.

## <a name="rest-api"></a>REST API
Du kan låsa distribuerade resurser med den [REST API för hantering av Lås](https://docs.microsoft.com/rest/api/resources/managementlocks). REST-API kan du skapa och ta bort lås och hämta information om befintliga Lås.

Om du vill skapa ett lås, kör du:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Omfattningen kan vara en prenumeration, resursgrupp eller resurs. Lås-namnet är vad du vill anropa låset. Api-versionen, Använd **2015-01-01**.

I en begäran innehåller ett JSON-objekt som anger egenskaperna för låset.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du arbetar med resurslås finns [Lås ned din Azure-resurser](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Läs om hur du ordnar dina resurser i [med taggar för att organisera dina resurser](resource-group-using-tags.md)
* Om du vill ändra vilken resursgrupp som en resurs som finns i [flytta resurser till den nya resursgruppen.](resource-group-move-resources.md)
* Du kan tillämpa begränsningar och konventioner över din prenumeration med anpassade principer. Mer information finns i [vad är Azure principen?](../azure-policy/azure-policy-introduction.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

