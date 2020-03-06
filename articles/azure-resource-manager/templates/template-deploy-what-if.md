---
title: Malldistribution vad-om (för hands version)
description: Ta reda på vilka ändringar som sker i resurserna innan du distribuerar en Azure Resource Manager-mall.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388509"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager-mall för att distribuera konsekvens åtgärder (för hands version)

Innan du distribuerar en mall kanske du vill förhandsgranska de ändringar som kommer att ske. Azure Resource Manager tillhandahåller åtgärden vad händer om du kan se hur resurser kommer att ändras om du distribuerar mallen. Konsekvens åtgärden gör inga ändringar i befintliga resurser. I stället förväntas ändringarna om den angivna mallen distribueras.

> [!NOTE]
> Konsekvens åtgärden är för närvarande en för hands version. Du måste [Registrera dig för för hands versionen för](https://aka.ms/armtemplatepreviews)att kunna använda den. Som en för hands version kan resultatet ibland visa att en resurs kommer att ändras när ingen ändring sker. Vi arbetar för att minska problemen, men vi behöver din hjälp. Rapportera de här problemen på [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Du kan använda åtgärden vad händer om med PowerShell-kommandon eller REST API åtgärder.

I PowerShell innehåller utdata färgkodade resultat som hjälper dig att se de olika typerna av ändringar.

![Resource Manager-mall distribution av konsekvens åtgärder fullresourcepayload och ändrings typer](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

Du kan använda antingen Azure PowerShell eller Azure-REST API för konsekvens åtgärden.

### <a name="azure-powershell"></a>Azure PowerShell

Om du vill se en förhands granskning av ändringarna innan du distribuerar en mall, lägger du till parametern `-Whatif` switch i distributions kommandot.

* `New-AzResourceGroupDeployment -Whatif` för resurs grupps distributioner
* `New-AzSubscriptionDeployment -Whatif` och `New-AzDeployment -Whatif` för distributioner på prenumerations nivå

Eller så kan du använda parametern `-Confirm` växel för att förhandsgranska ändringarna och uppmanas att fortsätta med distributionen.

* `New-AzResourceGroupDeployment -Confirm` för resurs grupps distributioner
* `New-AzSubscriptionDeployment -Confirm` och `New-AzDeployment -Confirm` för distributioner på prenumerations nivå

Föregående kommandon returnerar en text sammanfattning som du kan kontrol lera manuellt. Om du vill hämta ett objekt som du kan använda för att kontrol lera ändringar använder du:

* `$results = Get-AzResourceGroupDeploymentWhatIf` för resurs grupps distributioner
* `$results = Get-AzSubscriptionDeploymentWhatIf` eller `$results = Get-AzDeploymentWhatIf` för distributioner på prenumerations nivå

> [!NOTE]
> Före lanseringen av alpha5-versionen av version 2.0.1 användes kommandot `New-AzDeploymentWhatIf`. Det här kommandot har ersatts av kommandona `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`och `Get-AzSubscriptionDeploymentWhatIf`. Om du har använt en tidigare version måste du uppdatera den syntaxen. Parametern `-ScopeType` har tagits bort.

### <a name="azure-rest-api"></a>Azure-REST API

För REST API använder du:

* [Distributioner – what if](/rest/api/resources/deployments/whatif) för resurs grupps distributioner
* [Distributioner – what if vid prenumerations omfång](/rest/api/resources/deployments/whatifatsubscriptionscope) för distributioner på prenumerations nivå

## <a name="change-types"></a>Ändra typer

Åtgärden konsekvens visar sex olika typer av ändringar:

- **Skapa**: resursen finns inte för närvarande, men den har definierats i mallen. Resursen kommer att skapas.

- **Ta bort**: den här ändrings typen gäller endast när [slutfört läge](deployment-modes.md) används för distribution. Resursen finns, men har inte definierats i mallen. Med slutfört läge tas resursen bort. Endast resurser som [stöder fullständig borttagning av läge](complete-mode-deletion.md) ingår i den här ändrings typen.

- **Ignorera**: resursen finns, men har inte definierats i mallen. Resursen kommer inte att distribueras eller ändras.

- **Nochang**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras, men egenskaperna för resursen ändras inte. Den här ändrings typen returneras när [ResultFormat](#result-format) är inställt på `FullResourcePayloads`, vilket är standardvärdet.

- **Ändra**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras och egenskaperna för resursen ändras. Den här ändrings typen returneras när [ResultFormat](#result-format) är inställt på `FullResourcePayloads`, vilket är standardvärdet.

- **Distribuera**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras. Egenskaperna för resursen kanske inte ändras. Åtgärden returnerar den här ändrings typen när den inte har tillräckligt med information för att avgöra om några egenskaper kommer att ändras. Du ser bara det här villkoret när [ResultFormat](#result-format) är inställt på `ResourceIdOnly`.

## <a name="result-format"></a>Resultat format

Du kan styra detalj nivån som returneras om förväntade ändringar. Använd parametern **-WhatIfResultFormat** i distributions kommandona (`New-Az*Deployment`). I programmerings objekts kommandona (`Get-Az*DeploymentWhatIf`) använder du parametern **ResultFormat** .

Ställ in parametern format på **FullResourcePayloads** för att hämta en lista över resurser som kommer att ändras och information om de egenskaper som kommer att ändras. Ställ in parametern format på **ResourceIdOnly** för att hämta en lista över resurser som kommer att ändras. Standardvärdet är **FullResourcePayloads**.  

Följande resultat visar de två olika utdataformaten:

- Fullständiga resurs nytto laster

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

## <a name="run-what-if-operation"></a>Kör konsekvens Åtgärd

### <a name="set-up-environment"></a>Konfigurera miljö

För att se hur det fungerar kan vi köra vissa tester. Distribuera först en [mall som skapar ett virtuellt nätverk](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Du använder det här virtuella nätverket för att testa hur ändringar rapporteras av vad-om.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Test ändring

När distributionen är klar är du redo att testa konsekvens åtgärden. Nu ska du distribuera en [mall som ändrar det virtuella nätverket](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Det saknas en ursprunglig tagg, ett undernät har tagits bort och adressprefixet har ändrats.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Vad-om-utdata ser ut ungefär så här:

![Resource Manager-mall distribution av konsekvens åtgärder](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Textens utdata är:

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

Observera överst i utdata som färger definieras för att ange typ av ändringar.

Längst ned i utdata visas taggens ägare borttagen. Adressprefixet har ändrats från 10.0.0.0/16 till 10.0.0.0/15. Under nätet med namnet subnet001 togs bort. Kom ihåg att ändringarna inte distribuerades i själva verket. Du ser en förhands granskning av de ändringar som sker om du distribuerar mallen.

Några av egenskaperna som visas som borttagna ändras inte. Egenskaper kan rapporteras felaktigt som borttagna när de inte finns i mallen, utan anges automatiskt under distributionen som standardvärden. Resultatet betraktas som "brus" i svaret. Den slutgiltiga distribuerade resursen kommer att ha värdena som angetts för egenskaperna. När konsekvens åtgärden mognar kommer dessa egenskaper att filtreras bort från resultatet.

## <a name="programmatically-evaluate-what-if-results"></a>Utvärdera vad som händer genom programmering

Nu ska vi program mässigt utvärdera vad-om-resultatet genom att ange kommandot till en variabel.

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

Konsekvens åtgärden stöder användning av [distributions läge](deployment-modes.md). När du har angett till slutfört läge raderas inte resurser som inte finns i mallen. I följande exempel distribueras en [mall som inte har några definierade resurser](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) i komplett läge.

Om du vill förhandsgranska ändringar innan du distribuerar en mall använder du parametern `-Confirm` växel med kommandot Deployment. Om ändringarna visas som du förväntade dig, bekräftar du att du vill att distributionen ska slutföras.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Eftersom inga resurser har definierats i mallen och distributions läget är inställt på Slutför, tas det virtuella nätverket bort.

![Resource Manager-mall distributions läge för konsekvens distribution av utdata slutförs](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Textens utdata är:

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

- Om du upptäcker felaktiga resultat från för hands versionen av vad-om, kan du rapportera problemen på [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Information om hur du distribuerar mallar med Azure PowerShell finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](deploy-powershell.md).
- Information om hur du distribuerar mallar med REST finns i [distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](deploy-rest.md).
