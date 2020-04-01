---
title: Självstudiekurs - Skapa & distribuera mall
description: Skapa din första Azure Resource Manager-mall. I självstudien får du lära dig mer om syntaxen för mallfilen och hur du distribuerar ett lagringskonto.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b05bccf10ef5f273a74ca49e02162fd0408230f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411713"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Självstudiekurs: Skapa och distribuera din första ARM-mall

Den här självstudien introducerar dig till AZURE Resource Manager-mallar (ARM). Den visar hur du skapar en startmall och distribuerar den till Azure. Du får lära dig mer om mallens struktur och de verktyg du behöver för att arbeta med mallar. Det tar cirka **12 minuter** att slutföra den här självstudien, men den faktiska tiden varierar beroende på hur många verktyg du behöver installera.

Den här självstudien är den första i en serie. När du går igenom serien ändrar du startmallen steg för steg tills du har utforskat alla kärndelar i en ARM-mall. Dessa element är byggstenarna för mycket mer komplexa mallar. Vi hoppas att du i slutet av serien är säker på att skapa egna mallar och redo att automatisera dina distributioner med mallar.

Om du vill veta mer om fördelarna med att använda mallar och varför du bör automatisera distributionen med mallar läser du [Azure Resource Manager-mallar](overview.md).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="get-tools"></a>Hämta verktyg

Låt oss börja med att se till att du har de verktyg du behöver för att skapa och distribuera mallar.

### <a name="editor"></a>Redigerare

Mallar är JSON-filer. För att skapa mallar behöver du en bra JSON-redigerare. Vi rekommenderar Visual Studio-kod med tillägget Resource Manager Tools. Om du behöver installera dessa verktyg läser du [Använda Visual Studio-kod för att skapa ARM-mallar](use-vs-code-to-create-template.md).

### <a name="command-line-deployment"></a>Distribution av kommandorad

Du behöver också antingen Azure PowerShell eller Azure CLI för att distribuera mallen. Om du använder Azure CLI måste du ha den senaste versionen. Instruktioner för installationen finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)

När du har installerat Azure PowerShell eller Azure CLI kontrollerar du att du loggar in för första gången. Hjälp finns [i Logga in - PowerShell](/powershell/azure/install-az-ps#sign-in) eller [Logga in - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Okej, du är redo att börja lära dig om mallar.

## <a name="create-your-first-template"></a>Skapa din första mall

1. Öppna Visual Studio-kod med tillägget Resource Manager Tools installerat.
1. På **Arkiv-menyn** väljer du **Ny fil** för att skapa en ny fil.
1. Välj **Spara som**på **Arkiv-menyn** .
1. Namnge filen **azuredeploy** och välj JSON-filtillägget. **JSON** Det fullständiga namnet på filen **azuredeploy.json**.
1. Spara filen på arbetsstationen. Välj en sökväg som är lätt att komma ihåg eftersom du anger sökvägen senare när du distribuerar mallen.
1. Kopiera och klistra in följande JSON i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Så här ser vs-kodmiljön ut:

    ![Visual Studio-mall för Visual Studio-kod för Visual Studio-mall i Resource Manager](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Den här mallen distribuerar inga resurser. Vi börjar med en tom mall så att du kan bekanta dig med stegen för att distribuera en mall samtidigt som du minimerar risken för att något går fel.

    JSON-filen har följande element:

    - **$schema**: Anger platsen för JSON-schemafilen. Schemafilen beskriver de egenskaper som är tillgängliga i en mall. Schemat definierar till exempel **resurser** som en av de giltiga egenskaperna för en mall. Oroa dig inte för att datumet för schemat är 2019-04-01. Den här schemaversionen är uppdaterad och innehåller alla de senaste funktionerna. Schemadatumet har inte ändrats eftersom det inte har gjorts några avbrottsändringar sedan introduktionen.
    - **contentVersion**: Anger versionen av mallen (till exempel 1.0.0.0). Du kan ange vilket värde som helst för det här elementet. Använd det här värdet om du vill dokumentera betydande ändringar i mallen. När du distribuerar resurser med hjälp av mallen kan det här värdet användas för att se till att rätt mall används.
    - **resurser**: Innehåller de resurser som du vill distribuera eller uppdatera. För närvarande är den tom, men du lägger till resurser senare.

1. Spara filen.

Grattis, du har skapat din första mall.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill börja arbeta med Azure PowerShell/Azure CLI loggar du in med dina Azure-autentiseringsuppgifter.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Skapa resursgrupp

När du distribuerar en mall anger du en resursgrupp som ska innehålla resurserna. Innan du kör distributionskommandot skapar du resursgruppen med antingen Azure CLI eller Azure PowerShell. Välj flikarna i följande kodavsnitt för att välja mellan Azure PowerShell och Azure CLI. CLI-exemplen i den här artikeln är skrivna för Bash-skalet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera mallen använder du antingen Azure CLI eller Azure PowerShell. Använd den resursgrupp som du skapade. Ge distributionen ett namn så att du enkelt kan identifiera den i distributionshistoriken. För enkelhetens skull kan du också skapa en variabel som lagrar sökvägen till mallfilen. Den här variabeln gör det enklare för dig att köra distributionskommandona eftersom du inte behöver skriva om sökvägen varje gång du distribuerar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Distributionskommandot returnerar resultat. Leta `ProvisioningState` efter om distributionen lyckades.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Etableringstillstånd för PowerShell-distribution](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Etableringstillstånd för Azure CLI-distribution](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper**på den vänstra menyn .

1. Välj den resursgrupp som distribueras i den sista proceduren. Standardnamnet är **myResourceGroup**. Du får inte se någon resurs som distribueras inom resursgruppen.

1. Observera längst upp till höger i översikten, status för distributionen visas. Välj **1 lyckades**.

   ![Visa distributionsstatus](./media/template-tutorial-create-first-template/deployment-status.png)

1. Du ser en historik över distributionen för resursgruppen. Välj **blanktemplate**.

   ![Välj distribution](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Du ser en sammanfattning av distributionen. I det här fallet finns det inte mycket att se eftersom inga resurser har distribuerats. Senare i den här serien kan det vara bra att granska sammanfattningen i distributionshistoriken. Meddelande till vänster kan du visa indata, utdata och mallen som används under distributionen.

   ![Visa distributionssammanfattning](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du har skapat en enkel mall som ska distribueras till Azure. I nästa självstudiekurs ska du lägga till ett lagringskonto i mallen och distribuera det till resursgruppen.

> [!div class="nextstepaction"]
> [Lägg till resurs](template-tutorial-add-resource.md)
