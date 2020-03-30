---
title: Skapa logikappmallar för distribution
description: Lär dig hur du skapar Azure Resource Manager-mallar för automatisering av distribution i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972635"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Skapa Azure Resource Manager-mallar för att automatisera distributionen för Azure Logic Apps

I den här artikeln beskrivs hur du kan skapa en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md) för logikappen för logikappen för att automatisera hur du kan automatisera skapandet och distribuera logikappen. En översikt över strukturen och syntaxen för en mall som innehåller arbetsflödesdefinitionen och andra resurser som krävs för distribution finns i [Översikt: Automatisera distributionen för logikappar med Azure Resource Manager-mallar](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps tillhandahåller en [fördefinierad logikapp Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) som du kan återanvända, inte bara för att skapa logikappar, utan också för att definiera de resurser och parametrar som ska användas för distribution. Du kan använda den här mallen för dina egna affärsscenarier eller anpassa mallen så att den uppfyller dina krav.

> [!IMPORTANT]
> Kontrollera att anslutningar i mallen använder samma Azure-resursgrupp och plats som logikappen.

Mer information om Azure Resource Manager-mallar finns i följande avsnitt:

* [Mallstruktur och syntax för Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Författare Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
* [Utveckla Azure Resource Manager-mallar för molnkonsekvens](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Skapa mallar med Visual Studio

Om du vill skapa giltiga parameteriserade logikappmallar som oftast är klara för distribution använder du Visual Studio (kostnadsfri Community-utgåva eller mer) och Azure Logic Apps Tools för Visual Studio. Du kan sedan antingen [skapa logikappen i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) eller hitta och ladda ned en befintlig [logikapp från Azure-portalen till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Genom att ladda ned logikappen får du en mall som innehåller definitionerna för logikappen och andra resurser, till exempel anslutningar. Mallen *parameteriserar*också eller definierar parametrar för de värden som används för att distribuera logikappen och andra resurser. Du kan ange värdena för dessa parametrar i en separat parameterfil. På så sätt kan du enklare ändra dessa värden baserat på dina distributionsbehov. Mer information finns i de här ämnena:

* [Skapa logikappar med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Hantera Logic Apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Skapa mallar med Azure PowerShell

Du kan skapa Resource Manager-mallar med hjälp av Azure PowerShell med [LogicAppTemplate-modulen](https://github.com/jeffhollan/LogicAppTemplateCreator). Den här modulen med öppen källkod utvärderar först logikappen och alla anslutningar som logikappen använder. Modulen genererar sedan mallresurser med nödvändiga parametrar för distribution.

Anta till exempel att du har en logikapp som tar emot ett meddelande från en Azure Service Bus-kö och överför data till en Azure SQL-databas. Modulen bevarar all orchestration logik och parameteriserar SQL och Service Bus anslutningssträngar så att du kan tillhandahålla och ändra dessa värden baserat på dina distributionsbehov.

De här exemplen visar hur du skapar och distribuerar logikappar med hjälp av Azure Resource Manager-mallar, Azure Pipelines i Azure DevOps och Azure PowerShell:

* [Exempel: Ansluta till Azure Service Bus-köer från Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Ansluta till Azure Storage-konton från Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en funktionsappåtgärd för Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Ansluta till ett integrationskonto från Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Installera PowerShell-moduler

1. Om du inte redan har gjort det installerar du [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Om du vill installera LogicAppTemplate-modulen från [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate)kan du köra det här kommandot:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Om du vill uppdatera till den senaste versionen kör du det här kommandot:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Om du vill installera manuellt följer du stegen i GitHub för [Logikappmallskapare](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Installera Azure Resource Manager-klient

För att LogicAppTemplate-modulen ska fungera med alla Azure-klient- och prenumerationsåtkomsttoken installerar du [Azure Resource Manager-klientverktyget](https://github.com/projectkudu/ARMClient), som är ett enkelt kommandoradsverktyg som anropar Azure Resource Manager API.

När du `Get-LogicAppTemplate` kör kommandot med det här verktyget får kommandot först en åtkomsttoken via ARMClient-verktyget, skickar token till PowerShell-skriptet och skapar mallen som en JSON-fil. Mer information om verktyget finns i den här [artikeln om Azure Resource Manager-klientverktyget](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generera mall med PowerShell

Om du vill generera mallen när du har installerat LogicAppTemplate-modulen och [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)kör du det här PowerShell-kommandot:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Om du vill följa rekommendationen för rörledningar i en token `$SubscriptionId` från Azure Resource [Manager-klientverktyget](https://github.com/projectkudu/ARMClient)kör du det här kommandot i stället där ditt Azure-prenumerations-ID:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Efter extrahering kan du sedan skapa en parameterfil från mallen genom att köra det här kommandot:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Kör det här kommandot för extrahering med Azure Key Vault-referenser (endast statiskt):

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametrar | Krävs | Beskrivning |
|------------|----------|-------------|
| TemplateFile | Ja | Filsökvägen till mallfilen |
| KeyVault | Inga | En uppräkning som beskriver hur du hanterar möjliga nyckelvalvvärden. Standardvärdet är `None`. |
||||

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera mallar för logikappar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
