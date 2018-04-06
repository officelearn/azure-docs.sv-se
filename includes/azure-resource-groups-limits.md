| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Resurser per [resursgruppen](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (per resurstypen) |800 |Varierar per resurstyp |
| Distributioner per resursgrupp i distributionshistoriken |800 |800 |
| Resurser per distribution |800 |800 |
| Hantering av Lås (per unika område) |20 |20 |
| Antal taggar (per resurs eller resursgrupp) |15 |15 |
| Taggen nyckellängd |512 |512 |
| Taggen värdets längd |256 |256 |


#### <a name="template-limits"></a>Mallen gränser

| Värde | Standardgräns | Övre gräns |
| --- | --- | --- |
| Parametrar |256 |256 |
| Variabler |256 |256 |
| Resurserna (inklusive antal kopior) |800 |800 |
| Utdata |64 |64 |
| Malluttryck |24,576 tecken |24,576 tecken |
| Resurser i exporterade mallar |200 |200 | 
| Mallstorlek |1 MB |1 MB |
| Parametern filstorlek |64 kB |64 kB |

Du kan överskrida vissa mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från tidigare som inte längre behövs. Du kan ta bort poster från historiken med [az distribution bort](/cli/azure/group/deployment#az_group_deployment_delete) för Azure CLI eller [ta bort AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) i PowerShell. Ta bort en post från distributionshistoriken påverkar inte distribuera resurser. 