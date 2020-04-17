---
title: Konfigurera din egen nyckel för att kryptera Azure Service Bus-data i vila
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för kryptering av Azure Service Bus-datastöd.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: 82a5fbef8c307d60d82b147f04a2a687b8b0433e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459074"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurera kundhanterade nycklar för kryptering av Azure Service Bus-data i vila med hjälp av Azure-portalen
Azure Service Bus Premium tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Service Bus Premium förlitar sig på Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med Microsoft-hanterade nycklar. 

## <a name="overview"></a>Översikt
Azure Service Bus stöder nu möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller kundhanterade nycklar (Bring Your Own Key - BYOK). Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomsten till de kundhanterade nycklar som används för att kryptera Azure Service Bus i vila.

Att aktivera BYOK-funktionen är en engångsinställningsprocess på ditt namnområde.

> [!NOTE]
> Det finns några förbehåll till kunden hanterade nyckeln för kryptering på tjänstsidan. 
>   * Den här funktionen stöds av [Azure Service Bus Premium-nivån.](service-bus-premium-messaging.md) Det går inte att aktivera för standardnivå servicebussnamnområden.
>   * Krypteringen kan bara aktiveras för nya eller tomma namnområden. Om namnområdet innehåller data misslyckas krypteringsåtgärden.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckelanvändning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

Den här artikeln visar hur du konfigurerar ett nyckelvalv med kundhanterade nycklar med hjälp av Azure-portalen. Mer information om hur du skapar ett nyckelvalv med Azure-portalen finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> Om du använder kundhanterade nycklar med Azure Service Bus krävs att nyckelvalvet har två obligatoriska egenskaper konfigurerade. De är: **Mjuk Ta bort** och inte **rensa**. Dessa egenskaper är aktiverade som standard när du skapar ett nytt nyckelvalv i Azure-portalen. Om du behöver aktivera dessa egenskaper i ett befintligt nyckelvalv måste du dock använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar
Så här aktiverar du kundhanterade nycklar i Azure-portalen:

1. Navigera till ditt Service Bus Premium-namnområde.
2. På sidan **Inställningar** i tjänstbussens namnområde väljer du **Kryptering**.
3. Välj krypteringen **som hanteras av kund med den kundhanterade nyckeln** i vila enligt följande bild.

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckelvalv med nycklar

När du har aktiverat kundhanterade nycklar måste du associera den hanterade nyckeln för kunden med namnområdet Azure Service Bus. Service Bus stöder endast Azure Key Vault. Om du aktiverar alternativet **Kryptering med kundhanterad nyckel** i föregående avsnitt måste du ha nyckeln importerad till Azure Key Vault. Dessutom måste nycklarna ha **Mjuk borttagning** och **Rensa inte** konfigurerad för nyckeln. Dessa inställningar kan konfigureras med [PowerShell](../key-vault/general/soft-delete-powershell.md) eller [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Om du vill skapa ett nytt nyckelvalv följer du [snabbstarten](../key-vault/general/overview.md)för Azure Key Vault . Mer information om hur du importerar befintliga nycklar finns i [Om nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).
1. Om du vill aktivera både mjukt borttagnings- och rensningsskydd när du skapar ett valv använder du kommandot [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensningsskydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du kommandot [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel väljer du **Generera/importera** på **Menyn Nycklar** under **Inställningar**.
        
        ![Välj knappen Generera/importera](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 

    1. Du kan nu välja den här nyckeln för att associera med Service Bus-namnområdet för kryptering från listrutan. 

        ![Välj nyckel från nyckelvalv](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > För redundans kan du lägga till upp till 3 nycklar. I händelse av att en av nycklarna har upphört att gälla, eller inte är tillgänglig, kommer de andra nycklarna att användas för kryptering.
        
    1. Fyll i informationen för nyckeln och klicka på **Välj**. Detta gör det möjligt att kryptera data i vila på namnområdet med en kundhanterad nyckel. 


    > [!IMPORTANT]
    > Om du funderar på att använda kundhanterad nyckel tillsammans med Geo disaster recovery, läs nedan - 
    >
    > För att aktivera kryptering i vila med kundhanterad nyckel ställs en [åtkomstprincip](../key-vault/general/secure-your-key-vault.md) in för Service Bus hanterade identitet på den angivna Azure KeyVault. Detta säkerställer kontrollerad åtkomst till Azure KeyVault från Azure Service Bus-namnområdet.
    >
    > På grund av detta:
    > 
    >   * Om [geokatastrofåterställning](service-bus-geo-dr.md) redan är aktiverat för servicebussnamnområdet och du vill aktivera kundhanterad nyckel 
    >     * Bryt ihopkopplingen
    >     * [Ställ in åtkomstprincipen](../key-vault/general/managed-identity.md) för den hanterade identiteten för både primära och sekundära namnområden till nyckelvalvet.
    >     * Konfigurera kryptering på det primära namnområdet.
    >     * Para ihop de primära och sekundära namnområdena igen.
    > 
    >   * Om du vill aktivera Geo-DR på ett servicebussnamnområde där kundhanterad nyckel redan har ställts in,
    >     * [Ställ in åtkomstprincipen](../key-vault/general/managed-identity.md) för den hanterade identiteten för det sekundära namnområdet till nyckelvalvet.
    >     * Para ihop de primära och sekundära namnområdena.


## <a name="rotate-your-encryption-keys"></a>Rotera krypteringsnycklarna

Du kan rotera nyckeln i nyckelvalvet med hjälp av rotationsmekanismen För Azure Key Vaults. Mer information finns i [Konfigurera nyckelrotation och granskning](../key-vault/secrets/key-rotation-log-monitoring.md). Aktiverings- och utgångsdatum kan också ställas in för att automatisera tangentrotationen. Tjänsten Service Bus identifierar nya nyckelversioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar

Om du återkallar åtkomst till krypteringsnycklarna rensas inte data från Service Bus. Det går dock inte att komma åt data från servicebussens namnområde. Du kan återkalla krypteringsnyckeln via åtkomstprincipen eller genom att ta bort nyckeln. Läs mer om åtkomstprinciper och skydda nyckelvalvet från [säker åtkomst till ett nyckelvalv](../key-vault/general/secure-your-key-vault.md).

När krypteringsnyckeln har återkallats blir servicebusstjänsten på det krypterade namnområdet obrukbar. Om åtkomsten till nyckeln är aktiverad eller om den borttagna nyckeln återställs, väljer Service Bus-tjänsten nyckeln så att du kan komma åt data från det krypterade servicebussnamnområdet.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Använda Resource Manager-mall för att aktivera kryptering
I det här avsnittet visas hur du utför följande uppgifter med **Azure Resource Manager-mallar**. 

1. Skapa **premium** ett premiumservicebussnamnområde med en **hanterad tjänstidentitet**.
2. Skapa ett **nyckelvalv** och ge tjänsten identitetsåtkomst till nyckelvalvet. 
3. Uppdatera servicebussens namnområde med nyckelvalvsinformationen (nyckel/värde). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Skapa ett premiumservicebussnamnområde med hanterad tjänstidentitet
I det här avsnittet visas hur du skapar ett Azure Service Bus-namnområde med hanterad tjänstidentitet med hjälp av en Azure Resource Manager-mall och PowerShell. 

1. Skapa en Azure Resource Manager-mall för att skapa ett namnområde för premiumnivå för servicebuss med en hanterad tjänstidentitet. Namn på filen: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Skapa en mallparameterfil med namnet: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<ServiceBusNamespaceName>`- Namn på din Service Bus namnområde
    > - `<Location>`- Plats för din servicebuss namnområde

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Kör följande PowerShell-kommando för att distribuera mallen för att skapa ett premium servicebussnamnområde. Hämta sedan ID:n för servicebussens namnområde för att använda det senare. Ersätt `{MyRG}` med namnet på resursgruppen innan kommandot körs.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Bevilja servicebussnamnområdesidentitetsåtkomst till nyckelvalvet

1. Kör följande kommando för att skapa ett nyckelvalv med **rensningsskydd** och **mjuk borttagning** aktiverat. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    Jag vet inte vad du ska ta på dig.
    
    Kör följande kommando för att uppdatera ett **befintligt nyckelvalv**. Ange värden för resursgrupps- och nyckelvalvsnamn innan kommandot körs. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ange principen för nyckelvalvsåtkomst så att den hanterade identiteten för Service Bus-namnområdet kan komma åt nyckelvärdet i nyckelvalvet. Använd ID:t för servicebussens namnområde från föregående avsnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Kryptera data i Service Bus-namnområdet med kundhanterad nyckel från nyckelvalvet
Du har gjort följande steg hittills: 

1. Skapade ett premiumnamnområde med en hanterad identitet.
2. Skapa ett nyckelvalv och bevilja den hanterade identitetsåtkomsten till nyckelvalvet. 

I det här steget uppdaterar du servicebussens namnområde med information om nyckelvalv. 

1. Skapa en JSON-fil med namnet **UpdateServiceBusNamespaceMedEncryption.json** med följande innehåll: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. Skapa en mallparameterfil: **UpdateServiceBusNamespaceMedEncryptionParams.json**.

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<ServiceBusNamespaceName>`- Namn på din Service Bus namnområde
    > - `<Location>`- Plats för din servicebuss namnområde
    > - `<KeyVaultName>`- Namn på din nyckel valv
    > - `<KeyName>`- Namnet på nyckeln i nyckelvalvet  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över servicebuss](service-bus-messaging-overview.md)
- [Översikt över Nyckelvalv](../key-vault/general/overview.md)


