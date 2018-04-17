---
title: Skapa en virtuell dator med flera nätverkskort – Azure Resource Manager-mall | Microsoft Docs
description: Skapa en virtuell dator med flera nätverkskort med en Azure Resource Manager-mall.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Skapa en virtuell dator med flera nätverkskort med en mall
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md).  Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för [den klassiska distributionsmodellen](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Följande steg använder en resursgrupp med namnet *IaaSStory* för webbservrar och en resursgrupp med namnet *IaaSStory BackEnd* för DB-servrar.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa DB-servrar, måste du skapa den *IaaSStory* resursgrupp med alla nödvändiga resurser för det här scenariot. För att skapa dessa resurser, gör du följande:

1. Gå till [på mallsidan](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. På mallsidan till höger om **överordnade resursgruppen**, klickar du på **till Azure**.
3. Om det behövs, ändra parametervärden och följ stegen i Azure preview portal för att distribuera resursgruppen.

> [!IMPORTANT]
> Kontrollera att din lagringskontonamn är unika. Du kan inte ha dubbla lagringskontonamn i Azure.
> 

## <a name="understand-the-deployment-template"></a>Förstå distributionsmallen
Innan du distribuerar mallen med den här dokumentationen, kontrollera att du förstår hur det fungerar. Följande steg ger en bra översikt över mallen:

1. Gå till [på mallsidan](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Klicka på **azuredeploy.json** att öppna mallfilen.
3. Observera den *osType* parametrar i listan nedan. Den här parametern används för att välja vilka VM-avbildningen som ska användas för databasservern, tillsammans med flera operativsystem relaterade inställningar.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Rulla ned till listan över variabler och kontrollera definitionen för den **dbVMSetting** variabler, som anges nedan. Den tar emot en array-element som ingår i den **dbVMSettings** variabeln. Om du är bekant med termer som software development, kan du visa den **dbVMSettings** variabeln som en hashtabell eller en ordlista.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Anta att du vill distribuera Windows virtuella datorer som kör SQL i serverdelen. Värdet för **osType** skulle vara *Windows*, och **dbVMSetting** variabeln skulle innehålla element som anges nedan, som motsvarar det första värdet i den **dbVMSettings** variabeln.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Observera den **vmSize** innehåller värdet *Standard_DS3*. Endast vissa storlekar på VM kan använda flera nätverkskort. Du kan kontrollera vilka VM-storlekar stöd för flera nätverkskort genom att läsa den [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Linux VM-storlekar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artiklar.

7. Rulla ned till **resurser** och Lägg märke till det första elementet. Beskriver ett lagringskonto. Det här lagringskontot används för att underhålla datadiskar som används av varje VM-databas. I det här scenariot har varje databas VM en OS-disk som lagras i reguljära storage och två datadiskar för som lagras i SSD (premium) lagring.

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Rulla ned till nästa resurs, enligt nedan. Denna resurs representerar det nätverkskort som används för åtkomst till databasen i varje VM-databasen. Observera användningen av den **kopiera** funktion i den här resursen. Mallen kan du distribuera många virtuella datorer som du vill, som baseras på den **dbCount** parameter. Därför måste du skapa samma mängd nätverkskort för åtkomst till databasen, en för varje virtuell dator.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Rulla ned till nästa resurs, enligt nedan. Denna resurs representerar det nätverkskort som används för hantering i varje VM-databasen. Återigen behöver du något av dessa nätverkskort för varje databas VM. Observera den **networkSecurityGroup** element, länka en NSG som ger åtkomst till RDP/SSH till endast detta nätverkskort.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Rulla ned till nästa resurs, enligt nedan. Denna resurs representerar en tillgänglighetsuppsättning ska delas av alla virtuella datorer som databas. På så sätt kan du garantera att det alltid är en virtuell dator i uppsättningen köras under underhåll.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Rulla ned till nästa resurs. Denna resurs representerar databasen virtuella datorer, som visas i först raderna nedan. Observera användningen av den **kopiera** fungera igen och se till att flera virtuella datorer skapas baserat på de **dbCount** parameter. Observera också den **dependsOn** samling. Visas en lista med två nätverkskort som krävs för att skapas innan den virtuella datorn har distribuerats, tillsammans med tillgänglighetsuppsättningen och storage-konto.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Rulla nedåt i den Virtuella datorresursen till den **networkProfile** element, enligt nedan. Observera att det finns två nätverkskort som referens för varje virtuell dator. När du skapar flera nätverkskort för en virtuell dator måste du ange den **primära** -egenskapen för ett nätverkskort till *SANT*, och resten till *FALSKT*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Distribuera ARM-mallen med klicka för att distribuera

> [!IMPORTANT]
> Se till att du följer den [förutsättningar](#Pre-requisites) steg innan du följer anvisningarna nedan.
> 

Exempelmallen som är tillgänglig i den offentliga databasen använder en parameterfil som innehåller standardvärdena som används för att generera scenariot som beskrivs ovan. Följ för att distribuera den här mallen med Klicka för att distribuera [länken](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), till höger om **Backend resursgrupp (se dokumentationen)** klickar du på **till Azure**, ersätta Standardparametervärden om det behövs och följ instruktionerna i portalen.

Bilden nedan visar innehållet i den nya resursgruppen efter distributionen.

![Backend-resursgrupp](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Distribuera mallen med hjälp av PowerShell
Om du vill distribuera mallen som du hämtade med hjälp av PowerShell, installerar och konfigurerar PowerShell genom att slutföra stegen i den [installerar och konfigurerar PowerShell](/powershell/azure/overview) artikel och utför sedan följande steg:

Kör den **`New-AzureRmResourceGroup`** för att skapa en resursgrupp med hjälp av mallen.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Förväntad utdata:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI
Följ stegen nedan om du vill distribuera mallen med hjälp av Azure CLI.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **`azure config mode`** för att byta till Resource Manager-läge enligt nedan.

    ```azurecli
    azure config mode arm
    ```

    Förväntad utdata visas nedan:

        info:    New mode is arm

3. Öppna den [parameterfilen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), Välj dess innehåll och spara den till en fil i datorn. I det här exemplet sparade vi parameterfilen till *parameters.json*.
4. Kör cmdleten **`azure group deployment create`** för att distribuera det nya VNet med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan. Listan som visas efter utdatan beskriver de parametrar som används.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Förväntad utdata:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

