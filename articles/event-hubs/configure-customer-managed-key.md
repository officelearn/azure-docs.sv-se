---
title: Konfigurera din egen nyckel för att kryptera Azure Event Hubs-data i vila
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för kryptering av Azure Event Hubs-datastöd.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 43e626355feaf1e51fc840f82506c559a1859b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621993"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurera kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila med hjälp av Azure-portalen
Azure Event Hubs tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Event Hubs förlitar sig på Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med Microsoft-hanterade nycklar. 

## <a name="overview"></a>Översikt
Azure Event Hubs stöder nu möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller kundhanterade nycklar (Bring Your Own Key – BYOK). Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomsten till de kundhanterade nycklar som används för att kryptera Azure Event Hubs-data i vila.

Att aktivera BYOK-funktionen är en engångsinställningsprocess på ditt namnområde.

> [!NOTE]
> BYOK-funktionen stöds av [Event Hubs dedikerade](event-hubs-dedicated-overview.md) kluster med en klientorganisation. Det går inte att aktivera för vanliga namnområden för Event Hubs.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckelanvändning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

Den här artikeln visar hur du konfigurerar ett nyckelvalv med kundhanterade nycklar med hjälp av Azure-portalen. Mer information om hur du skapar ett nyckelvalv med Azure-portalen finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Om du använder kundhanterade nycklar med Azure Event Hubs krävs att nyckelvalvet har två obligatoriska egenskaper konfigurerade. De är: **Mjuk Ta bort** och inte **rensa**. Dessa egenskaper är aktiverade som standard när du skapar ett nytt nyckelvalv i Azure-portalen. Om du behöver aktivera dessa egenskaper i ett befintligt nyckelvalv måste du dock använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar
Så här aktiverar du kundhanterade nycklar i Azure-portalen:

1. Navigera till ditt dedikerade kluster för händelsehubbar.
1. Markera det namnområde där du vill aktivera BYOK.
1. På sidan **Inställningar** i namnområdet Händelserbubbar väljer du **Kryptering**. 
1. Välj krypteringen **som hanteras av kund med den kundhanterade nyckeln** i vila enligt följande bild. 

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckelvalv med nycklar
När du har aktiverat kundhanterade nycklar måste du associera den hanterade nyckeln för kunden med namnområdet Azure Event Hubs. Event Hubs stöder endast Azure Key Vault. Om du aktiverar alternativet **Kryptering med kundhanterad nyckel** i föregående avsnitt måste du ha nyckeln importerad till Azure Key Vault. Dessutom måste nycklarna ha **Mjuk borttagning** och **Rensa inte** konfigurerad för nyckeln. Dessa inställningar kan konfigureras med [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) eller [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Om du vill skapa ett nytt nyckelvalv följer du [snabbstarten](../key-vault/key-vault-overview.md)för Azure Key Vault . Mer information om hur du importerar befintliga nycklar finns i [Om nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).
1. Om du vill aktivera både mjukt borttagnings- och rensningsskydd när du skapar ett valv använder du kommandot [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensningsskydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du kommandot [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel väljer du **Generera/importera** på **Menyn Nycklar** under **Inställningar**.
        
        ![Välj knappen Generera/importera](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 
    1. Du kan nu välja den här nyckeln för att associera med namnområdet Event Hubs för kryptering från listrutan. 

        ![Välj nyckel från nyckelvalv](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Fyll i informationen för nyckeln och klicka på **Välj**. Detta gör det möjligt att kryptera data i vila på namnområdet med en kundhanterad nyckel. 


## <a name="rotate-your-encryption-keys"></a>Rotera krypteringsnycklarna
Du kan rotera nyckeln i nyckelvalvet med hjälp av rotationsmekanismen För Azure Key Vaults. Mer information finns i [Konfigurera nyckelrotation och granskning](../key-vault/key-vault-key-rotation-log-monitoring.md). Aktiverings- och utgångsdatum kan också ställas in för att automatisera tangentrotationen. Tjänsten Event Hubs identifierar nya nyckelversioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar
Om du återkallar åtkomst till krypteringsnycklarna rensas inte data från händelsehubbar. Det går dock inte att komma åt data från namnområdet Event Hubs. Du kan återkalla krypteringsnyckeln via åtkomstprincipen eller genom att ta bort nyckeln. Läs mer om åtkomstprinciper och skydda nyckelvalvet från [säker åtkomst till ett nyckelvalv](../key-vault/key-vault-secure-your-key-vault.md).

När krypteringsnyckeln har återkallats blir tjänsten Event Hubs på det krypterade namnområdet obrukbar. Om åtkomsten till nyckeln är aktiverad eller borttagningsnyckeln återställs, väljer eventhubbar-tjänsten nyckeln så att du kan komma åt data från det krypterade händelsehubbarnamnområdet.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar 
Genom att ange diagnostikloggar för BYOK-aktiverade namnområden får du den information som krävs om åtgärderna när ett namnområde krypteras med kundhanterade nycklar. Dessa loggar kan aktiveras och senare strömma till en händelsehubb eller analyseras via logganalys eller strömmas till lagring för att utföra anpassade analyser. Mer information om diagnostikloggar finns i [Översikt över Azure Diagnostic-loggar](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Aktivera användarloggar
Följ dessa steg för att aktivera loggar för kundhanterade nycklar.

1. I Azure-portalen navigerar du till namnområdet som har BYOK aktiverat.
1. Välj **Diagnostikinställningar** under **Övervakning**.

    ![Välj diagnostikinställningar](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Välj **+Lägg till diagnostikinställning**. 

    ![Välj lägg till diagnostikinställning](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Ange ett **namn** och välj var du vill strömma loggarna till.
1. Välj **CustomerManagedKeyUserLogs** och **Spara**. Den här åtgärden aktiverar loggarna för BYOK på namnområdet.

    ![Välj alternativet Kundhanterad nyckelanvändare](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Loggschema 
Alla loggar lagras i JSON-format (JavaScript Object Notation). Varje post har strängfält som använder det format som beskrivs i följande tabell. 

| Namn | Beskrivning |
| ---- | ----------- | 
| TaskName (3500 | Beskrivning av aktiviteten som misslyckades. |
| ActivityId (på)ActivityId) | Internt ID som används för spårning. |
| category | Definierar klassificeringen av aktiviteten. Om nyckeln från nyckelvalvet till exempel inaktiveras, skulle det vara en informationskategori eller om en nyckel inte kan packas upp, kan den hamna i fel. |
| resourceId | Resurs-ID för Azure Resource Manager |
| keyVault (keyVault) | Fullständigt namn på nyckelvalvet. |
| key | Nyckelnamnet som används för att kryptera namnområdet Händelserhubbar. |
| version | Den version av nyckeln som används. |
| Drift | Åtgärden som utförs på nyckeln i nyckelvalvet. Inaktivera/aktivera till exempel nyckeln, figursluta eller packa upp |
| kod | Koden som är associerad med åtgärden. Exempel: Felkod, 404 betyder att nyckeln inte hittades. |
| meddelande | Alla felmeddelanden som är associerade med åtgärden |

Här är ett exempel på loggen för en kundhanterad nyckel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Använda Resource Manager-mall för att aktivera kryptering
I det här avsnittet visas hur du utför följande uppgifter med **Azure Resource Manager-mallar**. 

1. Skapa ett **namnområde för händelsehubbar** med en hanterad tjänstidentitet.
2. Skapa ett **nyckelvalv** och ge tjänsten identitetsåtkomst till nyckelvalvet. 
3. Uppdatera namnområdet Event Hubs med nyckelvalvsinformation (nyckel/värde). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Skapa ett eventhubs-kluster och namnområde med hanterad tjänstidentitet
I det här avsnittet visas hur du skapar ett Namnområde för Azure Event Hubs med hanterad tjänstidentitet med hjälp av en Azure Resource Manager-mall och PowerShell. 

1. Skapa en Azure Resource Manager-mall för att skapa ett namnområde för eventhubbar med en hanterad tjänstidentitet. Namn på filen: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Skapa en mallparameterfil med namnet: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<EventHubsClusterName>`- Namn på ditt eventhubbarkung    
    > - `<EventHubsNamespaceName>`- Namn på namnområdet Event Hubs
    > - `<Location>`- Plats för namnområdet Event Hubs

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Kör följande PowerShell-kommando för att distribuera mallen för att skapa ett namnområde för händelsehubbar. Hämta sedan ID:n för namnområdet Event Hubs för att använda det senare. Ersätt `{MyRG}` med namnet på resursgruppen innan kommandot körs.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Bevilja namnområdesidentitetsåtkomst för händelsehubbar till nyckelvalvet

1. Kör följande kommando för att skapa ett nyckelvalv med **rensningsskydd** och **mjuk borttagning** aktiverat. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    Jag vet inte vad du ska ta på dig.    
    
    Kör följande kommando för att uppdatera ett **befintligt nyckelvalv**. Ange värden för resursgrupps- och nyckelvalvsnamn innan kommandot körs. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ange principen för nyckelvalvsåtkomst så att den hanterade identiteten för namnområdet Event Hubs kan komma åt nyckelvärdet i nyckelvalvet. Använd ID:t för namnområdet Händelserhubbar från föregående avsnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Kryptera data i namnområde för händelsehubbar med kundhanterad nyckel från nyckelvalvet
Du har gjort följande steg hittills: 

1. Skapade ett premiumnamnområde med en hanterad identitet.
2. Skapa ett nyckelvalv och bevilja den hanterade identitetsåtkomsten till nyckelvalvet. 

I det här steget uppdaterar du namnområdet Event Hubs med information om nyckelvalv. 

1. Skapa en JSON-fil med namnet **CreateEventHubClusterAndNamespace.json** med följande innehåll: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Skapa en mallparameterfil: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<EventHubsClusterName>`- Namnet på ditt eventhubbark.        
    > - `<EventHubsNamespaceName>`- Namn på namnområdet Event Hubs
    > - `<Location>`- Plats för namnområdet Event Hubs
    > - `<KeyVaultName>`- Namn på din nyckel valv
    > - `<KeyName>`- Namnet på nyckeln i nyckelvalvet

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Kör följande PowerShell-kommando för att distribuera Resource Manager-mallen. Ersätt `{MyRG}` med namnet på resursgruppen innan du kör kommandot. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Felsöka
Bästa praxis är att alltid aktivera loggar som visas i föregående avsnitt. Det hjälper till att spåra aktiviteter när BYOK-kryptering är aktiverat. Det hjälper också att scoping ner problemen.

Följande är de vanligaste felkoderna att leta efter när BYOK-kryptering är aktiverad.

| Åtgärd | Felkod | Resulterande datatillstånd |
| ------ | ---------- | ----------------------- | 
| Ta bort behörigheten för figursättning/uppspömning från ett nyckelvalv | 403 |    Otillgängliga |
| Ta bort AAD-rollmedlemskap från ett AAD-huvudnamn som beviljade behörigheten radbrytning/upprrap | 403 |  Otillgängliga |
| Ta bort en krypteringsnyckel från nyckelvalvet | 404 | Otillgängliga |
| Ta bort nyckelvalvet | 404 | Oåtkomlig (förutsätter att mjuk borttagning är aktiverat, vilket är en obligatorisk inställning.) |
| Ändra förfallotiden på krypteringsnyckeln så att den redan har gått ut | 403 |   Otillgängliga  |
| Ändra NBF (inte tidigare) så att nyckelkrypteringsnyckeln inte är aktiv | 403 | Otillgängliga  |
| Välja alternativet **Tillåt MSFT-tjänster** för nyckelvalvsbrandväggen eller på annat sätt blockera nätverksåtkomst till nyckelvalvet som har krypteringsnyckeln | 403 | Otillgängliga |
| Flytta nyckelvalvet till en annan klient | 404 | Otillgängliga |  
| Återkommande nätverksproblem eller DNS/AAD/MSI-avbrott |  | Tillgänglig med cachelagrade datakrypteringsnyckel |

> [!IMPORTANT]
> Om du vill aktivera Geo-DR på ett namnområde som använder BYOK-krypteringen måste det sekundära namnområdet för parkoppling vara i ett dedikerat kluster och ha en systemtilldelad hanterad identitet aktiverad på den. Mer information finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över Event Hubs](event-hubs-about.md)
- [Översikt över Nyckelvalv](../key-vault/key-vault-overview.md)




