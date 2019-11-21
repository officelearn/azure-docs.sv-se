---
title: Set up Azure IoT Hub Device Provisioning using Azure Resource Manager template
description: Azure quickstart - Set up the Azure IoT Hub Device Provisioning Service using a template
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 9a763a492496cef0f01468dc4b655a61e91a2a53
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229474"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with an Azure Resource Manager template

Du kan använda [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) för att programmässigt konfigurera de Azure-molnresurser som är nödvändiga för att etablera dina enheter. These steps show how to create an IoT hub and a new IoT Hub Device Provisioning Service, and link the two services together using an Azure Resource Manager template. This quickstart uses [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) to perform the programmatic steps necessary to create a resource group and deploy the template, but you can easily use the [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby, or other programming languages to perform these steps and deploy your template. 


## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- This quickstart requires that you run the Azure CLI locally. Du måste ha Azure CLI-version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Logga in på Azure och skapa en resursgrupp

Logga in på ditt Azure-konto och välj din prenumeration.

1. At the command prompt, run the [login command][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Use the following [command to list the Azure accounts][lnk-az-account-command] available for you to use:
    
    ```azurecli
    az account list 
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. När du skapar Azure-molnresurser IoT-hubbar och etablerar tjänster skapar du dem i en resursgrupp. Either use an existing resource group, or run the following [command to create a resource group][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > I föregående exempel skapas en resursgrupp i USA, västra. Du kan visa en lista över tillgängliga platser genom att köra kommandot `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

Använd en JSON-mall för att skapa en etableringstjänst och en länkad IoT-hubb i din resursgrupp. Du kan också använda en Azure Resource Manager-mall för att göra ändringar i en befintlig etableringstjänst eller IoT-hubb.

1. Använd ett redigeringsprogram för att skapa en Azure Resource Manager-mall som heter **template.json** med följande stomme. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Ersätt avsnittet med **parametrar** med följande innehåll. Parameteravsnittet anger parametrar som kan överföras från en annan fil. I det här avsnittet anges namnet på IoT-hubben och etableringstjänsten som ska skapas. Det anger också plats både för IoT-hubben och etableringstjänsten. Värdena är begränsade till Azure-regioner som stöder IoT-hubbar och etableringstjänster. Om du vill ha en lista över platser som stöds för enhetsetableringstjänsten kan du köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` eller gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka på ”enhetsetableringstjänst”.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Ersätt avsnittet med **variabler** med följande innehåll. I det här avsnittet definieras värden som används senare för att konstruera IoT-hubbens anslutningssträng, som behövs för att länka etableringstjänsten och IoT-hubben. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Lägg till följande rader till samlingen **resurser** när du ska skapa en IoT-hubb. The JSON specifies the minimum properties required to create an IoT hub. Egenskaperna **namn** och **plats** skickas som parametrar. To learn more about the properties you can specify for an IoT hub in a template, see [Microsoft.Devices/IotHubs template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Om du vill skapa etableringstjänsten lägger du till följande rader efter IoT-hubbspecifikationen i samlingen **resources**. Etableringstjänstens **namn** och **plats** överförs i parametrar. Ange IoT-hubbarna för att länka till etableringstjänsten i samlingen **iotHubs**. Du måste minst ange egenskaperna **connectionString** och **location** för varje länkad IoT-hubb. Du kan också ange egenskaper som **allocationWeight** och **applyAllocationPolicy** för varje IoT-hubb, samt egenskaper som **allocationPolicy** och **authorizationPolicies** för själva etableringstjänsten. Mer information finns i [Microsoft.Devices/provisioningServices-mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   Egenskapen **dependsOn** används för att se till att Resource Manager skapar IoT-hubben före etableringstjänsten. Mallen kräver IoT-hubbens anslutningssträng för att ange dess länkning till etableringstjänsten, så hubben och dess nycklar måste skapas först. I mallen används funktioner som **concat** och **listKeys** för att skapa anslutningssträngen. Om du vill lära dig mer läser du [Azure Resource Manager-mallfunktioner](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Spara mallfilen. Den klara mallen bör se ut så här:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Skapa en Resource Manager-parameterfil

The template that you defined in the last step uses parameters to specify the name of the IoT hub, the name of the provisioning service, and the location (Azure region) to create them. Du kan skicka dessa parametrar i en separat fil. När du gör det kan du återanvända samma mall för flera distribueringar. Skapa parameterfilen genom att följa dessa steg:

1. Använd ett redigeringsprogram för att skapa en Azure Resource Manager-parameterfil som heter **parameters.json** med följande stomme: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Lägg till värdet **iotHubName** i parameteravsnittet.  An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Lägg till värdet **provisioningServiceName** till parameteravsnittet. You will also need to choose a globally unique name for your provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Lägg till värdet **hubLocation** i parameteravsnittet. Värdet anger plats både för IoT-hubben och etableringstjänsten. Värdet måste motsvara en av de platser som anges i samlingen **allowedValues** i parameterdefinitionen i mallfilen. Den här samlingen begränsar värdena för Azure-platser som stöder både IoT-hubbar och etableringstjänster. Om du vill ha en lista över platser som stöds för enhetsetableringstjänsten kan du köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` eller gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka på ”enhetsetableringstjänst”.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Spara filen. 


> [!IMPORTANT]
> Både IoT-hubben och etableringstjänsten kan identifieras offentligt som DNS-slutpunkter, så se till att undvika känslig information när du namnger dem.
>

## <a name="deploy-the-template"></a>Distribuera mallen

Använd följande Azure CLI-kommandon för att distribuera dina mallar och verifiera distributionen.

1. För att distribuera din mall kör du följande [kommando för att starta en distribuering](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Leta efter egenskapen **provisioningState** som är inställt på ”Succeeded” i utdata. 

   ![Etableringsutdata](./media/quick-setup-auto-provision-rm/output.png) 


2. Om du vill verifiera din distribution kör du följande [kommando för att lista resurser](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) och hålla utkik efter den nya etableringstjänsten och IoT-hubben i utdata:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. If you do not plan to continue, you can use the Azure CLI to [delete an individual resource][lnk-az-resource-command], such as an IoT hub or a provisioning service, or to delete a resource group and all of its resources.

Ta bort etableringstjänsten genom att köra följande kommando:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Ta bort en IoT-hubb genom att köra följande kommando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Om du vill ta bort en resursgrupp och alla dess resurser kör du följande kommando:

```azurecli
az group delete --name {your resource group name}
```

Du kan också ta bort resursgrupper och enskilda resurser med Azure-portalen, PowerShell eller REST API:er eller SDK:er för plattform som stöds som har publicerats för Azure Resource Manager eller IoT Hub Device Provisioning-tjänsten.

## <a name="next-steps"></a>Nästa steg

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
