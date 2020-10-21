---
title: Kundhanterade nycklar eller ta med din egen nyckel (BYOK) med Media Services
description: Det här är en själv studie kurs om hur du använder Kundhanterade nycklar med ett Media Services lagrings konto
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326167"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Självstudie: Använd Kundhanterade nycklar eller ta med din egen nyckel (BYOK) med Media Services

Med 2020-05-01-API: et kan du använda en kundhanterad RSA-nyckel med ett Media Services konto som har en Systemhanterad identitet.  Den här självstudien innehåller en Postman-samling och-miljö för att skicka REST-begäranden till Azure-tjänster.  Tjänster som används:

- Azure Active Directory program registrering för Postman
- Microsoft Graph API
- Azure-lagring
- Azure Key Vault
- Media Services

I den här självstudien får du lära dig att använda Postman för att:

> [!div class="checklist"]
> * Hämta token för användning med Azure-tjänster.
> * Skapa en resurs grupp och ett lagrings konto.
> * Skapa ett Media Services-konto med en Systemhanterad identitet
> * Skapa en Key Vault för att lagra en RSA-nyckel (Customer Managed) för användning med lagrings kontot.
> * Uppdatera Media Services kontot för att använda RSA-nyckeln med lagrings kontot.
> * Använd variabler i Postman.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- Registrera ett huvud namn för tjänsten med rätt behörigheter.
- Installera [Postman](https://www.postman.com).
- Hämta Postman-samlingen för den här själv studie kursen på [Azure-exempel: Media-Services-Kundhanterade nycklar-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrera ett huvud namn för tjänsten med de behörigheter som krävs

[Skapa ett huvud namn för tjänsten](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Se [alternativ två](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) för att hämta hemligheten för tjänstens huvud namn.  Observera hemligheten någonstans, precis som när du lämnar den hemliga sidan i portalen går det inte att komma åt den.

Tjänstens huvud namn måste ha följande behörigheter för att kunna utföra uppgifterna i den här självstudien.

![de behörigheter som krävs för tjänstens huvud namn](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Installera Postman

Om du inte redan har installerat Postman för användning med Azure kan du hämta den på [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Hämta och importera samlingen

Hämta Postman-samlingen för den här själv studie kursen på [Azure-exempel: Media-Services-Kundhanterade nycklar-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Installation av samling och miljö

1. Kör Postman.
1. Välj **Importera**.
1. Välj **överför filer**.
1. Navigera till den plats där du har sparat samlingen och miljö filerna.
1. Välj samling och miljö fil.
1. Välj **Öppna**.  (En varning visas om att filerna inte importeras som ett API, utan som samlingar.  Detta är bra.  Det är vad du vill.)
1. Den här samlingen visas nu i samlingarna som BYOK.
1. Miljövariablerna visas nu i dina miljöer.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Förstå REST API begär anden i samlingen

Samlingen ger följande REST API begär Anden. Begär Anden måste skickas i den ordning som de flesta har test skript som dynamiskt skapar globala variabler för nästa (eller efterföljande) begäran i sekvensen. Du behöver inte skapa de globala variablerna manuellt.

I Postman visas dessa variabler inom `{{ }}` hakparenteser.  Exempelvis `{{bearerToken}}`.

1. Hämta AAD-token – testet anger den globala variabeln *bearerToken*
2. Hämta Graph-token – testet anger den globala variabeln *graphToken*
3. Hämta information om tjänstens huvud namn – testet anger den globala variabeln *servicePrincipalObjectId*
4. Skapa ett lagrings konto – testet anger den globala variabeln *storageAccountId*
5. Skapa ett Media Services konto med en Systemhanterad identitet – testet anger den globala variabeln *principalId*
6. Skapa en Key Vault som beviljar åtkomst till tjänstens huvud namn – testet anger den globala variabeln *keyVaultId*
7. Hämta en Key Vault token – testet anger den globala variabeln *keyVaultToken*
8. Skapa RSA-nyckel i Key Vault – testet ställer in global variabel *keyId*
9. Uppdatera Media Services kontot för att använda nyckeln med lagrings kontot – det finns inget test skript för den här begäran.

## <a name="define-environment-variables"></a>Definiera miljövariabler

1. Växla till den miljö som du laddade ned genom att välja List rutan miljöer.
1. Upprätta miljövariabler i Postman. De används också som variabler i `{{ }}` hakparenteser.  Exempelvis `{{tenantId}}`.

    * *tenantId* = klient-ID
    * *servicePrincipalId* = ID för tjänstens huvud namn som du upprättar med din favorit metod: Portal, CLI och så vidare.
    * *servicePrincipalSecret* = hemligheten som skapats för tjänstens huvud namn
    * *prenumeration* = ditt PRENUMERATIONS-ID
    * *storageName* = det namn som du vill ge ditt lagrings utrymme
    * *accountName* = det media service konto namn som du vill använda
    * *keyVaultName* = det Key Vault namn som du vill använda
    * *resourceLocation* = Central (eller där du vill placera dina resurser.  Den här samlingen har bara testats med centrala.)
    * *resourceGroup* = resurs gruppens namn

    Följande variabler är standard för att arbeta med Azure-resurser, så du behöver inte ändra dem.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>Skicka begär Anden

När dina miljövariabler har definierats kan du antingen köra begär anden en i taget i ordningen ovan eller använda Postman-löpare för att köra samlingen.

## <a name="change-the-key"></a>Ändra nyckeln

Medie tjänster kommer automatiskt att identifiera när nyckeln har ändrats.  Du testar detta genom att skapa en annan nyckel version för samma nyckel. Media Services ska kunna identifiera den här nyckeln på mindre än 15 minuter.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda de resurser som du har skapat och **du inte vill fortsätta att fakturera**, tar du bort dem.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du...
> [!div class="nextstepaction"]
> [Koda en fjärrfil baserat på URL och strömma videon med REST](stream-files-tutorial-with-rest.md)
