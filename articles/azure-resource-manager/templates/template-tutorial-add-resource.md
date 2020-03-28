---
title: Självstudiekurs - Lägg till resurs i mallen
description: I artikeln beskrivs hur du skapar din första Azure Resource Manager-mall. Du lär dig mer om mallfilsyntaxen och hur du distribuerar ett lagringskonto.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 79e337b411f9d115d93050ebeee346a526913d39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371739"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Självstudiekurs: Lägga till en resurs i ARM-mallen

I föregående [självstudie har](template-tutorial-create-first-template.md)du lärt dig hur du skapar en tom mall och distribuerar den. Nu är du redo att distribuera en verklig resurs. I den här självstudien lägger du till ett lagringskonto. Det tar cirka **9 minuter** att slutföra den här guiden.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför den [inledande självstudien om mallar,](template-tutorial-create-first-template.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Lägg till resurs

Om du vill lägga till en lagringskontodefinition i den befintliga mallen tittar du på den markerade JSON i följande exempel. I stället för att försöka kopiera delar av mallen kopierar du hela filen och ersätter mallen med dess innehåll.

Ersätt **{provide-unique-name}** med ett unikt lagringskontonamn.

> [!IMPORTANT]
> Namnet på lagringskontot måste vara unikt i Azure. Namnet får bara ha gemener eller siffror. Det kan inte vara längre än 24 tecken. Du kan prova ett namngivningsmönster som att använda **store1** som prefix och sedan lägga till dina initialer och dagens datum. Namnet du använder kan till exempel se ut som **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Det är inte enkelt att gissa ett unikt namn för ett lagringskonto och fungerar inte bra för att automatisera stora distributioner. Senare i den här självstudieserien använder du mallfunktioner som gör det enklare att skapa ett unikt namn.

## <a name="resource-properties"></a>Resursegenskaper

Du kanske undrar hur du hittar de egenskaper som ska användas för varje resurstyp. Du kan använda [ARM-mallreferensen](/azure/templates/) för att hitta de resurstyper som du vill distribuera.

Varje resurs som du distribuerar har minst följande tre egenskaper:

- **typ**: Typ av resurs. Det här värdet är en kombination av resursproviderns namnområde och resurstypen (till exempel Microsoft.Storage/storageAccounts).
- **apiVersion**: Version av REST API som ska användas för att skapa resursen. Varje resursprovider publicerade sina egna API-versioner, så det här värdet är specifikt för typen.
- **namn**: Resursens namn.

De flesta resurser **location** har också en platsegenskap som anger den region där resursen distribueras.

De andra egenskaperna varierar beroende på resurstyp och API-version. Det är viktigt att förstå sambandet mellan API-versionen och de tillgängliga egenskaperna, så låt oss hoppa in mer i detalj.

I den här självstudien har du lagt till ett lagringskonto i mallen. Du kan se den API-versionen på [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Observera att du inte har lagt till alla egenskaper i mallen. Många av egenskaperna är valfria. Microsoft.Storage-resursprovidern kan släppa en ny API-version, men den version du distribuerar behöver inte ändras. Du kan fortsätta använda den versionen och veta att resultatet av distributionen kommer att vara konsekventa.

Om du visar en äldre API-version, till exempel [storageAccounts 2016-05-01,](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)ser du att en mindre uppsättning egenskaper är tillgängliga.

Om du bestämmer dig för att ändra API-versionen för en resurs kontrollerar du att du utvärderar egenskaperna för den versionen och justerar mallen på rätt sätt.

## <a name="deploy-template"></a>Distribuera mallen

Du kan distribuera mallen för att skapa lagringskontot. Ge distributionen ett annat namn så att du enkelt kan hitta den i historiken.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Två möjliga distributionsfel som kan uppstå:

- Fel: Code=AccountNameInvalid; Message={provide-unique-name} är inte ett giltigt lagringskontonamn. Lagringskontonamnet får vara mellan 3 och 24 tecken långa och endast med siffror och gemener.

    Ersätt **{provide-unique-name}** i mallen med ett unikt lagringskontonamn.  Se [Lägga till resurs](#add-resource).

- Fel: Code=StorageAccountAlreadyTaken; Message=Lagringskontot store1abc09092019 är redan taget.

    Prova ett annat lagringskontonamn i mallen.

Den här distributionen tar längre tid än din tomma malldistribution eftersom lagringskontot skapas. Det kan ta ungefär en minut men är oftast snabbare.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Välj den resursgrupp som du har distribuerat till.
1. Du ser att ett lagringskonto har distribuerats.
1. Observera att distributionsetiketten nu säger: **Distributioner: 2 Lyckades**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du har skapat en enkel mall för att distribuera ett Azure-lagringskonto.  I de senare självstudierna får du lära dig hur du lägger till parametrar, variabler, resurser och utdata i en mall. Dessa funktioner är byggstenarna för mycket mer komplexa mallar.

> [!div class="nextstepaction"]
> [Lägga till parametrar](template-tutorial-add-parameters.md)