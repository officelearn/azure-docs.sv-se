---
title: Skapa ett Azure App konfigurations lager med hjälp av Azure Resource Manager-mall (ARM-mall)
titleSuffix: Azure App Configuration
description: Lär dig hur du skapar ett Azure App konfigurations lager med hjälp av Azure Resource Manager-mall (ARM-mall).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 09/21/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 840f907015e9673caba46998493b5cb705de5fb7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824178"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Snabb start: skapa en Azure App konfigurations lagring med en ARM-mall

I den här snabb starten beskrivs hur du:

- Distribuera ett program konfigurations lager med ARM-mall
- Skapa nyckel värden i ett program konfigurations lager med ARM-mall
- Läs nyckel värden i ett konfigurations Arkiv för appar från ARM-mallen

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/en-us/resources/templates/101-app-configuration-store-kv/). Det skapar ett nytt konfigurations lager för appar med två nyckel värden i. Den använder sedan `reference` funktionen för att mata ut värdena för de två nyckel värde resurserna. Genom att läsa nyckelns värde på det här sättet kan det användas på andra platser i mallen.

Snabb starten använder- `copy` elementet för att skapa flera instanser av nyckel värdes resurs. Mer information om `copy` -elementet finns [i resurs upprepning i arm-mallar](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Den här mallen kräver version av resurs leverantör för program konfiguration `2020-07-01-preview` eller senare. Den här versionen använder `reference` funktionen för att läsa nyckel värden. `listKeyValue`Funktionen som användes för att läsa nyckel värden i den tidigare versionen är inte tillgänglig från och med version `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json" range="1-88" highlight="52-58,61-75,80,84":::

Två Azure-resurser definieras i mallen:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): skapa ett konfigurations lager för appar.
- Microsoft. AppConfiguration/configurationStores/nyckel värden: skapa ett nyckel värde i appens konfigurations arkiv.

> [!NOTE]
> `keyValues`Resurs namnet är en kombination av nyckel och etikett. Nyckeln och etiketten är kopplade till `$` avgränsaren. Etiketten är valfri. I exemplet ovan `keyValues` skapar resursen med namn `myKey` ett nyckel värde utan en etikett.
>
> Procent kodning, även kallat URL-kodning, tillåter att nycklar eller etiketter innehåller tecken som inte tillåts i ARM-mallens resurs namn. `%` är inte ett tillåtet värde antingen, så `~` används i sitt ställe. Om du vill koda ett namn korrekt följer du dessa steg:
>
> 1. Använd URL-kodning
> 2. Ersätt `~` med `~7E`
> 3. Ersätt `%` med `~`
>
> Om du till exempel vill skapa ett nyckel/värde-par med nyckel namn `AppName:DbEndpoint` och etikett namn `Test` bör resurs namnet vara `AppName~3ADbEndpoint$Test` .

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett konfigurations lager för appar med två nyckel värden i.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Du kan också distribuera mallen med hjälp av följande PowerShell-cmdlet. Nyckel värden visas i utdata från PowerShell-konsolen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Skriv **app Configuration**i rutan Azure Portal Sök. Välj **app-konfiguration** i listan.
1. Välj den nyligen skapade konfigurations resursen för appen.
1. Under **åtgärder**klickar du på **konfigurations Utforskaren.**
1. Kontrol lera att det finns två nyckel värden.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, konfigurations arkivet för appen och alla relaterade resurser. Om du planerar att använda appens konfigurations Arkiv i framtiden kan du hoppa över borttagningen. Om du inte kommer att fortsätta att använda den här butiken tar du bort alla resurser som skapats i den här snabb starten genom att köra följande cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du skapar andra program med Azure App konfiguration fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Snabb start: skapa en ASP.NET Core-app med Azure App konfiguration](quickstart-aspnet-core-app.md)