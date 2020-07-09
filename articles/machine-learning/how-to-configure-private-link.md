---
title: Konfigurera en privat slut punkt (för hands version)
titleSuffix: Azure Machine Learning
description: Använd Azures privata länk för att få säker åtkomst till din Azure Machine Learning-arbetsyta från ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: ed3b59b9e6aecb91f4ecb9d569b989b6b2396c48
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134876"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurera en privat Azure-länk för en Azure Machine Learning arbets yta (förhands granskning)

I det här dokumentet får du lära dig hur du använder en privat Azure-länk med din Azure Machine Learning-arbetsyta. Den här funktionen är för närvarande en för hands version och är tillgänglig i regionerna USA, östra, västra USA, västra 2, södra centrala USA. 

Med Azures privata länk kan du ansluta till din arbets yta med en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i det virtuella nätverket. Du kan sedan begränsa åtkomsten till din arbets yta så att den bara sker över de privata IP-adresserna. Privat länk hjälper till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Azures privata länk påverkar inte Azures kontroll plan (hanterings åtgärder) som att ta bort arbets ytan eller hantera beräknings resurser. Till exempel skapa, uppdatera eller ta bort ett beräknings mål. De här åtgärderna utförs via det offentliga Internet som normalt.
>
> För hands versionen av Azure Machine Learning Compute instances stöds inte i en arbets yta där privat länk är aktive rad.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Skapa en arbets yta som använder en privat slut punkt

För närvarande stöder vi bara aktivering av en privat slut punkt när du skapar en ny Azure Machine Learning-arbetsyta. Följande mallar finns för flera populära konfigurationer:

> [!TIP]
> Automatiskt godkännande styr automatisk åtkomst till den privata länken aktiverade resursen. Mer information finns i [Vad är Azure Private Link service](../private-link/private-link-service-overview.md).

* [Arbets yta med Kundhanterade nycklar och automatiskt godkännande för privat länk](#cmkaapl)
* [Arbets yta med Kundhanterade nycklar och manuellt godkännande för privat länk](#cmkmapl)
* [Arbets yta med Microsoft-hanterade nycklar och automatiskt godkännande för privat länk](#mmkaapl)
* [Arbets yta med Microsoft-hanterade nycklar och manuellt godkännande för privat länk](#mmkmapl)

När du distribuerar en mall måste du ange följande information:

* Namn på arbetsyta
* Azure-region för att skapa resurserna i
* Arbetsyte Edition (Basic eller Enterprise)
* Om hög sekretess inställningar för arbets ytan ska aktive ras
* Om kryptering för arbets ytan med en kundhanterad nyckel ska vara aktive rad och de associerade värdena för nyckeln
* Virtual Network och under nätets namn skapas ett nytt virtuellt nätverk och undernät i mallen

När en mall har skickats och etableringen har slutförts kommer resurs gruppen som innehåller din arbets yta att innehålla tre nya artefakt typer relaterade till en privat länk:

* Privat slut punkt
* Nätverksgränssnitt
* Privat DNS-zon

Arbets ytan innehåller också ett Azure-Virtual Network som kan kommunicera med arbets ytan över den privata slut punkten.

### <a name="deploy-the-template-using-the-azure-portal"></a>Distribuera mallen med hjälp av Azure Portal

1. Följ stegen i [distribuera resurser från den anpassade mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). När du kommer till skärmen __Redigera mall__ klistrar du in i någon av mallarna från slutet av det här dokumentet.
1. Välj __Spara__ för att använda mallen. Ange följande information och godkänn de allmänna villkor som anges:

   * Prenumeration: Välj den Azure-prenumeration som ska användas för dessa resurser.
   * Resurs grupp: Välj eller skapa en resurs grupp som ska innehålla tjänsterna.
   * Namn på arbets yta: namnet som ska användas för Azure Machine Learning arbets ytan som ska skapas. Arbets ytans namn måste innehålla mellan 3 och 33 tecken. Det får bara innehålla alfanumeriska tecken och "-".
   * Plats: Välj den plats där resurserna ska skapas.

Mer information finns i [distribuera resurser från en anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

### <a name="deploy-the-template-using-azure-powershell"></a>Distribuera mallen med hjälp av Azure PowerShell

I det här exemplet förutsätts att du har sparat en av mallarna från slutet av det här dokumentet till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

### <a name="deploy-the-template-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI

I det här exemplet förutsätts att du har sparat en av mallarna från slutet av det här dokumentet till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Använda en arbets yta över en privat slut punkt

Eftersom kommunikation till arbets ytan endast tillåts från det virtuella nätverket måste alla utvecklings miljöer som använder arbets ytan vara medlemmar i det virtuella nätverket. Till exempel en virtuell dator i det virtuella nätverket.

> [!IMPORTANT]
> För att undvika tillfälligt avbrott i anslutningen rekommenderar Microsoft att tömma DNS-cachen på datorer som ansluter till arbets ytan när du har aktiverat privat länk. 

Information om Azure Virtual Machines finns i Virtual Machines- [dokumentationen](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Använda Azure Storage

Om du vill skydda det Azure Storage kontot som används av din arbets yta, så Lägg det i det virtuella nätverket.

Information om hur du placerar lagrings kontot i det virtuella nätverket finns i [använda ett lagrings konto för din arbets yta](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

## <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Om du vill skydda Azure Key Vault som används av din arbets yta kan du antingen flytta den i det virtuella nätverket eller aktivera en privat länk.

Information om hur du placerar nyckel valvet i det virtuella nätverket finns i [använda en nyckel valv instans med din arbets yta](how-to-enable-virtual-network.md#key-vault-instance).

Information om hur du aktiverar privat länk för nyckel valvet finns i [integrera Key Vault med Azure privat länk](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Använda Azure Kubernetes Services

Om du vill skydda de Azure Kubernetes-tjänster som används av din arbets yta, så Lägg till den i ett virtuellt nätverk. Mer information finns i [använda Azure Kubernetes Services med din arbets yta](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Azure Machine Learning stöder inte användning av en Azure Kubernetes-tjänst som har privat länk aktive rad.

## <a name="azure-container-registry"></a>Azure Container Registry

Information om hur du skyddar Azure Container Registry i det virtuella nätverket finns i [använd Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Om du använder en privat länk för din Azure Machine Learning arbets yta och lägger Azure Container Registry för din arbets yta i ett virtuellt nätverk, måste du också använda följande Azure Resource Manager-mall. Med den här mallen kan din arbets yta kommunicera med ACR via den privata länken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

<a id="cmkaapl"></a>
### <a name="workspace-with-customer-managed-keys-and-auto-approval-for-private-link"></a>Arbets yta med Kundhanterade nycklar och automatiskt godkännande för privat länk

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
          "mode": "Incremental",
          "template": {
              "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "resources": [
                  {
                      "type": "Microsoft.Network/privateDnsZones",
                      "apiVersion": "2018-09-01",
                      "name": "privatelink.api.azureml.ms",
                      "location": "global",
                      "tags": {},
                      "properties": {}
                  },
                  {
                      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
                      "apiVersion": "2018-09-01",
                      "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
                      "location": "global",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "virtualNetwork": {
                              "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                          },
                          "registrationEnabled": false
                      }
                  },
                  {
                      "apiVersion": "2017-05-10",
                      "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
                      "type": "Microsoft.Resources/deployments",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "mode": "Incremental",
                          "templatelink": {
                              "contentVersion": "1.0.0.0",
                              "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                          },
                          "parameters": {
                              "privateDnsName": {
                                  "value": "privatelink.api.azureml.ms"
                              },
                              "privateEndpointNicResourceId": {
                                  "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                              },
                              "nicRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                              },
                              "ipConfigRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                              },
                              "uniqueId": {
                                  "value": "[variables('privateDnsGuid')]"
                              },
                              "existingRecords": {
                                  "value": {}
                              }
                          }
                      }
                  }
              ]
          }
      },
      "resourceGroup": "[resourceGroup().name]"
  }
  ]
}
```

<a id="cmkmapl"></a>
### <a name="workspace-with-customer-managed-keys-and-manual-approval-for-private-link"></a>Arbets yta med Kundhanterade nycklar och manuellt godkännande för privat länk

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

<a id="mmkaapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-auto-approval-for-private-link"></a>Arbets yta med Microsoft-hanterade nycklar och automatiskt godkännande för privat länk

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"
    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Network/privateDnsZones",
              "apiVersion": "2018-09-01",
              "name": "privatelink.api.azureml.ms",
              "location": "global",
              "tags": {},
              "properties": {}
            },
            {
              "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
              "apiVersion": "2018-09-01",
              "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
              "location": "global",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "virtualNetwork": {
                  "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                },
                "registrationEnabled": false
              }
            },
            {
              "apiVersion": "2017-05-10",
              "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
              "type": "Microsoft.Resources/deployments",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "mode": "Incremental",
                "templatelink": {
                  "contentVersion": "1.0.0.0",
                  "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                },
                "parameters": {
                  "privateDnsName": {
                    "value": "privatelink.api.azureml.ms"
                  },
                  "privateEndpointNicResourceId": {
                    "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                  },
                  "nicRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                  },
                  "ipConfigRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                  },
                  "uniqueId": {
                    "value": "[variables('privateDnsGuid')]"
                  },
                  "existingRecords": {
                    "value": {}
                  }
                }
              }
            }
          ]
        }
      },
      "resourceGroup": "[resourceGroup().name]"
    }
  ]
}
```

<a id="mmkmapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-manual-approval-for-private-link"></a>Arbets yta med Microsoft-hanterade nycklar och manuellt godkännande för privat länk

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar Azure Machine Learning-arbetsytan finns i artikeln [företags säkerhet](concept-enterprise-security.md) .
