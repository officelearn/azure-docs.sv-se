---
title: Låsa resurser för att förhindra ändringar
description: Hindra användare från att uppdatera eller ta bort viktiga Azure-resurser genom att använda ett lås för alla användare och roller.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274013"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Låsa resurser för att förhindra oväntade ändringar

Som administratör kan du behöva låsa en prenumeration, resursgrupp eller resurs så att inga andra användare i organisationen tar bort eller ändrar viktiga resurser av misstag. Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen kallas låsen **Ta bort** respektive **skrivskyddat.**

* **CanNotDelete** innebär att behöriga användare fortfarande kan läsa och ändra en resurs, men de kan inte ta bort resursen. 
* **ReadOnly** innebär att behöriga användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Att använda det här låset liknar att begränsa alla behöriga användare till de behörigheter som beviljas av **rollen Läsare.**

## <a name="how-locks-are-applied"></a>Hur lås används

När du använder ett lås på ett överordnat scope ärver alla resurser i det omfånget samma lås. Även resurser som du lägger till senare ärver låset från den överordnade. Det mest restriktiva låset i arvet har företräde.

Till skillnad från rollbaserad åtkomstkontroll använder du hanteringslås för att tillämpa en begränsning för alla användare och roller. Mer information om hur du anger behörigheter för användare och roller finns i [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md).

Resource Manager-lås gäller endast för åtgärder som sker i hanteringsplanet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsar inte hur resurser utför sina egna funktioner. Resursändringar är begränsade, men resursåtgärder är inte begränsade. Ett ReadOnly-lås på en SQL-databas hindrar dig till exempel från att ta bort eller ändra databasen. Det hindrar dig inte från att skapa, uppdatera eller ta bort data i databasen. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

Om du använder **ReadOnly** kan det leda till oväntade resultat eftersom vissa åtgärder som inte verkar ändra resursen kräver åtgärder som blockeras av låset. **ReadOnly-låset** kan tillämpas på resursen eller på resursgruppen som innehåller resursen. Några vanliga exempel på de åtgärder som blockeras av ett **ReadOnly-lås** är:

* Ett **ReadOnly-lås** på ett lagringskonto hindrar alla användare från att lista nycklarna. Åtgärden för att visa nycklar hanteras via en POST-begäran eftersom nycklarna som returneras är tillgängliga för skrivåtgärder.

* Ett **ReadOnly-lås** på en App Service-resurs förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivåtkomst.

* Ett **ReadOnly-lås** på en resursgrupp som innehåller en virtuell dator hindrar alla användare från att starta eller starta om den virtuella datorn. Dessa åtgärder kräver en POST-begäran.

## <a name="who-can-create-or-delete-locks"></a>Vem kan skapa eller ta bort lås

Om du vill skapa eller ta `Microsoft.Authorization/*` bort `Microsoft.Authorization/locks/*` hanteringslås måste du ha åtkomst till eller åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

## <a name="managed-applications-and-locks"></a>Hanterade program och lås

Vissa Azure-tjänster, till exempel Azure Databricks, använder [hanterade program](../managed-applications/overview.md) för att implementera tjänsten. I så fall skapar tjänsten två resursgrupper. En resursgrupp innehåller en översikt över tjänsten och är inte låst. Den andra resursgruppen innehåller infrastrukturen för tjänsten och är låst.

Om du försöker ta bort infrastrukturresursgruppen visas ett felmeddelande om att resursgruppen är låst. Om du försöker ta bort låset för infrastrukturresursgruppen visas ett felmeddelande om att låset inte kan tas bort eftersom det ägs av ett systemprogram.

Ta i stället bort tjänsten, som också tar bort infrastrukturresursgruppen.

För hanterade program väljer du den tjänst som du har distribuerat.

![Välj tjänst](./media/lock-resources/select-service.png)

Observera att tjänsten innehåller en länk för en **hanterad resursgrupp**. Resursgruppen innehåller infrastrukturen och är låst. Det går inte att ta bort det direkt.

![Visa hanterad grupp](./media/lock-resources/show-managed-group.png)

Om du vill ta bort allt för tjänsten, inklusive den låsta infrastrukturresursgruppen, väljer **du Ta bort** för tjänsten.

![Ta bort tjänsten](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Azure Säkerhetskopieringar och lås

Om du låser resursgruppen som skapats av Azure Backup Service, kommer säkerhetskopior att börja misslyckas. Tjänsten stöder högst 18 återställningspunkter. Med ett **CanNotDelete-lås** kan säkerhetskopieringstjänsten inte rensa återställningspunkter. Mer information finns i [Vanliga frågor och svar-Säkerhetskopiera virtuella Azure-datorer](../../backup/backup-azure-vm-backup-faq.md).

## <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Mall

När du använder en Resource Manager-mall för att distribuera ett lås använder du olika värden för namnet och typen beroende på låsets omfattning.

När du använder ett lås på en **resurs**använder du följande format:

* namn -`{resourceName}/Microsoft.Authorization/{lockName}`
* typ -`{resourceProviderNamespace}/{resourceType}/providers/locks`

När du använder ett lås på en **resursgrupp** eller **prenumeration**använder du följande format:

* namn -`{lockName}`
* typ -`Microsoft.Authorization/locks`

I följande exempel visas en mall som skapar en apptjänstplan, en webbplats och ett lås på webbplatsen. Resurstypen för låset är resurstypen för den resurs som ska låsas och **/providers/locks**. Namnet på låset skapas genom att resursnamnet sammanfogas med **/Microsoft.Authorization/** och namnet på låset.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Ett exempel på att ange ett lås för en resursgrupp finns i [Skapa en resursgrupp och låsa den](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Du låser distribuerade resurser med Azure PowerShell med kommandot [New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Om du vill låsa en resurs anger du namnet på resursen, dess resurstyp och dess resursgruppnamn.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill låsa en resursgrupp anger du namnet på resursgruppen.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Om du vill ha information om ett lås använder du [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Om du vill ha alla lås i prenumerationen använder du:

```azurepowershell-interactive
Get-AzResourceLock
```

Om du vill hämta alla lås för en resurs använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla lås för en resursgrupp använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Du låser distribuerade resurser med Azure CLI med kommandot [az lock create.](/cli/azure/lock#az-lock-create)

Om du vill låsa en resurs anger du namnet på resursen, dess resurstyp och dess resursgruppnamn.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Om du vill låsa en resursgrupp anger du namnet på resursgruppen.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Om du vill ha information om ett lås använder du [az-låslistan](/cli/azure/lock#az-lock-list). Om du vill ha alla lås i prenumerationen använder du:

```azurecli
az lock list
```

Om du vill hämta alla lås för en resurs använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla lås för en resursgrupp använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
Du kan låsa distribuerade resurser med [REST API för hanteringslås](https://docs.microsoft.com/rest/api/resources/managementlocks). Med REST API kan du skapa och ta bort lås och hämta information om befintliga lås.

Om du vill skapa ett lås kör du:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Scopet kan vara en prenumeration, resursgrupp eller resurs. Låsnamnet är vad du vill kalla låset. Använd **2016-09-01**för api-version .

I begäran ska du inkludera ett JSON-objekt som anger egenskaperna för låset.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du ordnar resurserna logiskt finns i [Använda taggar för att ordna dina resurser](tag-resources.md)
* Du kan tillämpa begränsningar och konventioner i din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).

