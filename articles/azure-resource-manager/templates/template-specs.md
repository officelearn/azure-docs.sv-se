---
title: Översikt över mall-specifikationer
description: Beskriver hur du skapar specifikationer för mallar och delar dem med andra användare i din organisation.
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099739"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specifikationer för Azure Resource Manager mall (för hands version)

En mall specifikation är en ny resurs typ för att lagra en Azure Resource Manager-mall (ARM-mall) i Azure för senare distribution. Med den här resurs typen kan du dela ARM-mallar med andra användare i din organisation. Precis som med andra Azure-resurser kan du använda rollbaserad åtkomst kontroll (RBAC) för att dela mallen specifikation. användarna behöver bara Läs behörighet till mallen för att distribuera mallen, så du kan dela mallen utan att tillåta andra att ändra den.

**Microsoft. Resources/templateSpecs** är den nya resurs typen för specifikationer för mallar. Det består av en huvudmall och ett valfritt antal länkade mallar. Azure sparar säkert mall-specifikationer i resurs grupper. Mall-specifikationer stöder [versions hantering](#versioning).

Om du vill distribuera en mall använder du standard Azure-verktyg som PowerShell, Azure CLI, Azure Portal, REST och andra SDK: er och klienter som stöds. Du använder samma kommandon och skickar i samma parametrar för mallen.

Fördelen med att använda mall-specifikationer är att team i din organisation inte behöver återskapa eller kopiera mallar för vanliga scenarier. Du skapar kanoniska mallar och delar dem. Mallarna som du lägger till i en mall specifikation bör verifieras av administratörer i organisationen för att följa organisationens krav och vägledning.

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Du måste [Registrera dig för listan vänte tid för](https://aka.ms/templateSpecOnboarding)att kunna använda den.

## <a name="create-template-spec"></a>Skapa mall-specifikation

I följande exempel visas en enkel mall för att skapa ett lagrings konto i Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

När du skapar mallen för mallen skickas filen PowerShell-eller CLI-kommandon till huvud mal len. Om huvudmallen refererar till länkade mallar, hittar kommandona dem och paketerar dem för att skapa mallen specifikation. Mer information finns i [skapa en mall-specifikation med länkade mallar](#create-a-template-spec-with-linked-templates).

Skapa en mall specifikation genom att använda:

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

Du kan visa alla mall-specifikationer i din prenumeration genom att använda:

```azurepowershell
Get-AzTemplateSpec
```

Du kan visa information om en mall-specifikation, inklusive dess versioner med:

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

När du har skapat specifikationen för mallen kan användare med **Läs** behörighet till mallen distribuera den. Information om hur du beviljar åtkomst finns i [Självstudier: bevilja en grupp åtkomst till Azure-resurser med hjälp av Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Specifikationer för mallar kan distribueras via portalen, PowerShell, Azure CLI eller som en länkad mall i en större mall-distribution. Användare i en organisation kan distribuera en mall specifikation till valfri omfattning i Azure (resurs grupp, prenumeration, hanterings grupp eller klient organisation).

I stället för att skicka in en sökväg eller URI för en mall distribuerar du en mall-specifikation genom att ange dess resurs-ID. Resurs-ID: t har följande format:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Observera att resurs-ID: t innehåller ett versions nummer för mallens specifikation.

Du kan till exempel distribuera en mall-specifikation med följande PowerShell-kommando.

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

I praktiken kommer du vanligt vis `Get-AzTemplateSpec` att köra för att hämta ID: t för den mall specifikation som du vill distribuera.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>Skapa en mall-specifikation med länkade mallar

Om huvud mal len för din mall-spec refererar till länkade mallar kan PowerShell-och CLI-kommandona automatiskt hitta och paketera de länkade mallarna från den lokala enheten. Du behöver inte manuellt konfigurera lagrings konton eller lagrings platser som är värdar för mallens specifikationer – allt är självständigt i resursens Specifikations resurs.

Följande exempel består av en huvud-mall med två länkade mallar. Exemplet är bara utdrag ur mallen. Observera att den använder en egenskap med namnet `relativePath` för att länka till de andra mallarna. Du måste använda `apiVersion` `2020-06-01` eller senare för distributions resursen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

När PowerShell-eller CLI-kommandot för att skapa mallen körs i föregående exempel, hittar kommandot tre filer – huvud mal len, webbappens mall ( `webapp.json` ) och databas mal len ( `database.json` )-och paketerar dem i mallen specifikation.

Mer information finns i [Självstudier: skapa en mall-specifikation med länkade mallar](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Distribuera mallens specifikation som en länkad mall

När du har skapat en mall är det enkelt att återanvända den från en ARM-mall eller någon annan mall-specifikation. Du länkar till en mall-specifikation genom att lägga till dess resurs-ID i mallen. Den länkade mallens specifikation distribueras automatiskt när du distribuerar huvud mal len. Med det här beteendet kan du utveckla specifikationer för modulär mall och återanvända dem efter behov.

Du kan till exempel skapa en mall-specifikation som distribuerar nätverks resurser och en annan mall-specifikation som distribuerar lagrings resurser. I ARM-mallar länkar du till dessa två mallar för mallar varje gång du behöver konfigurera nätverks-eller lagrings resurser.

Följande exempel liknar det tidigare exemplet, men du använder `id` egenskapen för att länka till en mall-specifikation snarare än `relativePath` egenskapen för att länka till en lokal mall. Använd `2020-06-01` för API-versionen för distributions resursen. I exemplet finns mall-specifikationerna i en resurs grupp med namnet **templateSpecsRG**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

Mer information om hur du länkar mall-specifikationer finns i [Självstudier: Distribuera en mall specifikation som en länkad mall](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Versionshantering

När du skapar en mall-specifikation anger du ett versions nummer för den. När du itererar på mallkod kan du antingen uppdatera en befintlig version (för snabb korrigeringar) eller publicera en ny version. Versionen är en text sträng. Du kan välja att följa alla versioner av systemet, inklusive semantisk versions hantering. Användare av mallen specifikation kan ange det versions nummer som de vill använda för att distribuera det.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar och distribuerar en mall finns i [snabb start: skapa och distribuera mall specifikation](quickstart-create-template-specs.md).

* Mer information om hur du länkar mallar i specifikationer för mallar finns i [Självstudier: skapa en mall-specifikation med länkade mallar](template-specs-create-linked.md).

* Mer information om hur du distribuerar en mall specifikation som en länkad mall finns i [självstudie: Distribuera en mall specifikation som en länkad mall](template-specs-deploy-linked-template.md).
