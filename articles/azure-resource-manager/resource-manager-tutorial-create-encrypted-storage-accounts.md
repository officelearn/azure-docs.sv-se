---
title: Skapa en Azure Resource Manager-mall för att distribuera ett krypterat lagringskonto | Microsoft Docs
description: Använd Visual Studio Code för att skapa en mall för att distribuera ett krypterat lagringskonto.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 57d5f7039831c9fd617926f20f3ff001b22ef314
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43097893"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Självstudie: Skapa en Azure Resource Manager-mall för att distribuera ett krypterat lagringskonto

Lär dig hur du hittar information för att slutföra en Azure Resource Manager-mall.

I den här självstudien använder du en basmall från Azure-snabbstartmallar för att skapa ett Azure Storage-konto.  Använd referensdokumentationen för mallar för att anpassa basmallen och skapa ett krypterat lagringskonto.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Förstå mallen
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/).
* Resource Manager Tools-tillägget. Information om att installera finns på sidan om att [installera tillägget för Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**>**Spara som** för att spara filen som **azuredeploy.json** till den lokala datorn.

## <a name="understand-the-format"></a>Förstå formatet

Från VS Code döljer du mallen till rotnivån. Du får den enklaste strukturen med följande element:

![Enklaste struktur för Resource Manager-mall](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: ange platsen för den JSON-schemafil som beskriver versionen av mallspråket.
* **contentVersion**: ange valfritt värde för det här elementet för att dokumentera betydande förändringar i mallen.
* **parameters** (parametrar): ange de värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen.
* **variables** (variabler): ange de värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck.
* **resources** (resurser): ange de resurstyper som distribueras eller uppdateras i en resursgrupp.
* **outputs** (utdata): ange de värden som returneras efter distributionen.

## <a name="use-parameters-in-template"></a>Använda parametrar i mallen

Med parametrar kan du anpassa distributionen genom att tillhandahålla värden som är skräddarsydda för en viss miljö. Du kan använda de parametrar som definieras i mallen när du anger värden för lagringskontot.

![Resource Manager-mallparametrar](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

I den här mallen definieras två parametrar. Lägg märke till att en mallfunktionen används i location.defaultValue:

```json
"defaultValue": "[resourceGroup().location]",
```

Funktionen resourceGroup() returnerar ett objekt som representerar den aktuella resursgruppen. En lista med mallfunktioner finns i [Azure Resource Manager-mallfunktioner](./resource-group-template-functions.md).

Så använder du de parametrar som definieras i mallen:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Använda variabler i mallen

Med variabler kan du skapa värden som kan användas i hela mallen. Variabler hjälper till att minska komplexiteten i mallarna.

![Resource Manager-mallvariabler](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Den här mallen definierar en variabel: *storageAccountName*. I definitionen används två mallfunktioner:

- **concat()**: sammanfogar strängar. Mer information finns i [concat](./resource-group-template-functions-string.md#concat).
- **uniqueString()**: skapar en deterministisk hash-sträng baserat på de värden som anges som parametrar. Varje Azure-lagringskonto måste ha ett namn som är unikt i hela Azure. Den här funktionen tillhandahåller en unik sträng. Fler strängfunktioner finns i [Strängfunktioner](./resource-group-template-functions-string.md).

Så använder du den variabel som definieras i mallen:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Redigera mallen

Målet med den här självstudien är att definiera en mall för att skapa ett krypterat lagringskonto.  Exempelmallen skapar bara ett grundläggande okrypterat lagringskonto. För att hitta den krypteringsrelaterade konfigurationen kan du använda mallreferensen för Azure Storage-kontot.

1. Bläddra till [Azure-mallar](https://docs.microsoft.com/azure/templates/).
2. Från innehållsförteckningen till vänster väljer du **Referens**->**Lagring**->**Lagringskonton**. Du kan också ange **lagring** i fältet **Filtrera efter rubrik**.  Sidan innehåller schemat för definition av information för lagringskonto.
3. Utforska den krypteringsrelaterade informationen.  
4. I egenskapselementet (properties) i resursdefinitionen för lagringskontot lägger du till följande json:

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Den här delen aktiverar krypteringsfunktionen i blob-lagringstjänsten.

Ändra mallen från Visual Studio Code, så att det slutliga resurselementet som ser ut som följer:

![Krypterade lagringskontoresurser för Resource Manager-mall](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information finns i avsnittet [Distribuera mallen](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) i Visual Studio Code-snabbstarten för distributionen.

Följande skärmbild visar CLI-kommandot för att lista det nya lagringskontot, vilket anger att kryptering har aktiverats för blob-lagringen.

![Krypterat lagringskonto för Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder mallreferensen för att anpassa en befintlig mall. Den mall som används i den här självstudien innehåller bara en Azure-resurs.  I nästa självstudie utvecklar du en mall med flera resurser. Några av resurserna har beroende resurser.

> [!div class="nextstepaction"]
> [Skapa flera resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
