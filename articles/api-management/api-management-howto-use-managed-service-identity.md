---
title: Använd Azure hanterad tjänstidentitet i Azure API Management | Microsoft Docs
description: Lär dig hur du använder Azure hanterad tjänstidentitet i API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: b7208943a27bcd184100ae426721a2fe8f6e1c72
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970492"
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Använda Azure hanterad tjänstidentitet i Azure API Management

Den här artikeln visar hur du skapar en hanterad tjänstidentitet för en API Management-tjänstinstans och hur du kommer åt andra resurser. En hanterad tjänstidentitet som genereras av Azure Active Directory (Azure AD) kan din API Management-instans att enkelt och säkert sätt få åtkomst till andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Den här hanterade tjänstidentiteter hanteras av Azure och kräver inte att etablera eller rotera hemligheter. Läs mer om Azure Managed Service Identity, [hanterad tjänstidentitet för Azure-resurser](../active-directory/msi-overview.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>Skapa en hanterad tjänstidentitet för en API Management-instans

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterad tjänstidentitet i portalen, ska du först skapa en API Management-instans som vanligt och sedan aktivera funktionen.

1. Skapa en API Management-instans i portalen som vanligt. Navigera till den i portalen.
2. Välj **hanterad tjänstidentitet**.
3. Växla Register med Azure Active Directory till On. Klicka på Spara.

![Aktivera MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall

Du kan skapa en API Management-instans med en identitet genom att inkludera följande egenskap i resursdefinitionen: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Detta informerar du Azure att skapa och hantera identitet för din API Management-instans. 

En fullständig Azure Resource Manager-mall kan se ut så här:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Använd den hanterade tjänstidentiteten för att komma åt andra resurser

> [!NOTE]
> Hanterad tjänstidentitet kan för närvarande kan användas för att hämta certifikat från Azure Key Vault för anpassade domännamn för API Management. Fler scenarier kommer snart att stödjas.
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Skaffa ett certifikat från Azure Key Vault

#### <a name="prerequisites"></a>Förutsättningar
1. Key Vault som innehåller pfx-certifikatet måste finnas i samma Azure-prenumeration och samma resursgrupp som API Management-tjänsten. Det här är ett krav för Azure Resource Manager-mallen. 
2. Innehållstypen för hemligheten måste vara *application/x-pkcs12*. Du kan använda följande skript för att ladda upp certifikatet:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Om versionen för certifikatet inte anges, hämta API Management automatiskt den nya versionen av certifikatet när det överförs till Key Vault. 

I följande exempel visas en Azure Resource Manager-mall som innehåller följande steg:

1. Skapa en API Management-instans med hanterade tjänstidentiteter.
2. Uppdatera principer för åtkomst av en instans av Azure Key Vault och tillåter API Management-instans att hämta hemligheter från den.
3. Uppdatera API Management-instans genom att ange ett anpassat domännamn via ett certifikat från Key Vault-instansen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Managed Service Identity:

* [Hanterad tjänstidentitet för Azure-resurser](../active-directory/msi-overview.md)
* [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates)

