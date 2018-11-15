---
title: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig hur du distribuerar tillägg för virtuell dator med Azure Resource Manager-mallar
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2f5cffa0b31d46d76564565001aaed678eb1a649
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613604"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar

Lär dig hur du använder [Azure-tillägg för virtuell dator](../virtual-machines/extensions/features-windows.md) för att utföra konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer. Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. I den här självstudien distribuerar du ett anpassat skripttillägg från en Resource Manager-mall för att köra ett PowerShell-skript på en virtuell Windows-dator.  Skriptet installerar webbserver på den virtuella datorn.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda ett PowerShell-skript
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med verktygstillägget för Resource Manager.  Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-powershell-script"></a>Förbereda ett PowerShell-skript

Ett PowerShell-skript med följande innehåll delas från ett [Azure Storage-konto med offentlig åtkomst](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Om du väljer att publicera filen till din egen plats måste du uppdatera [fileUri]-elementet i mallen senare under självstudien.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Det finns fem resurser som definieras av mallen:

    * `Microsoft.Storage/storageAccounts`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
5. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="edit-the-template"></a>Redigera mallen

Lägg till en resurs för tillägg för virtuell dator i den befintliga mallen med följande innehåll:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Se [tilläggsreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions) om du behöver mer information om den här resursdefinitionen. Här följer några viktiga element:

* **name** (namn): eftersom tilläggsresursen är en underordnad resurs för det virtuella datorobjektet måste namnet ha den virtuella datorns namnprefix. Se [Underordnade resurser](./resource-manager-templates-resources.md#child-resources).
* **dependsOn**: tilläggsresursen måste skapas efter att den virtuella datorn har skapats.
* **fileUris**: det här är de platser där skriptfilerna lagras. Om du väljer att inte använda det som angavs måste du uppdatera värdena.
* **commandToExecute**: det här är kommandot för att anropa skriptet.  

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Det rekommenderas att du använder ett genererat lösenord för den virtuella datorns administratörskonto. Se [Förutsättningar](#prerequisites).

## <a name="verify-the-deployment"></a>Verifiera distributionen

I portalen väljer du den virtuella datorn, och i översikten för den virtuella datorn använder du **Klicka för att kopiera** till höger om IP-adressen för att kopiera och klistra in den på en webbläsarflik. Standardvälkomstsidan för IIS öppnas och bör se ut så här:

![Azure Resource Manager distribuerar IIS-webbserver med kundskript för VM-tillägg](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en virtuell dator och ett tillägg för virtuell dator. Tillägget installerade IIS-webbservern på den virtuella datorn. Information om hur du använder SQL Database-tillägget för att importera en BACPAC-fil finns här:

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
