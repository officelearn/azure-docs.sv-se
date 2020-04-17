---
title: Använda kundhanterade nycklar för att kryptera dina konfigurationsdata
description: Kryptera dina konfigurationsdata med kundhanterade nycklar
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457441"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Använda kundhanterade nycklar för att kryptera appkonfigurationsdata
Azure App-konfiguration [krypterar känslig information i vila](../security/fundamentals/encryption-atrest.md). Användningen av kundhanterade nycklar ger förbättrat dataskydd genom att du kan hantera dina krypteringsnycklar.  När kryptering av hanterade nycklar används krypteras all känslig information i appkonfiguration med en Azure Key Vault-nyckel som tillhandahålls av användaren.  Detta ger möjlighet att rotera krypteringsnyckeln på begäran.  Det ger också möjlighet att återkalla Azure App-konfigurationens åtkomst till känslig information genom att återkalla appkonfigurationsinstansens åtkomst till nyckeln.

## <a name="overview"></a>Översikt 
Azure App Configuration krypterar känslig information i vila med hjälp av en 256-bitars AES-krypteringsnyckel som tillhandahålls av Microsoft. Varje AppKonfigurationsinstans har sin egen krypteringsnyckel som hanteras av tjänsten och används för att kryptera känslig information. Känslig information innehåller värden som finns i nyckelvärdespar.  När kundhanterad nyckelfunktion är aktiverad använder Appkonfiguration en hanterad identitet som tilldelats appkonfigurationsinstansen för att autentisera med Azure Active Directory. Den hanterade identiteten anropar sedan Azure Key Vault och radbryts i appkonfigurationsinstansens krypteringsnyckel. Den raderade krypteringsnyckeln lagras sedan och den oförpackade krypteringsnyckeln cachelagras i Appkonfiguration i en timme. Appkonfiguration uppdaterar den oförpackade versionen av appkonfigurationsinstansens krypteringsnyckel varje timme. Detta säkerställer tillgänglighet under normala driftsförhållanden. 

>[!IMPORTANT]
> Om den identitet som tilldelats appkonfigurationsinstansen inte längre har behörighet att packa upp instansens krypteringsnyckel, eller om den hanterade nyckeln tas bort permanent, går det inte längre att dekryptera känslig information som lagras i appkonfigurationsinstansen. Om du använder Azure Key Vaults [mjuka borttagningsfunktion](../key-vault/general/overview-soft-delete.md) minskar risken för att krypteringsnyckeln tas bort av misstag.

När användare aktiverar kundens hanterade nyckelfunktioner i sin Azure App Configuration-instans styr de tjänstens förmåga att komma åt känslig information. Den hanterade nyckeln fungerar som en rotkrypteringsnyckel. En användare kan återkalla åtkomsten till sin appkonfigurationsinstans till sin hanterade nyckel genom att ändra sin princip för nyckelvalvsåtkomst. När den här åtkomsten återkallas förlorar appkonfigurationen möjligheten att dekryptera användardata inom en timme. Nu förbjuder appkonfigurationsinstansen alla åtkomstförsök. Denna situation kan återställas genom att tjänsten beviljas åtkomst till den hanterade nyckeln igen.  Inom en timme kommer App Configuration att kunna dekryptera användardata och fungera under normala förhållanden.

>[!NOTE]
>Alla Azure App-konfigurationsdata lagras i upp till 24 timmar i en isolerad säkerhetskopia. Detta inkluderar den oförpackade krypteringsnyckeln. Dessa data är inte omedelbart tillgängliga för tjänsten eller serviceteamet. I händelse av en nödåterställning återkallas Azure App-konfigurationen igen från de hanterade nyckeldata.

## <a name="requirements"></a>Krav
Följande komponenter krävs för att kunna aktivera den kundhanterade nyckelfunktionen för Azure App-konfiguration:
- Konfigurationsinstans på standardnivå
- Azure Key Vault med funktioner för mjuk borttagning och rensningsskydd aktiverat
- En RSA- eller RSA-HSM-nyckel i nyckelvalvet
    - Nyckeln får inte ha upphört att gälla, den måste vara aktiverad och den måste ha både in- och uppbrytarfunktioner aktiverade

När dessa resurser har konfigurerats återstår två steg för att låta Azure App-konfigurationen använda key vault-nyckeln:
1. Tilldela en hanterad identitet till Azure App Configuration-instansen
2. Bevilja `GET`identiteten `WRAP`, `UNWRAP` och behörigheterna i målnyckelvalvets åtkomstprincip.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Aktivera kundhanterad nyckelkryptering för din Azure App-konfigurationsinstans
Till att börja med behöver du en korrekt konfigurerad Azure App Configuration-instans. Om du ännu inte har en appkonfigurationsinstans tillgänglig följer du en av dessa snabbstarter för att ställa in en:
- [Skapa en ASP.NET Core-app med Azure App Configuration](quickstart-aspnet-core-app.md)
- [Skapa en .NET Core-app med Azure App-konfiguration](quickstart-dotnet-core-app.md)
- [Skapa en .NET Framework-app med Azure App Configuration](quickstart-dotnet-app.md)
- [Skapa en Java Spring-app med Azure App-konfiguration](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell är ett kostnadsfritt interaktivt skal som du kan använda för att köra kommandoradsinstruktionerna i den här artikeln.  Den har vanliga Azure-verktyg förinstallerade, inklusive .NET Core SDK. Om du är inloggad på din Azure-prenumeration startar du ditt [Azure Cloud Shell](https://shell.azure.com) från shell.azure.com.  Du kan läsa mer om Azure Cloud Shell genom [att läsa vår dokumentation](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Skapa och konfigurera ett Azure Key Vault
1. Skapa ett Azure Key Vault med Azure CLI.  Observera att `vault-name` `resource-group-name` båda och tillhandahålls av användaren och måste vara unika.  Vi `contoso-vault` använder `contoso-resource-group` och i dessa exempel.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Aktivera mjuk-borttagning och rensningsskydd för Key Vault. Ersätt namnen på nyckelvalvet (`contoso-vault`)`contoso-resource-group`och resursgruppen ( ) som skapats i steg 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Skapa en Key Vault-nyckel. Ange en `key-name` unik för den här nyckeln och`contoso-vault`ersätt namnen på key vault ( ) som skapats i steg 1. Ange om `RSA` du `RSA-HSM` vill eller kryptera.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Utdata från det här kommandot visar nyckel-ID ("kid") för den genererade nyckeln.  Anteckna nyckel-ID:et som ska användas senare i den här övningen.  Nyckel-ID:et `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`har formen: .  Nyckel-ID:et har tre viktiga komponenter:
    1. Key Vault URI: https://{my key vault}.vault.azure.net
    1. Nyckelvalvets nyckelnamn: {Nyckelnamn}
    1. Nyckelvalvs nyckelversion: {Nyckelversion}

1. Skapa en systemtilldelad hanterad identitet med Hjälp av Azure CLI och ersätter namnet på din AppKonfigurationsinstans och resursgrupp som användes i föregående steg. Den hanterade identiteten används för att komma åt den hanterade nyckeln. Vi `contoso-app-config` använder för att illustrera namnet på en App Configuration-instans:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    Utdata för det här kommandot innehåller huvud-ID ("principalId") och klient-ID ("tenandId") för den tilldelade datorn.  Detta kommer att användas för att bevilja identitetsåtkomst till den hanterade nyckeln.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Den hanterade identiteten för Azure App Configuration-instansen behöver åtkomst till nyckeln för att utföra nyckelvalidering, kryptering och dekryptering. Den specifika uppsättning åtgärder som den `GET`behöver `WRAP`åtkomst `UNWRAP` till innehåller: , och för nycklar.  För att bevilja åtkomsten krävs huvud-ID för appkonfigurationsinstansens hanterade identitet. Det här värdet erhölls i föregående steg. Det visas nedan `contoso-principalId`som . Bevilja behörighet till den hanterade nyckeln med kommandoraden:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. När Azure App Configuration-instansen kan komma åt den hanterade nyckeln kan vi aktivera den kundhanterade nyckelfunktionen i tjänsten med Hjälp av Azure CLI. Återkalla följande egenskaper som registrerats `key name` `key vault URI`under stegen för att skapa nyckeln: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Azure App Configuration-instansen är nu konfigurerad för att använda en kundhanterad nyckel som lagras i Azure Key Vault.

## <a name="next-steps"></a>Efterföljande moment
I den här artikeln har du konfigurerat din Azure App Configuration-instans för att använda en kundhanterad nyckel för kryptering.  Lär dig hur du [integrerar din tjänst med Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).