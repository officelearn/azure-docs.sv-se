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
ms.date: 01/03/2018
ms.author: tomfitz
ms.openlocfilehash: ab42789b091898c69091ba6b3fa2a8bf91e711f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
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
I följande exempel visas en mall som skapar en app service-plan, en webbplats och ett lås på webbplatsen. Resurstypen för låset är resurstypen för resurs att låsa och **/providers/Lås**. Namnet på låset har skapats genom att resursnamnet med **/Microsoft.Authorization/** och namnet på låset.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Du Lås distribueras resurser med Azure PowerShell med hjälp av den [ny AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) kommando.

Ange namnet på resursen och dess resurstypen dess resursgruppens namn om du vill låsa en resurs.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill låsa en resursgrupp, ange namnet på resursgruppen.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

För att få information om ett lås använda [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Om du vill hämta alla Lås i din prenumeration, använder du:

```powershell
Get-AzureRmResourceLock
```

Om du vill hämta alla låsningar för en resurs, använder du:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla låsningar för en resursgrupp, använder du:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås, använder du:

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Du Lås distribueras resurser med Azure CLI med hjälp av den [az Lås skapa](/cli/azure/lock#az_lock_create) kommando.

Ange namnet på resursen och dess resurstypen dess resursgruppens namn om du vill låsa en resurs.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Om du vill låsa en resursgrupp, ange namnet på resursgruppen.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

För att få information om ett lås använda [az Lås listan](/cli/azure/lock#az_lock_list). Om du vill hämta alla Lås i din prenumeration, använder du:

```azurecli
az lock list
```

Om du vill hämta alla låsningar för en resurs, använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla låsningar för en resursgrupp, använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås, använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST-API
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
* Läs om hur du ordnar dina resurser i [med taggar för att organisera dina resurser](resource-group-using-tags.md)
* Om du vill ändra vilken resursgrupp som en resurs som finns i [flytta resurser till den nya resursgruppen.](resource-group-move-resources.md)
* Du kan tillämpa begränsningar och konventioner över din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

