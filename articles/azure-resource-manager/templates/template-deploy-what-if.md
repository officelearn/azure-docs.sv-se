---
title: Malldistribution what-if (förhandsgranska)
description: Bestäm vilka ändringar som ska hända med dina resurser innan du distribuerar en Azure Resource Manager-mall.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: bc42585204e5cc2c3ece5293a3934fd22fe8507b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156454"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Arm-malldistribution vad händer om -åtgärd (förhandsversion)

Innan du distribuerar en ARM-mall (Azure Resource Manager) kanske du vill förhandsgranska de ändringar som ska ske. Azure Resource Manager tillhandahåller vad händer om-åtgärden så att du kan se hur resurser kommer att ändras om du distribuerar mallen. Vad händer om-åtgärden gör inga ändringar i befintliga resurser. I stället förutses ändringarna om den angivna mallen distribueras.

> [!NOTE]
> Vad händer om-åtgärden är för närvarande i förhandsversion. Om du vill använda den måste du [registrera dig för förhandsgranskningen](https://aka.ms/armtemplatepreviews). Som en förhandsversion kan resultaten ibland visa att en resurs ändras när det faktiskt inte sker någon ändring. Vi arbetar för att minska dessa frågor, men vi behöver din hjälp. Vänligen rapportera dessa [https://aka.ms/whatifissues](https://aka.ms/whatifissues)frågor på .

Du kan använda vad händer om-åtgärden med PowerShell-kommandona eller REST API-åtgärder.

I PowerShell innehåller utdata färgkodade resultat som hjälper dig att se de olika typerna av ändringar.

![Resurshanterarens malldistribution what-if-åtgärd fullresourcepayload och ändra typer](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Texten ouptput är:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Vad händer om-kommandon

Du kan använda antingen Azure PowerShell eller Azure REST API för vad händer om-åtgärden.

### <a name="azure-powershell"></a>Azure PowerShell

Om du vill se en förhandsgranskning av `-Whatif` ändringarna innan du distribuerar en mall lägger du till växelparametern i distributionskommandot.

* `New-AzResourceGroupDeployment -Whatif`för resursgruppsdistributioner
* `New-AzSubscriptionDeployment -Whatif`och `New-AzDeployment -Whatif` för prenumerationsnivådistributioner

Du kan också `-Confirm` använda switchparametern för att förhandsgranska ändringarna och uppmanas att fortsätta med distributionen.

* `New-AzResourceGroupDeployment -Confirm`för resursgruppsdistributioner
* `New-AzSubscriptionDeployment -Confirm`och `New-AzDeployment -Confirm` för prenumerationsnivådistributioner

De föregående kommandona returnerar en textsammanfattning som du kan granska manuellt. Om du vill hämta ett objekt som du kan programmässigt inspektera för ändringar använder du:

* `$results = Get-AzResourceGroupDeploymentWhatIf`för resursgruppsdistributioner
* `$results = Get-AzSubscriptionDeploymentWhatIf`eller `$results = Get-AzDeploymentWhatIf` för prenumerationsnivådistributioner

> [!NOTE]
> Innan version 2.0.1-alpha5 släpptes använde `New-AzDeploymentWhatIf` du kommandot. Det här kommandot har `Get-AzDeploymentWhatIf` `Get-AzResourceGroupDeploymentWhatIf`ersatts `Get-AzSubscriptionDeploymentWhatIf` av kommandona , och . Om du har använt en tidigare version måste du uppdatera syntaxen. Parametern `-ScopeType` har tagits bort.

### <a name="azure-rest-api"></a>REST-API för Azure

För REST API, använd:

* [Distributioner - Vad händer om](/rest/api/resources/deployments/whatif) för resursgruppsdistributioner
* [Distributioner – Vad händer om i prenumerationsscope](/rest/api/resources/deployments/whatifatsubscriptionscope) för prenumerationsnivådistributioner

## <a name="change-types"></a>Ändra typer

I åtgärden Vad händer om visas sex olika typer av ändringar:

- **Skapa**: Resursen finns för närvarande inte men definieras i mallen. Resursen skapas.

- **Ta bort**: Den här ändringstypen gäller endast när du använder [fullständigt läge](deployment-modes.md) för distribution. Resursen finns, men definieras inte i mallen. Med fullständigt läge tas resursen bort. Endast resurser som [stöder fullständig borttagning av läge](complete-mode-deletion.md) ingår i den här ändringstypen.

- **Ignorera**: Resursen finns, men definieras inte i mallen. Resursen kommer inte att distribueras eller ändras.

- **NoChange**: Resursen finns och definieras i mallen. Resursen kommer att distribueras om, men resursens egenskaper ändras inte. Den här ändringstypen returneras `FullResourcePayloads`när [ResultFormat](#result-format) är inställt på , vilket är standardvärdet.

- **Ändra**: Resursen finns och definieras i mallen. Resursen distribueras om och resursens egenskaper ändras. Den här ändringstypen returneras `FullResourcePayloads`när [ResultFormat](#result-format) är inställt på , vilket är standardvärdet.

- **Distribuera**: Resursen finns och definieras i mallen. Resursen kommer att distribueras om. Resursens egenskaper kan ändras eller inte. Åtgärden returnerar den här ändringstypen när den inte har tillräckligt med information för att avgöra om några egenskaper kommer att ändras. Du ser bara det här villkoret när [ResultFormat](#result-format) är inställt på `ResourceIdOnly`.

## <a name="result-format"></a>Resultatformat

Du kan styra detaljnivån som returneras om de förväntade ändringarna. I distributionskommandona`New-Az*Deployment`( ) använder du parametern **-WhatIfResultFormat.** Använd parametern **ResultFormat** `Get-Az*DeploymentWhatIf`i programmatiska objektkommandon ( ).

Ange formatparametern till **FullResourcePayloads** för att få en lista över resurser som kommer att ändras och information om de egenskaper som kommer att ändras. Ange formatparametern till **ResourceIdOnly** för att få en lista över resurser som ska ändras. Standardvärdet är **FullResourcePayloads**.  

Följande resultat visar de två olika utdataformaten:

- Fullständiga resursnyttolaster

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Endast resurs-ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Kör vad händer om-åtgärden

### <a name="set-up-environment"></a>Konfigurera miljö

För att se hur vad-om fungerar, låt oss köra några tester. Distribuera först en [mall som skapar ett virtuellt nätverk](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Du ska använda det här virtuella nätverket för att testa hur ändringar rapporteras av vad händer om.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Teständring

När distributionen är klar är du redo att testa vad händer om-åtgärden. Den här gången distribuera en [mall som ändrar det virtuella nätverket](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Det saknas en av de ursprungliga taggarna, ett undernät har tagits bort och adressprefixet har ändrats.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Vad händer om-utdata liknar:

![Resurshanterarens malldistribution vad händer om-utdata för åtgärden](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Textutdata är:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Observera högst upp i utdata att färger definieras för att ange typen av ändringar.

Längst ned i utdata visas taggen Ägare har tagits bort. Adressprefixet ändrades från 10.0.0.0/16 till 10.0.0.0/15. Undernätet med namnet undernät001 har tagits bort. Kom ihåg att ändringarna inte har distribuerats. Du ser en förhandsgranskning av de ändringar som ska ske om du distribuerar mallen.

Vissa av de egenskaper som visas som borttagna ändras inte. Egenskaper kan rapporteras felaktigt som borttagna när de inte finns i mallen, men ställs automatiskt in under distributionen som standardvärden. Detta resultat anses vara "brus" i vad händer om-svaret. Den slutliga distribuerade resursen har värdena inställda för egenskaperna. När vad-händer-om-åtgärden mognar filtreras dessa egenskaper bort från resultatet.

## <a name="programmatically-evaluate-what-if-results"></a>Programmässigt utvärdera vad händer om-resultat

Nu ska vi programmässigt utvärdera vad händer om-resultaten genom att ange kommandot till en variabel.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Du kan se en sammanfattning av varje ändring.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Bekräfta borttagning

Vad händer om-åtgärden stöder användning av [distributionsläge](deployment-modes.md). När du är inställd på ett färdigt läge tas resurser som inte finns i mallen bort. I följande exempel distribueras en [mall som inte har några resurser definierade](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) i fullständigt läge.

Om du vill förhandsgranska ändringar innan `-Confirm` du distribuerar en mall använder du switchparametern med distributionskommandot. Om ändringarna är som förväntat bekräftar du att du vill att distributionen ska slutföras.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Eftersom inga resurser har definierats i mallen och distributionsläget är inställt på att slutföras tas det virtuella nätverket bort.

![Resurshanterarens malldistribution what-if-driftutdatadistributionsläge har slutförts](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Textutdata är:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Du ser de förväntade ändringarna och kan bekräfta att du vill att distributionen ska köras.

## <a name="next-steps"></a>Nästa steg

- Om du märker felaktiga resultat från förhandsversionen av vad [https://aka.ms/whatifissues](https://aka.ms/whatifissues)händer om, vänligen rapportera problemen på .
- Information om hur du distribuerar mallar med Azure PowerShell finns [i Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
- Information om hur du distribuerar mallar med REST finns [i Distribuera resurser med ARM-mallar och RESURSHANTERARENS REST API](deploy-rest.md).
