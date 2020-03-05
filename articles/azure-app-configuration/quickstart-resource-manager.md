---
title: Automatisk distribution av virtuella datorer med Azure App konfigurations snabb start
description: Den här snabb starten visar hur du använder Azure PowerShell-modulen och Azure Resource Manager mallar för att distribuera ett Azure App konfigurations lager. Använd sedan värdena i butiken för att distribuera en virtuell dator.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom: mvc
ms.openlocfilehash: 7c6f4b2ea9494c004067a8b19df2c2f098ac2b7f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274958"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration"></a>Snabb start: automatisk distribution av virtuella datorer med konfiguration av appar

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser med hjälp av PowerShell-cmdletar eller -skript. Den här snabb starten visar hur du använder Azure PowerShell och Azure Resource Manager mallar för att distribuera ett Azure App konfigurations lager. Sedan får du lära dig hur du använder nyckel värden i butiken för att distribuera en virtuell dator.

Du använder den nödvändiga mallen för att skapa ett konfigurations lager för appar och sedan lägga till nyckel värden i lagret med hjälp av Azure Portal eller Azure CLI. Den primära mallen refererar till befintliga nyckel värdes konfigurationer från ett befintligt konfigurations lager. De hämtade värdena används för att ange egenskaper för de resurser som skapats av mallen, t. ex. en virtuell dator i det här exemplet.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Den här snabbstarten kräver Azure PowerShell-modulen. Hitta versionen som är installerad på den lokala datorn genom att köra `Get-Module -ListAvailable Az`. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och ange dina autentiseringsuppgifter för Azure i webbläsarens popup-fönster:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Om du har mer än en prenumeration väljer du den prenumeration som du vill använda för den här snabb starten genom att köra följande cmdlets. Glöm inte att ersätta `<your subscription name>` med namnet på din prenumeration:

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

## <a name="deploy-an-azure-app-configuration-store"></a>Distribuera ett Azure App konfigurations lager

Innan du kan tillämpa nyckel värden på den virtuella datorn måste du ha ett befintligt Azure App konfigurations lager. I det här avsnittet beskrivs hur du distribuerar ett Azure App konfigurations lager med hjälp av en Azure Resource Manager-mall. Om du redan har ett app config-arkiv kan du gå vidare till nästa avsnitt i den här artikeln. 

1. Kopiera och klistra in följande JSON-kod i en ny fil med namnet *krav. azuredeploy. JSON*.

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
        "defaultValue": "free",
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

1. Kopiera och klistra in följande JSON-kod i en ny fil med namnet *krav. azuredeploy. Parameters. JSON*. Ersätt **Get-Unique** med ett unikt namn för ditt konfigurations lager.

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

1. Kör följande kommando i PowerShell-fönstret för att distribuera Azure App konfigurations lagringen. Glöm inte att ersätta resurs gruppens namn, sökvägen till mallfilen och sökvägen till mallfilen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Lägg till konfigurations nyckel för virtuell dator-värden

Du kan skapa ett konfigurations lager för appar med hjälp av en Azure Resource Manager mall, men du måste lägga till nyckel värden med hjälp av Azure Portal eller Azure CLI. I den här snabb starten lägger du till nyckel värden med hjälp av Azure Portal.

1. När distributionen är klar navigerar du till det nyligen skapade konfigurations arkivet för appen i [Azure Portal](https://portal.azure.com).

1. Välj **inställningar** > **åtkomst nycklar**. Anteckna den primära skrivskyddade nyckel anslutnings strängen. Du kommer att använda den här anslutnings strängen senare för att konfigurera programmet för att kommunicera med det app-konfigurations lager som du har skapat.

1. Välj konfigurations **utforskaren** > **skapa** för att lägga till följande nyckel/värde-par:

   |Nyckel|Värde|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   Ange *mall* för **etikett**, men behåll **innehålls typen** tom.

## <a name="deploy-vm-using-stored-key-values"></a>Distribuera virtuell dator med hjälp av lagrade nyckel värden

Nu när du har lagt till nyckel värden i butiken är du redo att distribuera en virtuell dator med hjälp av en Azure Resource Manager-mall. Mallen refererar till de **windowsOsVersion** -och **diskSizeGB** -nycklar som du har skapat.

1. Kopiera och klistra in följande JSON-kod i en ny fil med namnet *azuredeploy. JSON*eller ladda ned filen från [Azures snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

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
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('diskSizeGBParameters')).value]",
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

1. Kopiera och klistra in följande JSON-kod i en ny fil med namnet *azuredeploy. Parameters. JSON*eller ladda ned filen från [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

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
   |adminPassword|Ett administratörs lösen ord för den virtuella datorn.|
   |appConfigStoreName|Namnet på din Azure App konfigurations lagring.|
   |appConfigStoreResourceGroup|Resurs gruppen som innehåller appens konfigurations arkiv.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Ett administratörs användar namn för den virtuella datorn.|
   |storageAccountName|Ett unikt namn för ett lagrings konto som är kopplat till den virtuella datorn.|
   |domainNameLabel|Ett unikt domän namn.|

1. Kör följande kommando i PowerShell-fönstret för att distribuera Azure App konfigurations lagringen. Glöm inte att ersätta resurs gruppens namn, sökvägen till mallfilen och sökvägen till mallfilen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Gratulerar! Du har distribuerat en virtuell dator med konfigurationer som lagras i Azure App-konfigurationen.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, konfigurations arkivet för appen, den virtuella datorn och alla relaterade resurser. Om du planerar att använda konfigurations arkivet för appen eller den virtuella datorn i framtiden kan du hoppa över borttagningen. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en virtuell dator med hjälp av en Azure Resource Manager-mall och nyckel värden från Azure App konfiguration.

Om du vill veta mer om hur du skapar andra program med Azure App konfiguration fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Snabb start: skapa en ASP.NET Core-app med Azure App konfiguration](quickstart-aspnet-core-app.md)
