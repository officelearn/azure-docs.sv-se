---
title: Använda hanterade identiteter i Azure API Management | Microsoft-dokument
description: Lär dig hur du använder hanterade identiteter i API Management
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
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249638"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Använda hanterade identiteter i Azure API Management

Den här artikeln visar hur du skapar en hanterad identitet för en API Management-tjänstinstans och hur du får åtkomst till andra resurser. En hanterad identitet som genereras av Azure Active Directory (Azure AD) gör att din API Management-instans enkelt och säkert kan komma åt andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Den här identiteten hanteras av Azure och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Skapa en hanterad identitet för en API Management-instans

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterad identitet i portalen skapar du först en API Management-instans som vanligt och aktiverar sedan funktionen.

1. Skapa en API Management-instans i portalen som vanligt. Gå till den i portalen.
2. Välj **Hanterade tjänstidentiteter**.
3. Växla registrera med Azure Active Directory till På. Klicka på Spara.

![Aktivera MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Använda Azure Resource Manager-mallen

Du kan skapa en API Management-instans med en identitet genom att inkludera följande egenskap i resursdefinitionen:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Detta talar om för Azure att skapa och hantera identiteten för din API Management-instans.

En komplett Azure Resource Manager-mall kan till exempel se ut så här:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
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
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Använd den hanterade tjänstidentiteten för att komma åt andra resurser

> [!NOTE]
> För närvarande kan hanterade identiteter användas för att hämta certifikat från Azure Key Vault för anpassade domännamn för API Management. Fler scenarier kommer att stödjas snart.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Skaffa ett certifikat från Azure Key Vault

#### <a name="prerequisites"></a>Krav
1. Nyckelvalvet som innehåller pfx-certifikatet måste finnas i samma Azure-prenumeration och samma resursgrupp som API Management-tjänsten. Detta är ett krav i Azure Resource Manager-mallen.
2. Innehållet typ av hemligheten måste vara *ansökan/x-pkcs12*. Du kan använda följande skript för att ladda upp certifikatet:

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
> Om objektversionen av certifikatet inte tillhandahålls hämtas automatiskt den nyare versionen av certifikatet när det har överförts till Key Vault.

I följande exempel visas en Azure Resource Manager-mall som innehåller följande steg:

1. Skapa en API Management-instans med en hanterad identitet.
2. Uppdatera åtkomstprinciperna för en Azure Key Vault-instans och gör det möjligt för API Management-instansen att hämta hemligheter från den.
3. Uppdatera API Management-instansen genom att ange ett anpassat domännamn via ett certifikat från Key Vault-instansen.

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
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
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

Läs mer om hanterade identiteter för Azure-resurser:

* [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates)
* [Autentisera med en hanterad identitet i en princip](./api-management-authentication-policies.md#ManagedIdentity)
