---
title: Använd mallreferens
description: Använd mallreferensen för Azure Resource Manager för att skapa en mall.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b713d508a5e28291778d3727c15e12972eea3a77
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878529"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>Självstudiekurs: Använd mallreferensen för Resurshanteraren

Lär dig hur du hittar mallschemainformationen och använder informationen för att skapa ARM-mallar (Azure Resource Manager).

I den här självstudien använder du en basmall från Azure-snabbstartmallar. Med hjälp av mallreferensdokumentation anpassar du mallen.

![Resurshanterarens referensreferens distribuera lagringskonto](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Förstå mallen
> * Leta upp mallreferensen
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio-kod med resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa ARM-mallar](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

[Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/) är en lagringsplats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Välj **Öppna**>**fil**i Visual Studio-kod .
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
1. Välj **Spara fil**>**som** om du vill spara filen som **azuredeploy.json** på den lokala datorn.

## <a name="understand-the-schema"></a>Förstå schemat

1. Från VS Code döljer du mallen till rotnivån. Du får den enklaste strukturen med följande element:

    ![Enklaste struktur för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: ange platsen för den JSON-schemafil som beskriver versionen av mallspråket.
    * **contentVersion**: ange valfritt värde för det här elementet för att dokumentera betydande förändringar i mallen.
    * **parameters** (parametrar): ange de värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen.
    * **variables** (variabler): ange de värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck.
    * **resources** (resurser): ange de resurstyper som distribueras eller uppdateras i en resursgrupp.
    * **outputs** (utdata): ange de värden som returneras efter distributionen.

1. Expandera **resurser**. Det finns en `Microsoft.Storage/storageAccounts`-resurs som definierats. SKU-namnet använder ett parametervärde.  Parametern kallas **storageAccountType**.

    ![Definition av lagringskonto för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Expandera **parametrar för** att se hur **storageAccountType** definieras. Parametern har fyra tillåtna värden. Du hittar de andra tillåtna värdena och ändrar sedan parameterdefinitionen.

    ![Resurser för lagringskonto för Resurshanterarens mallkonto](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Leta upp mallreferensen

1. Bläddra till [Azure-mallreferens](https://docs.microsoft.com/azure/templates/).
1. I rutan **Filtrera efter rubrik** anger du **lagringskonton**och väljer de första **lagringskontona** under **Referens > Lagring**.

    ![Resource Manager, mallreferens, lagringskonto](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    En resursprovider har vanligtvis flera API-versioner:

    ![Referenslagringskontoversioner för Resurshanterarens mallreferens](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Välj **Alla resurser** under **Lagring** i den vänstra rutan. På den här sidan visas resurstyper och versioner av lagringsresursleverantören. Vi rekommenderar att du använder de senaste API-versionerna för de resurstyper som definierats i mallen.

    ![Resurshanterarens referenslagringskontotyper versioner](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Välj den senaste versionen av **resurstypen storageAccount.** Den senaste versionen är **2019-06-01** när den här artikeln är skriven. Kontrollera att den här versionen matchar den version som används för lagringskontoresursen i mallen. Om du uppdaterar API-versionen kontrollerar du att resursdefinitionen matchar mallreferensen.

1. På den här sidan visas information om resurstypen storageAccount.  Den visar till exempel de tillåtna värdena för **Sku-objektet.** Det finns fler skus än vad som anges i snabbstartsmallen som du öppnade tidigare. Du kan anpassa snabbstartsmallen så att den innehåller alla tillgängliga lagringstyper.

    ![Resurshanterarens referenslagringskonto för referenslagring](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Redigera mallen

Från Visual Studio Code lägger du till ytterligare lagringskontotyper som visas i följande skärmbild:

![Resurser för lagringskonto för Resurshanteraren-mall](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information finns i avsnittet [Distribuera mallen](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) i Visual Studio Code-snabbstarten för distributionen. När du distribuerar mallen anger du parametern **storageAccountType** med ett nytt mervärde, till exempel **Premium_ZRS**. Distributionen misslyckas om du använder den ursprungliga snabbstartsmallen eftersom **Premium_ZRS** inte var ett tillåtet värde.  Om du vill skicka parametervärdet lägger du till följande växel i distributionskommandot:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
--parameters storageAccountType='Premium_ZRS'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
-storageAccountType "Premium_ZRS"
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder mallreferensen för att anpassa en befintlig mall. Du hittar mer information om att skapa flera instanser av lagringskonton i:

> [!div class="nextstepaction"]
> [Skapa flera instanser](./template-tutorial-create-multiple-instances.md)
