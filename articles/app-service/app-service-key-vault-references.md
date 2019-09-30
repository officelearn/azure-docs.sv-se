---
title: Key Vault referenser – Azure App Service | Microsoft Docs
description: Konceptuell referens och installations guide för Azure Key Vault referenser i Azure App Service och Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: cf4eade598de24e323a8c8647a64921f8797e3a2
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686743"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Använda Key Vault referenser för App Service och Azure Functions (förhands granskning)

> [!NOTE] 
> Key Vault referenser finns för närvarande i för hands version.

Det här avsnittet visar hur du arbetar med hemligheter från Azure Key Vault i din App Service eller Azure Functions program utan att behöva ändra kod. [Azure Key Vault](../key-vault/key-vault-overview.md) är en tjänst som tillhandahåller centraliserad hemligheter-hantering med fullständig kontroll över åtkomst principer och gransknings historik.

## <a name="granting-your-app-access-to-key-vault"></a>Ge appen åtkomst till Key Vault

För att kunna läsa hemligheter från Key Vault måste ett valv skapas och ge ditt program behörighet att komma åt det.

1. Skapa ett nyckel valv genom att följa [Key Vault snabb start](../key-vault/quick-create-cli.md).

1. Skapa en [systemtilldelad hanterad identitet](overview-managed-identity.md) för ditt program.

   > [!NOTE] 
   > Key Vault referenser stöder för närvarande endast systemtilldelade hanterade identiteter. Användare som tilldelats identiteter kan inte användas.

1. Skapa en [åtkomst princip i Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) för den program identitet som du skapade tidigare. Aktivera hemliga behörigheten "Get" för den här principen. Konfigurera inte inställningarna "auktoriserat program" eller `applicationId` eftersom detta inte är kompatibelt med en hanterad identitet.

    > [!NOTE]
    > Key Vault referenser kan för närvarande inte lösa hemligheter som lagras i ett nyckel valv med [nätverks begränsningar](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Syntax för referenser

En Key Vault referens är av formuläret `@Microsoft.KeyVault({referenceString})`, där `{referenceString}` ersätts av något av följande alternativ:

> [!div class="mx-tdBreakAll"]
> | Referens sträng                                                            | Beskrivning                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** bör vara den fullständiga data Plans-URI: n för en hemlighet i Key Vault, inklusive en version, t. ex. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** ska vara namnet på din Key Vault-resurs. **SecretName** ska vara namnet på mål hemligheten. **SecretVersion** bör vara den version av hemligheten som ska användas. |

> [!NOTE] 
> I den aktuella för hands versionen krävs versioner. När du roterar hemligheter måste du uppdatera versionen i program konfigurationen.

En fullständig referens skulle till exempel se ut så här:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Också

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Käll program inställningar från Key Vault

Key Vault referenser kan användas som värden för [program inställningar](configure-common.md#configure-app-settings), så att du kan behålla hemligheter i Key Vault i stället för plats konfigurationen. Program inställningarna krypteras säkert i vila, men om du behöver hemliga hanterings funktioner bör de gå in Key Vault.

Om du vill använda en Key Vault referens för en program inställning anger du referensen som värde för inställningen. Din app kan referera till hemligheten via nyckeln som normalt. Inga kod ändringar krävs.

> [!TIP]
> De flesta program inställningar som använder Key Vault referenser ska markeras som plats inställningar, eftersom du borde ha separata valv för varje miljö.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

När du automatiserar resurs distributioner via Azure Resource Manager mallar kan du behöva sekvensera dina beroenden i en viss ordning för att den här funktionen ska fungera. Observera att du måste definiera dina program inställningar som sin egen resurs, i stället för att använda en `siteConfig` egenskap i plats definitionen. Detta beror på att platsen måste definieras först så att den systemtilldelade identiteten skapas med den och kan användas i åtkomst principen.

Ett exempel på en psuedo-mall för en Function-app kan se ut så här:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> I det här exemplet är käll kontroll distributionen beroende av program inställningarna. Detta är vanligt vis ett osäkert beteende eftersom uppdaterings inställningen för appen har asynkront. Men eftersom vi har inkluderat `WEBSITE_ENABLE_SYNC_UPDATE_SITE` program inställningen är uppdateringen synkron. Det innebär att käll kontroll distributionen endast startar när program inställningarna har uppdaterats fullständigt.

## <a name="troubleshooting-key-vault-references"></a>Felsöka Key Vault referenser

Om en referens inte löses korrekt, används referensvärdet i stället. Det innebär att en miljö variabel skapas vars värde har `@Microsoft.KeyVault(...)` syntaxen för program inställningar. Detta kan orsaka att programmet returnerar fel, eftersom det förväntar sig en hemlighet för en viss struktur.

Oftast beror det på en felaktig konfiguration av [Key Vaults åtkomst princip](#granting-your-app-access-to-key-vault). Det kan dock också bero på en hemlighet som inte längre är befintlig eller syntaxfel i själva referensen.

Om syntaxen är korrekt kan du Visa andra orsaker till felet genom att kontrol lera den aktuella lösnings statusen med hjälp av en inbyggd detektor.

### <a name="using-the-detector-for-app-service"></a>Använda detektorn för App Service

1. I portalen navigerar du till din app.
2. Välj **diagnostisera och lös problem**.
3. Välj **tillgänglighet och prestanda** och välj sedan **webbapp.**
4. Hitta **Key Vault diagnostik för program inställningar** och klicka på **Mer information**.


### <a name="using-the-detector-for-azure-functions"></a>Använda detektorn för Azure Functions

1. I portalen navigerar du till din app.
2. Navigera till **plattforms funktioner.**
3. Välj **diagnostisera och lös problem**.
4. Välj **tillgänglighet och prestanda** och välj **Function app-Down eller rapportera fel.**
5. Klicka på **Key Vault diagnostik för program inställningar.**
