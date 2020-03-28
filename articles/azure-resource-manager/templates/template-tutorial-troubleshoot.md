---
title: Felsöka distributioner
description: Lär dig hur du övervakar och felsöker Azure Resource Manager-malldistributioner. Visar aktivitetsloggar och distributionshistorik.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 30b66414e87f642bc72b8723ebff57f2e9009f17
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239242"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Självstudiekurs: Felsöka distributioner av ARM-mallar

Lär dig hur du felsöker distributionsfel i Azure Resource Manager (ARM). I den här självstudien konfigurerar du två fel i en mall och lär dig hur du använder aktivitetsloggar och distributionshistoriken för att lösa problemen.

Det finns två typer av fel som rör malldistribution:

- **Valideringsfel** uppstår från scenarier som kan fastställas före distributionen. De innehåller syntaxfel i mallen eller försöker distribuera resurser som skulle överskrida prenumerationskvoterna.
- **Distributionsfel** uppstår från förhållanden som inträffar under distributionsprocessen. De omfattar försök att få åtkomst till en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som du använder för att felsöka distributionen. Båda typerna av fel visas i aktivitetsloggen. Dock visas valideringsfel inte i distributionshistoriken eftersom distributionen aldrig startades.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en problemmall
> * Felsöka valideringsfel
> * Felsöka vanliga distributionsfel
> * Rensa resurser

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio-kod med resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa ARM-mallar](use-vs-code-to-create-template.md).

## <a name="create-a-problematic-template"></a>Skapa en problemmall

Öppna en mall som heter [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto) från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/) och konfigurera två mallfel.

1. Välj **Öppna**>**fil**i Visual Studio-kod .
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Ändra raden **apiVersion** till följande rad:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** är ett ogiltigt elementnamn. Det är ett valideringsfel.
    - API-versionen ska vara ”2018-07-01”.  Det är ett distributionsfel.

5. Välj **Spara fil**>**som** om du vill spara filen som **azuredeploy.json** på den lokala datorn.

## <a name="troubleshoot-the-validation-error"></a>Felsöka valideringsfelet

Se avsnittet [Distribuera mallen](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) för distribution av mallen.

Du bör få ett fel i gränssnittet som liknar:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Felmeddelandet indikerar att problemet är med **apiVersion1**.

Använd Visual Studio Code för att åtgärda problemet genom att ändra **apiVersion1** till **apiVersion**, och spara sedan mallen.

## <a name="troubleshoot-the-deployment-error"></a>Felsöka distributionsfelet

Se avsnittet [Distribuera mallen](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) för distribution av mallen.

Du bör få ett fel i gränssnittet som liknar:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Distributionsfelet kan hittas i Azure-portalen med följande procedur:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna resursgruppen genom att välja **Resursgrupper** och sedan resursgruppens namn. Du bör se **1 Failed** (misslyckades) under **Distribution**.

    ![Självstudie om att felsöka Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Välj **Felinformation**.

    ![Självstudie om att felsöka Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Felmeddelandet är samma som det som visades tidigare:

    ![Självstudie om att felsöka Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Du kan även hitta felet i aktivitetsloggarna:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **övervakaraktivitetslogg** > **Activity log**.
3. Använd filtren för att hitta loggen.

    ![Självstudie om att felsöka Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Använd Visual Studio Code för att åtgärda problemet och omdistribuera sedan mallen.

En lista över vanliga fel finns i avsnittet om att [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien fick du lära dig felsöka distributionsfel för ARM-mall.  Mer information finns i avsnittet om att [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).
