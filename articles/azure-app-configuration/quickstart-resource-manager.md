---
title: Automatiserad VM-distribution med snabbstart för Azure App-konfiguration
description: Den här snabbstarten visar hur du använder Azure PowerShell-modulen och Azure Resource Manager-mallarna för att distribuera ett Azure App Configuration Store. Använd sedan värdena i arkivet för att distribuera en virtuell dator.
author: lisaguthrie
ms.author: lcozzens
ms.date: 04/14/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 02afa2cb36323e0c3c38c2451b1924b636f7faed
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309100"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Snabbstart: Automatiserad VM-distribution med mallen AppKonfiguration och Resource Manager

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser med hjälp av PowerShell-cmdletar eller -skript. Den här snabbstarten visar hur du använder Azure PowerShell- och Azure Resource Manager-mallar för att distribuera ett Azure App Configuration Store. Sedan får du lära dig hur du använder nyckelvärdena i arkivet för att distribuera en virtuell dator.

Du använder den nödvändiga mallen för att skapa ett App Configuration Store och sedan lägga till nyckelvärden i arkivet med Hjälp av Azure-portalen eller Azure CLI. Den primära mallen refererar till befintliga nyckelvärdeskonfigurationer från ett befintligt konfigurationsarkiv. De hämtade värdena används för att ange egenskaper för de resurser som skapas av mallen, till exempel en virtuell dator i det här exemplet.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)

* Den här snabbstarten kräver Azure PowerShell-modulen. Hitta versionen som är installerad på den lokala datorn genom att köra `Get-Module -ListAvailable Az`. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och ange dina autentiseringsuppgifter för Azure i webbläsarens popup-fönster:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Om du har mer än en prenumeration väljer du den prenumeration som du vill använda för den här snabbstarten genom att köra följande cmdlets. Glöm inte att `<your subscription name>` ersätta med namnet på din prenumeration:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Distribuera ett Azure App-konfigurationsarkiv

Innan du kan tillämpa nyckelvärden på den virtuella datorn måste du ha ett befintligt Azure App Configuration Store. I det här avsnittet beskrivs hur du distribuerar ett Azure App Configuration Store med hjälp av en Azure Resource Manager-mall. Om du redan har en app config store kan du gå vidare till nästa avsnitt i den här artikeln. 

1. Kopiera och klistra in följande json-kod i en ny fil med namnet *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Kopiera och klistra in följande json-kod i en ny fil med namnet *prereq.azuredeploy.parameters.json*. Ersätt **GET-UNIQUE** med ett unikt namn för din Configuration Store.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. I powershell-fönstret kör du följande kommando för att distribuera Azure App Configuration Store. Glöm inte att ersätta resursgruppsnamnet, sökvägen till mallfilen och sökvägen till mallparameterfilen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Lägga till nyckelvärden för VM-konfiguration

Du kan skapa ett App Configuration Store med en Azure Resource Manager-mall, men du måste lägga till nyckelvärden med Azure-portalen eller Azure CLI. I den här snabbstarten lägger du till nyckelvärden med Azure-portalen.

1. När distributionen är klar navigerar du till det nyligen skapade App Configuration Store i [Azure Portal](https://portal.azure.com).

1. Välj **Inställningar** > **Access Nycklar**. Anteckna den primära skrivskyddade nyckelanslutningssträngen. Du ska använda den här anslutningssträngen senare för att konfigurera programmet så att det kommunicerar med appkonfigurationsarkivet som du skapade.

1. Välj**Skapa konfigurationsutforskare** **Explorer** > om du vill lägga till följande nyckel-värde-par:

   |Nyckel|Värde|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   Ange *mall* för **Etikett**, men håll **innehållstypen** tom.

## <a name="deploy-vm-using-stored-key-values"></a>Distribuera virtuell dator med lagrade nyckelvärden

Nu när du har lagt till nyckelvärden i arkivet är du redo att distribuera en virtuell dator med hjälp av en Azure Resource Manager-mall. Mallen refererar till **de windowsOsVersion-** och **diskSizeGB-nycklar** som du har skapat.

> [!WARNING]
> ARM-mallar kan inte referera till nycklar i ett App Configuration Store som har Privat länk aktiverat.

1. Kopiera och klistra in följande json-kod i en ny fil med namnet *azuredeploy.json*eller hämta filen från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Kopiera och klistra in följande json-kod i en ny fil med namnet *azuredeploy.parameters.json*eller hämta filen från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Ersätt parametervärdena i mallen med följande värden:

   |Parameter|Värde|
   |-|-|
   |adminPassword|Ett administratörslösenord för den virtuella datorn.|
   |appConfigStoreName|Namnet på ditt Azure App Configuration Store.|
   |appConfigStoreResourceGroup|Resursgruppen som innehåller appkonfigurationsarkivet.|
   |vmSkuKey (vmSkuKey)|*windowsOSVersion (windowsOSVersion)*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Ett administratörsanvändarnamn för den virtuella datorn.|
   |storageAccountName|Ett unikt namn för ett lagringskonto som är associerat med den virtuella datorn.|
   |domänNamnLabel|Ett unikt domännamn.|

1. I PowerShell-fönstret kör du följande kommando för att distribuera den virtuella datorn. Glöm inte att ersätta resursgruppsnamnet, sökvägen till mallfilen och sökvägen till mallparameterfilen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>"
       -TemplateFile "<path to azuredeploy.json>" `
       -TemplateParameterFile "<path to azuredeploy.parameters.json>"
   ```

Grattis! Du har distribuerat en virtuell dator med konfigurationer som lagras i Azure App Configuration.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, App Configuration Store, VM och alla relaterade resurser. Om du planerar att använda App Configuration Store eller VM i framtiden kan du hoppa över att ta bort den. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en virtuell dator med hjälp av en Azure Resource Manager-mall och nyckelvärden från Azure App-konfiguration.

Om du vill veta mer om hur du skapar andra program med Azure App-konfiguration fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en ASP.NET Core-app med Azure App-konfiguration](quickstart-aspnet-core-app.md)
