---
title: Kryptera programkällan i vila
description: Kryptera dina programdata i Azure Storage och distribuera dem som en paketfil.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62179e900ace0d6d7b8b1f07e8f0ab685508f991
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408732"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med hjälp av kundhanterade nycklar

Om du krypterar funktionsappens programdata i vila krävs ett Azure Storage-konto och ett Azure Key Vault. Dessa tjänster används när du kör din app från ett distributionspaket.

  - [Azure Storage tillhandahåller kryptering i vila](../storage/common/storage-service-encryption.md). Du kan använda nycklar som tillhandahålls av systemet eller dina egna kundhanterade nycklar. Det är här dina programdata lagras när de inte körs i en funktionsapp i Azure.
  - [Körs från ett distributionspaket] ((run-functions-from-deployment-package.md) är en distributionsfunktion i App Service. Det gör att du kan distribuera webbplatsinnehåll från ett Azure Storage-konto med hjälp av en SAS-URL (Shared Access Signature).
  - [Key Vault-referenser](../app-service/app-service-key-vault-references.md) är en säkerhetsfunktion i App Service. Det låter dig importera hemligheter vid körning som programinställningar. Använd detta för att kryptera SAS-URL:en för ditt Azure Storage-konto.

## <a name="set-up-encryption-at-rest"></a>Konfigurera kryptering i vila

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Skapa först [ett Azure Storage-konto](../storage/common/storage-account-create.md) och [kryptera det med kundhanterade nycklar](../storage/common/encryption-customer-managed-keys.md). När lagringskontot har skapats använder du [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp paketfiler.

Använd sedan Lagringsutforskaren för att [generera en SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Spara den här SAS-URL:en, detta används senare för att möjliggöra säker åtkomst av distributionspaketet vid körning.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurera körs från ett paket från ditt lagringskonto
  
När du har laddat upp filen till Blob-lagring och `WEBSITE_RUN_FROM_PACKAGE` har en SAS-URL för filen anger du programinställningen till SAS-URL:en. I följande exempel kan du använda Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Om du lägger till den här programinställningen startas funktionsappen om. När appen har startats om bläddrar du till den och kontrollerar att appen har startat korrekt med distributionspaketet. Om programmet inte startade korrekt läser du [felsökningsguiden för Kör från paket](run-functions-from-deployment-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Kryptera programinställningen med key vault-referenser

Nu kan du ersätta `WEBSITE_RUN_FROM_PACKAGE` värdet för programinställningen med en Referens för Nyckelvalv till den SAS-kodade URL:en. Detta håller SAS-URL:en krypterad i Key Vault, vilket ger ett extra lager av säkerhet.

1. Använd följande [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) kommando för att skapa en Key Vault-förekomst.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Följ [de här instruktionerna för att ge appen åtkomst](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) till nyckelvalvet:

1. Använd följande [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) kommando för att lägga till din externa WEBBADRESS som en hemlighet i nyckelvalvet:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Använd följande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) kommando för `WEBSITE_RUN_FROM_PACKAGE` att skapa programinställningen med värdet som referens till den externa URL:en:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Den `<secret-version>` kommer att vara i `az keyvault secret set` utdata från föregående kommando.

Om du uppdaterar den här programinställningen startas funktionsappen om. När appen har startats om, bläddra till den se till att den har startat korrekt med hjälp av Key Vault referens.

## <a name="how-to-rotate-the-access-token"></a>Så här roterar du åtkomsttoken

Det är bäst att regelbundet rotera SAS-nyckeln för ditt lagringskonto. Om du vill vara säkra på att funktionsappen inte oavsiktligt förlorar åtkomsten måste du också uppdatera SAS-URL:en i Key Vault.

1. Rotera SAS-tangenten genom att navigera till ditt lagringskonto i Azure-portalen. Under **Inställningar** > **Access-tangenten**klickar du på ikonen för att rotera SAS-tangenten.

1. Kopiera den nya SAS-URL:en och använd följande kommando för att ange den uppdaterade SAS-URL:en i nyckelvalvet:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Uppdatera nyckelvalvets referens i programinställningen till den nya hemliga versionen:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Den `<secret-version>` kommer att vara i `az keyvault secret set` utdata från föregående kommando.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Så här återkallar du funktionsappens dataåtkomst

Det finns två metoder för att återkalla funktionsappens åtkomst till lagringskontot. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Rotera SAS-nyckeln för Azure Storage-kontot

Om SAS-nyckeln för lagringskontot roteras har funktionsappen inte längre åtkomst till lagringskontot, men den fortsätter att köras med den senast nedladdade versionen av paketfilen. Starta om funktionsappen för att rensa den senast nedladdade versionen.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Ta bort funktionsappens åtkomst till Key Vault

Du kan återkalla funktionsappens åtkomst till webbplatsdata genom att inaktivera funktionsappens åtkomst till Key Vault. Det gör du genom att ta bort åtkomstprincipen för funktionsappens identitet. Det här är samma identitet som du skapade tidigare när du konfigurerade nyckelvalvsreferenser.

## <a name="summary"></a>Sammanfattning

Dina programfiler krypteras nu i vila i ditt lagringskonto. När funktionsappen startar hämtas SAS-URL:en från nyckelvalvet. Slutligen läser funktionsappen in programfilerna från lagringskontot. 

Om du behöver återkalla funktionsappens åtkomst till ditt lagringskonto kan du antingen återkalla åtkomsten till nyckelvalvet eller rotera lagringskontonycklarna, vilket gör SAS-URL:en ogiltig.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Finns det någon extra kostnad för att köra min funktionsapp från distributionspaketet?

Endast kostnaden som är associerad med Azure Storage-kontot och eventuella tillämpliga utgående avgifter.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Hur påverkar det min funktionsapp som körs från distributionspaketet?

- Om du kör appen `wwwroot/` från distributionspaketet är det skrivskyddat. Appen får ett felmeddelande när den försöker skriva till den här katalogen.
- TAR- och GZIP-format stöds inte.
- Den här funktionen är inte kompatibel med lokal cache.

## <a name="next-steps"></a>Nästa steg

- [Key Vault-referenser för App Service](../app-service/app-service-key-vault-references.md)
- [Azure Storage-kryptering för data i vila](../storage/common/storage-service-encryption.md)
