---
title: Malldistribution vad-om (för hands version)
description: Ta reda på vilka ändringar som sker i resurserna innan du distribuerar en Azure Resource Manager-mall.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: tomfitz
ms.openlocfilehash: 31ef0f26043c416ff902fe792bae064c63f15b20
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2020
ms.locfileid: "84218297"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM-mall för att distribuera konsekvens åtgärder (för hands version)

Innan du distribuerar en Azure Resource Manager-mall (ARM) kan du förhandsgranska de ändringar som kommer att ske. Azure Resource Manager tillhandahåller åtgärden vad händer om du kan se hur resurser kommer att ändras om du distribuerar mallen. Konsekvens åtgärden gör inga ändringar i befintliga resurser. I stället förväntas ändringarna om den angivna mallen distribueras.

> [!NOTE]
> Konsekvens åtgärden är för närvarande en för hands version. Som en för hands version kan resultatet ibland visa att en resurs kommer att ändras när ingen ändring sker. Vi arbetar för att minska problemen, men vi behöver din hjälp. Rapportera de här problemen vid [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .

Du kan använda åtgärden vad händer om med Azure PowerShell, Azure CLI eller REST API åtgärder.

## <a name="install-powershell-module"></a>Installera PowerShell-modul

Om du vill använda konsekvens i PowerShell måste du installera en för hands version av modulen AZ. Resources från PowerShell-galleriet. Men innan du installerar modulen måste du kontrol lera att du har PowerShell Core (6. x eller 7. x). Om du har PowerShell 5. x eller tidigare [uppdaterar du din version av PowerShell](/powershell/scripting/install/installing-powershell). Du kan inte installera Preview-modulen på PowerShell 5. x eller tidigare.

### <a name="install-preview-version"></a>Installera för hands version

Om du vill installera Preview-modulen använder du:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Avinstallera alfa version

Om du tidigare har installerat en Alpha-version av modulen what-if avinstallerar du modulen. Alfa versionen är bara tillgänglig för användare som har registrerat sig för en tidig för hands version. Om du inte har installerat den för hands versionen kan du hoppa över det här avsnittet.

1. Kör PowerShell som administratör
1. Kontrol lera de installerade versionerna av modulen AZ. Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Om du har en installerad version med ett versions nummer i formatet **2. x. x-alpha**avinstallerar du den versionen.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Avregistrera den lagrings plats som du använde för att installera förhands granskningen.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Du är redo att använda vad som händer.

## <a name="install-azure-cli-module"></a>Installera Azure CLI-modul

Om du vill använda konsekvens i Azure CLI måste du ha Azure CLI-2.5.0 eller senare. Om det behövs [installerar du den senaste versionen av Azure CLI](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Visa resultat

När du använder konsekvens i PowerShell eller Azure CLI innehåller utdata färgkodade resultat som hjälper dig att se de olika typerna av ändringar.

![Resource Manager-mall distribution av konsekvens åtgärder fullresourcepayload och ändrings typer](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

## <a name="what-if-commands"></a>Vad händer om-kommandon

### <a name="azure-powershell"></a>Azure PowerShell

Om du vill förhandsgranska ändringar innan du distribuerar en mall lägger du till `-Whatif` parametern switch i distributions kommandot.

* `New-AzResourceGroupDeployment -Whatif`för resurs grupps distributioner
* `New-AzSubscriptionDeployment -Whatif`och `New-AzDeployment -Whatif` för distributioner på prenumerations nivå

Du kan använda `-Confirm` switch-parametern för att förhandsgranska ändringarna och uppmanas att fortsätta med distributionen.

* `New-AzResourceGroupDeployment -Confirm`för resurs grupps distributioner
* `New-AzSubscriptionDeployment -Confirm`och `New-AzDeployment -Confirm` för distributioner på prenumerations nivå

Föregående kommandon returnerar en text sammanfattning som du kan kontrol lera manuellt. Om du vill hämta ett objekt som du kan använda för att kontrol lera ändringar använder du:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`för resurs grupps distributioner
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`eller `$results = Get-AzDeploymentWhatIfResult` för distributioner på prenumerations nivå

### <a name="azure-cli"></a>Azure CLI

Om du vill förhandsgranska ändringar innan du distribuerar en mall använder `what-if` du med kommandot distribution.

* `az deployment group what-if`för resurs grupps distributioner
* `az deployment sub what-if`för distributioner på prenumerations nivå

Du kan använda `--confirm-with-what-if` växeln (eller dess kort form `-c` ) för att förhandsgranska ändringarna och uppmanas att fortsätta med distributionen.

* `az deployment group create --confirm-with-what-if`eller `-c` för resurs grupps distributioner
* `az deployment sub create --confirm-with-what-if`eller `-c` för distributioner på prenumerations nivå

Föregående kommandon returnerar en text sammanfattning som du kan kontrol lera manuellt. Om du vill hämta ett JSON-objekt som du kan använda program mässigt för att kontrol lera ändringar använder du:

* `az deployment group what-if --no-pretty-print`för resurs grupps distributioner
* `az deployment sub what-if --no-pretty-print`för distributioner på prenumerations nivå

### <a name="azure-rest-api"></a>REST-API för Azure

För REST API använder du:

* [Distributioner – what if](/rest/api/resources/deployments/whatif) för resurs grupps distributioner
* [Distributioner – what if vid prenumerations omfång](/rest/api/resources/deployments/whatifatsubscriptionscope) för distributioner på prenumerations nivå

## <a name="change-types"></a>Ändra typer

Åtgärden konsekvens visar sex olika typer av ändringar:

- **Skapa**: resursen finns inte för närvarande, men den har definierats i mallen. Resursen kommer att skapas.

- **Ta bort**: den här ändrings typen gäller endast när [slutfört läge](deployment-modes.md) används för distribution. Resursen finns, men har inte definierats i mallen. Med slutfört läge tas resursen bort. Endast resurser som [stöder fullständig borttagning av läge](complete-mode-deletion.md) ingår i den här ändrings typen.

- **Ignorera**: resursen finns, men har inte definierats i mallen. Resursen kommer inte att distribueras eller ändras.

- **Nochang**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras, men egenskaperna för resursen ändras inte. Den här ändrings typen returneras när [ResultFormat](#result-format) är inställd på `FullResourcePayloads` , vilket är standardvärdet.

- **Ändra**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras och egenskaperna för resursen ändras. Den här ändrings typen returneras när [ResultFormat](#result-format) är inställd på `FullResourcePayloads` , vilket är standardvärdet.

- **Distribuera**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras. Egenskaperna för resursen kanske inte ändras. Åtgärden returnerar den här ändrings typen när den inte har tillräckligt med information för att avgöra om några egenskaper kommer att ändras. Du ser bara det här villkoret när [ResultFormat](#result-format) är inställt på `ResourceIdOnly` .

## <a name="result-format"></a>Resultat format

Du styr detalj nivån som returneras om förväntade ändringar. Du kan välja mellan två alternativ:

* **FullResourcePayloads** – returnerar en lista med resurser som kommer att ändras och information om de egenskaper som kommer att ändras
* **ResourceIdOnly** – returnerar en lista med resurser som kommer att ändras

Standardvärdet är **FullResourcePayloads**.

Använd parametern för PowerShell-distributions kommandon `-WhatIfResultFormat` . Använd parametern i programmerings objekt kommandon `ResultFormat` .

För Azure CLI använder du `--result-format` parametern.
 
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Test ändring

När distributionen är klar är du redo att testa konsekvens åtgärden. Den här gången distribuerar du en [mall som ändrar det virtuella nätverket](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Det saknas en ursprunglig tagg, ett undernät har tagits bort och adressprefixet har ändrats.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

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

Längst ned i utdata visas taggens ägare borttagen. Adressprefixet har ändrats från 10.0.0.0/16 till 10.0.0.0/15. Under nätet med namnet subnet001 togs bort. Kom ihåg att dessa ändringar faktiskt inte distribuerades. Du ser en förhands granskning av de ändringar som sker om du distribuerar mallen.

Några av egenskaperna som visas som borttagna ändras inte. Egenskaper kan rapporteras felaktigt som borttagna när de inte finns i mallen, utan anges automatiskt under distributionen som standardvärden. Resultatet betraktas som "brus" i svaret. Den slutgiltiga distribuerade resursen kommer att ha värdena som angetts för egenskaperna. När konsekvens åtgärden mognar kommer dessa egenskaper att filtreras bort från resultatet.

## <a name="programmatically-evaluate-what-if-results"></a>Utvärdera vad som händer genom programmering

Nu ska vi program mässigt utvärdera vad-om-resultatet genom att ange kommandot till en variabel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Bekräfta borttagning

Konsekvens åtgärden stöder användning av [distributions läge](deployment-modes.md). När du har angett till slutfört läge raderas inte resurser som inte finns i mallen. I följande exempel distribueras en [mall som inte har några definierade resurser](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) i komplett läge.

Om du vill förhandsgranska ändringar innan du distribuerar en mall använder du parametern Confirm switch med kommandot Deployment. Om ändringarna visas som du förväntade dig bekräftar du att du vill att distributionen ska slutföras.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

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

## <a name="sdks"></a>SDK:er

Du kan använda konsekvens åtgärden via Azure SDK: er.

* För python använder du [vad-om](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* För Java använder du [DeploymentWhatIf-klass](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable).

* För .NET använder du [klassen DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet).

## <a name="next-steps"></a>Nästa steg

- Om du upptäcker felaktiga resultat från för hands versionen av vad-om, kan du rapportera problemen på [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Information om hur du distribuerar mallar med Azure PowerShell finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
- Information om hur du distribuerar mallar med Azure CLI finns i [distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md).
- Information om hur du distribuerar mallar med REST finns i [distribuera resurser med ARM-mallar och Resource Manager REST API](deploy-rest.md).
