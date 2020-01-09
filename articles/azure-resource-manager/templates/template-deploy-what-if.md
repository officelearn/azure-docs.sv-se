---
title: Malldistribution vad-om (för hands version)
description: Ta reda på vilka ändringar som sker i resurserna innan du distribuerar en Azure Resource Manager-mall.
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: edb9f5e35008b1270031d8e2d5c8a5efa37cb554
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484173"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager-mall för att distribuera konsekvens åtgärder (för hands version)

Innan du distribuerar en mall kanske du vill förhandsgranska de ändringar som kommer att ske. Azure Resource Manager tillhandahåller åtgärden vad händer om du kan se hur resurser kommer att ändras om du distribuerar mallen. Konsekvens åtgärden gör inga ändringar i befintliga resurser. I stället förväntas ändringarna om den angivna mallen distribueras.

> [!NOTE]
> Konsekvens åtgärden är för närvarande en för hands version. Du måste [Registrera dig för för hands versionen för](https://aka.ms/armtemplatepreviews)att kunna använda den. Som en för hands version kan resultatet ibland visa att en resurs kommer att ändras när ingen ändring sker. Vi arbetar för att minska problemen, men vi behöver din hjälp. Rapportera de här problemen på [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Du kan använda åtgärden vad händer om med kommandot `New-AzDeploymentWhatIf` PowerShell eller [distributions what if](/rest/api/resources/deployments/whatif) rest.

I PowerShell ser utdata ut ungefär så här:

![Resource Manager-mall distribution av konsekvens åtgärder fullresourcepayload och ändrings typer](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Ändra typer

Åtgärden konsekvens visar sex olika typer av ändringar:

- **Skapa**: resursen finns inte för närvarande, men den har definierats i mallen. Resursen kommer att skapas.

- **Ta bort**: den här ändrings typen gäller endast när [slutfört läge](deployment-modes.md) används för distribution. Resursen finns, men har inte definierats i mallen. Med slutfört läge tas resursen bort. Endast resurser som [stöder fullständig borttagning av läge](complete-mode-deletion.md) ingår i den här ändrings typen.

- **Ignorera**: resursen finns, men har inte definierats i mallen. Resursen kommer inte att distribueras eller ändras.

- **Nochang**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras, men egenskaperna för resursen ändras inte. Den här ändrings typen returneras när [ResultFormat](#result-format) är inställt på `FullResourcePayloads`, vilket är standardvärdet.

- **Ändra**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras och egenskaperna för resursen ändras. Den här ändrings typen returneras när [ResultFormat](#result-format) är inställt på `FullResourcePayloads`, vilket är standardvärdet.

- **Distribuera**: resursen finns och definieras i mallen. Resursen kommer att omdistribueras. Egenskaperna för resursen kanske inte ändras. Åtgärden returnerar den här ändrings typen när den inte har tillräckligt med information för att avgöra om några egenskaper kommer att ändras. Du ser bara det här villkoret när [ResultFormat](#result-format) är inställt på `ResourceIdOnly`.

## <a name="deployment-scope"></a>Distributions omfång

Du kan använda åtgärden vad händer om för distributioner antingen på prenumerations-eller resurs grupps nivå. Du anger distributions omfånget med parametern `-ScopeType`. De godkända värdena är `Subscription` och `ResourceGroup`. Den här artikeln visar distributioner av resurs grupper.

Information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md#).

## <a name="result-format"></a>Resultat format

Du kan styra detalj nivån som returneras om förväntade ändringar. Ange `ResultFormat` parametern till `FullResourcePayloads` för att få en lista över resurser som kommer att ändras och information om de egenskaper som kommer att ändras. Ange `ResultFormat` parametern till `ResourceIdOnly` för att hämta en lista över resurser som kommer att ändras. Standardvärdet är `FullResourcePayloads`.  

Följande skärm bilder visar de två olika utdataformaten:

- Fullständiga resurs nytto laster

    ![Resource Manager-mall distribution av konsekvens åtgärder för fullresourcepayloads-utdata](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Endast resurs-ID

    ![Resource Manager-mall distribution av konsekvens åtgärder för resourceidonly-utdata](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Kör konsekvens Åtgärd

### <a name="set-up-environment"></a>Konfigurera miljö

För att se hur det fungerar kan vi köra vissa tester. Börja med att distribuera en mall från [Azure snabb starts mallar som skapar ett lagrings konto](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Standard lagrings konto typen är `Standard_LRS`. Du använder det här lagrings kontot för att testa hur ändringar rapporteras av vad-om.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Test ändring

När distributionen är klar är du redo att testa konsekvens åtgärden. Kör kommandot what-if men ändra lagrings konto typen till `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

Vad-om-utdata ser ut ungefär så här:

![Resource Manager-mall distribution av konsekvens åtgärder](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Observera överst i utdata som färger definieras för att ange typ av ändringar.

Längst ned i utdata visas SKU-namnet (lagrings konto typen) kommer att ändras från **Standard_LRS** till **Standard_GRS**.

Några av egenskaperna som visas som borttagna ändras inte. I föregående bild är dessa egenskaper accessTier, kryptering. käll-och andra i avsnittet. Egenskaper kan rapporteras felaktigt som borttagna när de inte finns i mallen, utan anges automatiskt under distributionen som standardvärden. Resultatet betraktas som "brus" i svaret. Den slutgiltiga distribuerade resursen kommer att ha värdena som angetts för egenskaperna. När konsekvens åtgärden mognar kommer dessa egenskaper att filtreras bort från resultatet.

### <a name="test-deletion"></a>Ta bort test

Konsekvens åtgärden stöder användning av [distributions läge](deployment-modes.md). När du har angett till slutfört läge raderas inte resurser som inte finns i mallen. I följande exempel distribueras en [mall som inte har några definierade resurser](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) i komplett läge.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Eftersom inga resurser har definierats i mallen och distributions läget är inställt på Slutför, tas lagrings kontot bort.

![Resource Manager-mall distributions läge för konsekvens distribution av utdata slutförs](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Det är viktigt att komma ihåg vad som händer om inga faktiska ändringar görs. Lagrings kontot finns kvar i resurs gruppen.

## <a name="next-steps"></a>Nästa steg

- Om du upptäcker felaktiga resultat från för hands versionen av vad-om, kan du rapportera problemen på [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Information om hur du distribuerar mallar med Azure PowerShell finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](deploy-powershell.md).
- Information om hur du distribuerar mallar med REST finns i [distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](deploy-rest.md).
- Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
