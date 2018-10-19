---
title: Skapa länkade Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig att skapa länkade Azure Resource Manager-mallar för att skapa virtuella datorer
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d25ca14b78465a6c4fec7e90bc20436e3ca553fc
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419635"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Självstudie: Skapa länkade Azure Resource Manager-mallar

Lär dig att skapa länkade Azure Resource Manager-mallar. Med hjälp av länkade mallar kan du få en mall att anropa en annan. Det är perfekt för modularisering av mallar. I den här självstudien använder du samma mall som används i [Självstudie: Skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md), vilket skapar en virtuell dator, ett virtuellt nätverk och andra beroende resurser, inklusive ett lagringskonto. Du separerar lagringskontoresursen till en länkad mall.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Skapa den länkade mallen
> * Ladda upp den länkade mallen
> * Länka till den länkade mallen
> * Konfigurera beroende
> * Hämta värden från den länkade mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/).
* Resource Manager Tools-tillägget.  Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Slutför [Självstudie: Skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator). Detta är samma mall som används i [Självstudie: Skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md). Du sparar två kopior av samma mall som ska användas som:

- **Huvudmallen**: Skapa alla resurser förutom lagringskontot.
- **Den länkade mallen**: Skapa lagringskontot.

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.
5. Välj **Fil**>**Spara som** för att skapa en annan kopia av filen med namnet **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Skapa den länkade mallen

Den länkade mallen skapar ett lagringskonto. Den länkade mallen är nästan identisk med den fristående mallen som skapar ett lagringskonto. I den här självstudien behöver den länkade mallen skicka ett värde tillbaka till huvudmallen. Det här värdet definieras i elementet `outputs`.

1. Öppna linkedTemplate.json i Visual Studio Code om den inte är öppen.
2. Gör följande ändringar:

    - Ta bort alla resurser förutom lagringskontot. Du tar bort totalt fyra resurser.
    - Uppdatera elementet **outputs**, så det ser ut så här:

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        **storageUri** krävs av VM-resursdefinitionen i huvudmallen.  Du skickar tillbaka värdet till huvudmallen som ett utdatavärde.
    - Ta bort de parametrar som aldrig används. Dessa parametrar har en grön våglinje under sig. Du ska bara ha en parameter kvar med namnet **location**.
    - Ta bort elementet för **variables**. De behövs inte i den här självstudien.
    - Lägg till en parameter med namnet **storageAccountName**. Lagringskontonamnet skickas från huvudmallen till den länkade mallen som en parameter.

    När du är klar ska mallen se ut så här:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Spara ändringarna.

## <a name="upload-the-linked-template"></a>Ladda upp den länkade mallen

Mallarna måste vara tillgängliga från där du kör distributionen. Den här platsen kan vara ett Azure-lagringskonto, Github eller Dropbox. Om dina mallar innehåller känslig information ska du vara noga med att skydda åtkomsten till dem. I den här självstudien använder du den Cloud Shell-distributionsmetod som du använde i [Självstudie: Skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md). Huvudmallen (azuredeploy.json) laddas upp till gränssnittet. Den länkade mallen (linkedTemplate.json) måste delas någonstans.  För att minska antalet uppgifter i den här självstudien, har den länkade mall som definierades i föregående stycke laddats upp till [ett Azure-lagringskonto](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Anropa den länkade mallen

Huvudmallen heter azuredeploy.json.

1. Öppna azuredeploy.json i Visual Studio Code om den inte är öppen.
2. Ta bort lagringskontots resursdefinition från mallen.
3. Lägg till följande json-kodfragment till den plats där du har lagringskontodefinitionen:

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Ta hänsyn till följande information:

    - En `Microsoft.Resources/deployments`-resurs i huvudmallen används för att länka till en annan mall.
    - `deployments`-resursen har namnet `linkedTemplate`. Det här namnet används för [ konfigurering av beroende](#configure-dependency).  
    - Du kan bara använda läget för [stegvis](./deployment-modes.md) distribution när du anropar länkade mallar.
    - `templateLink/uri` innehåller den länkade mallens URI. Den länkade mallen har laddats upp till ett delat lagringskonto. Du kan uppdatera URI:n om du laddar upp mallen till en annan plats på Internet.
    - Använd `parameters` för att skicka värden från huvudmallen till den länkade mallen.
4. Spara ändringarna.

## <a name="configure-dependency"></a>Konfigurera beroende

Som du minns från [Självstudie: Skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md), är den virtuella datorresursen beroende av lagringskontot:

![Beroendediagram för Azure Resource Manager-mallar](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Eftersom lagringskontot är definierat i den länkade mallen nu, måste du uppdatera följande två element i resursen `Microsoft.Compute/virtualMachines`.

- Konfigurera om elementet `dependOn`. Lagringskontodefinitionen flyttas till den länkade mallen.
- Konfigurera om elementet `properties/diagnosticsProfile/bootDiagnostics/storageUri`. I [Skapa den länkade mallen](#create-the-linked-template) lade du till ett utdatavärde:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Det här värdet krävs av huvudmallen.

1. Öppna azuredeploy.json i Visual Studio Code om den inte är öppen.
2. Expandera den virtuella datorns resursdefinition och uppdatera **dependsOn** enligt följande skärmbild:

    ![Azure Resource Manager-länkade mallar konfigurerar beroende ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)
    
    ”linkedTemplate” är namnet på distributionsresursen.  
3. Uppdatera **properties/diagnosticsProfile/bootDiagnostics/storageUri** som visas på föregående skärmbild.

Mer information finns i [Använda länkade och nästlade mallar vid distribution av Azure-resurser](./resource-group-linked-templates.md).

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I de här självstudien kommer du att utveckla och distribuera länkade mallar. Mer information om att distribuera Azure-resurser i flera regioner, och om att använda säker distributionspraxis, finns i


> [!div class="nextstepaction"]
> [Använda Distributionshanteraren i Azure](./deployment-manager-tutorial.md)

