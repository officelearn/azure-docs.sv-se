---
title: Skapa Azure Resource Manager-mallar med beroende resurser | Microsoft Docs
description: Lär dig hur du skapar en Azure Resource Manager-mall med flera resurser samt hur du distribuerar den med hjälp av Azure-portalen
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
ms.openlocfilehash: fe6313c059a1dd1050240ead5f7ca8e3e1512aa6
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584521"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Självstudie: Skapa Azure Resource Manager-mallar med beroende resurser

Lär dig hur du skapar en Azure Resource Manager-mall för att distribuera flera resurser.  När du har skapat mallen distribuerar du mallen med hjälp av Cloud Shell från Azure-portalen.

I den här självstudien skapar du ett lagringskonto, en virtuell dator, ett virtuellt nätverk och några andra beroende resurser. Vissa resurser kan inte distribueras förrän en annan resurs finns. Till exempel kan du inte skapa den virtuella datorn förrän dess lagringskonto och nätverksgränssnitt finns. Du kan definiera den här relationen genom att göra en resurs beroende av de andra resurserna. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Mer information finns på sidan om att [definiera ordningen för distribution av resurser i Azure Resource Manager-mallar](./resource-group-define-dependencies.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Utforska mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/).
* Resource Manager Tools-tillägget.  Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="explore-the-template"></a>Utforska mallen

När du utforskar mallen i det här avsnittet kan du försöka besvara följande frågor:

- Hur många Azure-resurser är definierade i den här mallen?
- En av resurserna är ett Azure-lagringskonto.  Ser definitionen ut som den som används i den senaste självstudien?
- Kan du hitta mallreferenserna för de resurser som är definierade i mallen?
- Kan du hitta resursernas beroenden?

1. Från Visual Studio Code döljer du elementen tills du bara ser elementen på den första och andra nivån i **resurser**:

    ![Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Det finns fem resurser som definieras i mallen.
2. Expandera den första resursen. Det är ett lagringskonto. Definitionen ska vara identisk med den som används i början av den senaste självstudien.

    ![Lagringsdefinition för Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Expandera den andra resursen. Resurstypen är **Microsoft.Network/publicIPAddresses**. Du hittar mallreferensen genom att bläddra till [mallreferens](https://docs.microsoft.com/azure/templates/) och ange **offentlig ip-adress** eller **offentliga ip-adresser** i fältet **Filtrera efter rubrik**. Jämför resursdefinitionen med mallreferensen.

    ![Definition av offentlig IP-adress för Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Upprepa det senaste steget för att leta rätt på mallreferenserna för de övriga resurser som är definierade i mallen.  Jämför resursdefinitionerna med mallreferenserna.
5. Expandera den fjärde resursen:

    ![Azure Resource Manager-mallar i Visual Studio Code – dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Med elementet dependsOn kan du definiera en resurs som beroende på en eller flera resurser. I det här exemplet är den här resursen ett networkInterface.  Den beror på två resurser:

    * publicIPAddress
    * virtualNetwork

6. Expandera den femte resursen. Den här resursen är en virtuell dator. Den beror på två resurser:

    * storageAccount
    * networkInterface

Följande diagram illustrerar resurserna och beroendeinformation för den här mallen:

![Azure Resource Manager-mallar i Visual Studio Code – beroendediagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Genom att ange beroendena distribuerar Resource Manager effektivt lösningen. Den distribuerar lagringskontot, en offentlig IP-adress och ett virtuellt nätverk parallellt eftersom de inte har några beroenden. När den offentliga IP-adressen och det virtuella nätverket har distribuerats skapas ett nätverksgränssnitt. När alla andra resurser har distribuerats så distribuerar Resource Manager den virtuella datorn.

## <a name="deploy-the-template"></a>Distribuera mallen

Det finns många metoder för att distribuera mallar.  I den här självstudien använder du Cloud Shell från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com)
2. Välj **Cloud Shell** från det övre högra hörnet enligt följande bild:

    ![Azure portal Cloud shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Välj **PowerShell** från det övre vänstra hörnet i Cloud Shell.  Du använder PowerShell i den här självstudien.
4. Välj **Starta om**
5. Välj **Ladda upp fil** från Cloud Shell:

    ![Azure portal Cloud shell upload file](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Välj den fil som du sparade tidigare i självstudien. Standardnamnet är **azuredeploy.json**.  Om du har en fil med samma namn kommer den gamla filen att skrivas över utan något meddelande.
7. Från Cloud Shell kör du följande kommando för att kontrollera att filen har laddats upp. 

    ```shell
    ls
    ```

    ![Azure portal Cloud shell list file](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Det filnamn som visas på skärmbilden är azuredeploy.json.

8. Från Cloud Shell kör du följande kommando för att verifiera innehållet i JSON-filen:

    ```shell
    cat azuredeploy.json
    ```
9. Från Cloud Shell kör du följande PowerShell-kommandon:

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Här är skärmbilden för en exempeldistribution:

    ![Azure portal Cloud shell deploy template](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    På skärmbilden används de här värdena:

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Mallparametrar**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Kör följande PowerShell-kommando för att visa den nyligen skapade virtuella datorn:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    Namnet på den virtuella datorn är hårdkodat som **SimpleWinVM** i mallen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien utvecklar och distribuerar du en mall för att skapa en virtuell dator, ett virtuellt nätverk och de beroende resurserna. Information om hur du lär dig att distribuera Azure-resurser baserat på villkor finns i:


> [!div class="nextstepaction"]
> [Använda villkor](./resource-manager-tutorial-use-conditions.md)

