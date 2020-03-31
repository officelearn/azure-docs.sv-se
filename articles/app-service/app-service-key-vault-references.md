---
title: Använda Key Vault-referenser
description: Lär dig hur du konfigurerar Azure App Service och Azure Functions för att använda Azure Key Vault-referenser. Gör Key Vault-hemligheter tillgängliga för din programkod.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7fdb7c980a278e2dcd4b64a4b70de50721d0b72a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280344"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Använda Key Vault-referenser för App Service och Azure-funktioner

Det här avsnittet visar hur du arbetar med hemligheter från Azure Key Vault i ditt App Service- eller Azure Functions-program utan att kräva några kodändringar. [Azure Key Vault](../key-vault/key-vault-overview.md) är en tjänst som tillhandahåller centraliserad hemligheter hantering, med full kontroll över åtkomstprinciper och granskningshistorik.

## <a name="granting-your-app-access-to-key-vault"></a>Ge din app åtkomst till Key Vault

För att kunna läsa hemligheter från Key Vault måste du ha ett valv skapat och ge din app behörighet att komma åt det.

1. Skapa ett nyckelvalv genom att följa [snabbstarten för Key Vault](../key-vault/quick-create-cli.md).

1. Skapa en [systemtilldelad hanterad identitet](overview-managed-identity.md) för ditt program.

   > [!NOTE] 
   > Key Vault-referenser stöder för närvarande endast systemtilldelade hanterade identiteter. Användartilldelade identiteter kan inte användas.

1. Skapa en [åtkomstprincip i Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) för programidentiteten som du skapade tidigare. Aktivera den hemliga behörigheten "Hämta" för den här principen. Konfigurera inte det "auktoriserade `applicationId` programmet" eller inställningarna, eftersom detta inte är kompatibelt med en hanterad identitet.

    > [!NOTE]
    > Key Vault-referenser kan för närvarande inte lösa hemligheter som lagras i ett nyckelvalv med [nätverksbegränsningar](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Referenssyntax

En Referens för nyckelvalv `@Microsoft.KeyVault({referenceString})`är `{referenceString}` av formuläret , där ersätts med något av följande alternativ:

> [!div class="mx-tdBreakAll"]
> | Referenssträng                                                            | Beskrivning                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** ska vara den fullständiga dataplanets URI för en hemlighet i Key Vault, inklusive en version, t.ex.https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_; SecretName=_secretName_; SecretVersion =_secretVersion_ | **VaultName** ska namnet på din Key Vault-resurs. **SecretName** ska vara namnet på målhemligheten. **SecretVersion** bör vara den version av hemligheten att använda. |

En fullständig referens med Version skulle till exempel se ut så här:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Du kan också:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Inställningar för källprogram från Key Vault

Key Vault-referenser kan användas som värden för [programinställningar,](configure-common.md#configure-app-settings)så att du kan behålla hemligheter i Key Vault i stället för webbplatsen config. Programinställningarna krypteras säkert i vila, men om du behöver hemliga hanteringsfunktioner bör de gå in i Key Vault.

Om du vill använda en Referens för nyckelvalv för en programinställning anger du referensen som värdet för inställningen. Din app kan referera till hemligheten via dess nyckel som vanligt. Inga kodändringar krävs.

> [!TIP]
> De flesta programinställningar som använder Key Vault-referenser bör markeras som platsinställningar, eftersom du bör ha separata valv för varje miljö.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

När du automatiserar resursdistributioner via Azure Resource Manager-mallar kan du behöva sekvensera dina beroenden i en viss ordning för att den här funktionen ska fungera. Du måste definiera programinställningarna som sin egen resurs i `siteConfig` stället för att använda en egenskap i webbplatsdefinitionen. Detta beror på att platsen måste definieras först så att den systemtilldelade identiteten skapas med den och kan användas i åtkomstprincipen.

Ett exempel på psuedo-mall för en funktionsapp kan se ut så här:

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
> I det här exemplet beror källkontrolldistributionen på programinställningarna. Detta är normalt osäkert beteende, eftersom appinställningsuppdateringen fungerar asynkront. Men eftersom vi har `WEBSITE_ENABLE_SYNC_UPDATE_SITE` inkluderat programinställningen är uppdateringen synkron. Det innebär att distributionen av källkontroll endast startar när programinställningarna har uppdaterats fullständigt.

## <a name="troubleshooting-key-vault-references"></a>Felsöka nyckelvalvsreferenser

Om en referens inte matchas korrekt används referensvärdet i stället. Det innebär att för programinställningar skapas en miljövariabel vars värde har syntaxen. `@Microsoft.KeyVault(...)` Detta kan orsaka att programmet kastar fel, eftersom det förväntade sig en hemlighet av en viss struktur.

Oftast beror detta på en felaktig konfiguration av [åtkomstprincipen för Nyckelvalv](#granting-your-app-access-to-key-vault). Det kan dock också bero på att en hemlighet inte längre finns eller ett syntaxfel i själva referensen.

Om syntaxen är korrekt kan du visa andra felorsaker genom att kontrollera den aktuella upplösningsstatusen i portalen. Navigera till Programinställningar och välj "Redigera" för referensen i fråga. Under inställningskonfigurationen bör du se statusinformation, inklusive eventuella fel. Frånvaron av dessa innebär att referenssyntaxen är ogiltig.

Du kan också använda en av de inbyggda detektorerna för att få ytterligare information.

### <a name="using-the-detector-for-app-service"></a>Använda detektorn för App Service

1. Navigera till din app i portalen.
2. Välj **Diagnostisera och lösa problem**.
3. Välj **Tillgänglighet och prestanda** och välj **Webbapp nedåt.**
4. Sök upp **diagnostik för nyckelvalvsprogram** och klicka på **Mer info**.


### <a name="using-the-detector-for-azure-functions"></a>Använda detektorn för Azure-funktioner

1. Navigera till din app i portalen.
2. Navigera till **plattformsfunktioner.**
3. Välj **Diagnostisera och lösa problem**.
4. Välj **Tillgänglighet och prestanda** och välj **Funktionsapp nedåt eller rapportera fel.**
5. Klicka på **Key Vault Application Settings Diagnostics.**
