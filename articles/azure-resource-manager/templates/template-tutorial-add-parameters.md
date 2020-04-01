---
title: Självstudiekurs - lägg till parametrar i mallen
description: Lägg till parametrar i azure Resource Manager-mallen för att göra den återanvändbar.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de7ec961672db2f3120e00f1a42b33f71e7ab092
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437820"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Självstudiekurs: Lägg till parametrar i ARM-mallen

I föregående [självstudie har](template-tutorial-add-resource.md)du lärt dig hur du lägger till ett lagringskonto i mallen och distribuerar det. I den här självstudien får du lära dig hur du förbättrar AZURE Resource Manager (ARM) genom att lägga till parametrar. Den här självstudien tar cirka **14 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om resurser,](template-tutorial-add-resource.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

I slutet av föregående självstudie hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Du kanske har märkt att det finns ett problem med den här mallen. Lagringskontonamnet är hårdkodat. Du kan bara använda den här mallen för att distribuera samma lagringskonto varje gång. Om du vill distribuera ett lagringskonto med ett annat namn måste du skapa en ny mall, vilket uppenbarligen inte är ett praktiskt sätt att automatisera distributionerna.

## <a name="make-template-reusable"></a>Gör mallen återanvändbar

Om du vill göra mallen återanvändbar ska vi lägga till en parameter som du kan använda för att skicka in ett lagringskontonamn. Den markerade JSON i följande exempel visar vad som har ändrats i mallen. Parametern **storageName** identifieras som en sträng. Maxlängden är inställd på 24 tecken för att förhindra namn som är för långa.

Kopiera hela filen och ersätt mallen med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Distribuera mallen

Nu distribuerar vi mallen. I följande exempel distribueras mallen med Azure CLI eller PowerShell. Observera att du anger lagringskontonamnet som ett av värdena i distributionskommandot. Ange samma namn som du använde i föregående självstudie för lagringskontots namn.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Förstå resursuppdateringar

I föregående avsnitt har du distribuerat ett lagringskonto med samma namn som du hade skapat tidigare. Du kanske undrar hur resursen påverkas av omfördelningen.

Om resursen redan finns och ingen ändring identifieras i egenskaperna vidtas ingen åtgärd. Om resursen redan finns och en egenskap har ändrats uppdateras resursen. Om resursen inte finns skapas den.

Det här sättet att hantera uppdateringar innebär att mallen kan innehålla alla resurser du behöver för en Azure-lösning. Du kan distribuera om mallen på ett säkert sätt och veta att resurser bara ändras eller skapas när det behövs. Om du till exempel har lagt till filer i ditt lagringskonto kan du distribuera om lagringskontot utan att förlora dessa filer.

## <a name="customize-by-environment"></a>Anpassa efter miljö

Med parametrar kan du anpassa distributionen genom att tillhandahålla värden som är skräddarsydda för en viss miljö. Du kan till exempel klara olika värden baserat på om du distribuerar till en miljö för utveckling, test och produktion.

Den tidigare mallen har alltid distribuerat ett Standard_LRS lagringskonto. Du kanske vill ha flexibiliteten att distribuera olika SKU:er beroende på miljö. I följande exempel visas de ändringar som ska läggas till en parameter för SKU. Kopiera hela filen och klistra in över mallen.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

**Parametern storageSKU** har ett standardvärde. Det här värdet används när ett värde inte anges under distributionen. Den har också en lista över tillåtna värden. Dessa värden matchar de värden som behövs för att skapa ett lagringskonto. Du vill inte att användare av mallen ska passera i SKU:er som inte fungerar.

## <a name="redeploy-template"></a>Distribuera om mallen

Du är redo att sätta in igen. Eftersom standard-SKU:n är inställd **på Standard_LRS**behöver du inte ange något värde för den parametern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

Om du vill se flexibiliteten i mallen ska vi distribuera igen. Den här gången ställer SKU-parametern till **Standard_GRS**. Du kan antingen skicka in ett nytt namn för att skapa ett annat lagringskonto eller använda samma namn för att uppdatera ditt befintliga lagringskonto. Båda alternativen fungerar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Slutligen ska vi köra ytterligare ett test och se vad som händer när du passerar i en SKU som inte är en av de tillåtna värdena. I det här fallet testar vi scenariot där en användare av mallen tycker **att grundläggande** är en av de SKU: er.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Kommandot misslyckas omedelbart med ett felmeddelande som anger vilka värden som tillåts. Resource Manager identifierar felet innan distributionen startar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du har förbättrat mallen som skapades i den [första självstudien](template-tutorial-create-first-template.md) genom att lägga till parametrar. I nästa självstudiekurs får du lära dig mer om mallfunktioner.

> [!div class="nextstepaction"]
> [Lägga till mallfunktioner](template-tutorial-add-functions.md)