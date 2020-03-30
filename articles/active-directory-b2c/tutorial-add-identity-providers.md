---
title: 'Självstudiekurs: Lägga till identitetsleverantörer i dina appar'
titleSuffix: Azure AD B2C
description: Lär dig hur du lägger till identitetsleverantörer i dina program i Azure Active Directory B2C med Azure-portalen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183524"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Självstudiekurs: Lägga till identitetsleverantörer i dina program i Azure Active Directory B2C

Du kanske vill att användarna ska kunna logga in på dina program med olika identitetsprovidrar. En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. Du kan lägga till identitetsleverantörer som stöds av Azure Active Directory B2C (Azure AD B2C) i dina [användarflöden](user-flow-overview.md) med Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa identitetsproviderprogram
> * Lägg till identitetsleverantörerna i din klientorganisation
> * Lägg till identitetsleverantörerna i ditt användarflöde

Du använder vanligtvis bara en identitetsprovider i dina program, men du har möjlighet att lägga till fler. Den här självstudien visar hur du lägger till en Azure AD-identitetsleverantör och en Facebook-identitetsleverantör i ditt program. Det är valfritt att lägga till båda dessa identitetsleverantörer i ditt program. Du kan också lägga till andra identitetsleverantörer, till exempel [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)eller [Twitter](identity-provider-twitter.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in på ditt program.

## <a name="create-applications"></a>Skapa program

Identitetsproviderprogram tillhandahåller identifieraren och nyckeln för att möjliggöra kommunikation med din Azure AD B2C-klient. I det här avsnittet av självstudien skapar du ett Azure AD-program och ett Facebook-program som du får identifierare och nycklar från för att lägga till identitetsleverantörer till din klientorganisation. Om du bara lägger till en av identitetsleverantörerna behöver du bara skapa programmet för den leverantören.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Om du vill aktivera inloggning för användare från Azure AD måste du registrera ett program i Azure AD-klienten. Azure AD-klienten är inte samma sak som din Azure AD B2C-klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Ny registrering**.
1. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
1. Acceptera valet av **konton i den här organisationskatalogen endast** för det här programmet.
1. För **Redirect URI**godkänner du värdet för **webben** och anger följande `your-B2C-tenant-name` URL i alla gemener och ersätter med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Alla webbadresser ska nu använda [b2clogin.com](b2clogin.md).

1. Välj **Registrera**och registrera sedan **det program-ID som** du använder i ett senare steg.
1. Under **Hantera** på programmenyn väljer du **Certifikat & hemligheter**och väljer sedan Ny **klienthemlighet**.
1. Ange en **beskrivning** för klienthemligheten. Till exempel `Azure AD B2C App Secret`.
1. Välj utgångsperiod. För denna ansökan, acceptera valet av **I 1 år**.
1. Välj **Lägg till**och registrera sedan värdet för den nya klienthemligheten som du använder i ett senare steg.

### <a name="create-a-facebook-application"></a>Skapa en Facebook-applikation

Om du vill använda ett Facebook-konto som identitetsleverantör i Azure AD B2C måste du skapa ett program på Facebook. Om du inte redan har ett Facebook-konto [https://www.facebook.com/](https://www.facebook.com/)kan du få det på .

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontouppgifter.
1. Om du inte redan har gjort det måste du registrera dig som Facebook-utvecklare. Det gör du genom att välja **Kom igång i** det övre högra hörnet på sidan, acceptera Facebooks policyer och slutföra registreringsstegen.
1. Välj **Mina appar** och skapa sedan **appen**.
1. Ange ett **visningsnamn** och ett giltigt **kontaktmeddelande**.
1. Klicka på **Skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattformspolicyer och slutför en säkerhetskontroll online.
1. Välj**Grundläggande** **inställningar** > .
1. Välj en **kategori** `Business and Pages`, till exempel . Det här värdet krävs av Facebook, men används inte av Azure AD B2C.
1. Längst ned på sidan väljer du **Lägg till plattform**och väljer sedan **Webbplats**.
1. I Url för `https://your-tenant-name.b2clogin.com/` `your-tenant-name` **webbplats**anger du att ersätta med namnet på din klient.
1. Ange en url för **webbadressen** `http://www.contoso.com/`till sekretesspolicyn , till exempel . Webbadressen till sekretesspolicyn är en sida som du upprätthåller för att tillhandahålla sekretessinformation för ditt program.
1. Välj **Spara ändringar**.
1. Högst upp på sidan registrerar du värdet för **App-ID**.
1. Bredvid **App Secret**väljer du **Visa** och registrerar dess värde. Du använder både app-ID och App Secret för att konfigurera Facebook som en identitetsleverantör i din klientorganisation. **App Secret** är en viktig säkerhetsautentisering som du bör lagra säkert.
1. Välj plustecknet bredvid **PRODUKTER**och välj sedan **Konfigurera**under **Facebook-inloggning**.
1. Under **Facebook-inloggning** i menyn till vänster väljer du **Inställningar**.
1. Ange i **giltiga OAuth-omdirigerings-URI:er** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Ersätt `your-tenant-name` med namnet på din klient. Välj **Spara ändringar** längst ned på sidan.
1. Om du vill göra ditt Facebook-program tillgängligt för Azure AD B2C klickar du på **statusväljaren** längst upp **till** höger på sidan och aktiverar det för att göra programmet offentligt och klickar sedan på **Bekräfta**. Nu bör status ändras från **utveckling** till **Live**.

## <a name="add-the-identity-providers"></a>Lägg till identitetsleverantörerna

När du har skapat programmet för den identitetsleverantör som du vill lägga till lägger du till identitetsleverantören i din klientorganisation.

### <a name="add-the-azure-active-directory-identity-provider"></a>Lägga till Azure Active Directory-identitetsprovidern

1. Kontrollera att du använder katalogen som innehåller Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Ny OpenID Connect-provider**.
1. Ange ett **namn**. Ange till exempel *Contoso Azure AD*.
1. För **metadata-url**anger du `your-AD-tenant-domain` följande URL som ersätter med domännamnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. För **klient-ID**anger du det program-ID som du tidigare har registrerat.
1. För **Klienthemlighet**anger du klienthemligheten som du tidigare spelat in.
1. Lämna standardvärdena för **scope,** **svarstyp**och **svarsläge**.
1. (Valfritt) Ange ett värde för **Domain_hint**. Till exempel *ContosoAD*. [Domäntips](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att accelerera användaren till deras federerade IdP-inloggningssida. Eller så kan de användas av ett program med flera innehavare för att accelerera användaren direkt till den märkesvaror Azure AD inloggningssidan för sin klientorganisation.
1. Under **Identitetsproviderns anspråksmappning**anger du följande värden för anspråksmappning:

    * **Användar-ID:** *oid*
    * **Visningsnamn**: *namn*
    * **Förnamn**: *given_name*
    * **Efternamn**: *family_name*
    * **E-post:** *unique_name*

1. Välj **Spara**.

### <a name="add-the-facebook-identity-provider"></a>Lägg till Facebook-identitetsleverantören

1. Välj **Identitetsleverantörer**och välj sedan **Facebook**.
1. Ange ett **namn**. Till exempel *Facebook*.
1. För **klient-ID**anger du app-ID:t för Facebook-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den Apphemlighet som du spelade in.
1. Välj **Spara**.

## <a name="update-the-user-flow"></a>Uppdatera användarflödet

I självstudien som du slutförde som en del av förutsättningarna skapade du ett användarflöde för registrering och inloggning med namnet *B2C_1_signupsignin1*. I det här avsnittet lägger du till identitetsleverantörerna i *B2C_1_signupsignin1* användarflödet.

1. Välj **Användarflöden (principer)** och välj sedan *B2C_1_signupsignin1* användarflödet.
2. Välj **Identitetsleverantörer**, välj de **Facebook-** och **Contoso Azure AD-identitetsleverantörer** som du har lagt till.
3. Välj **Spara**.

## <a name="test-the-user-flow"></a>Testa användarflödet

1. På sidan Översikt för det användarflöde som du skapade väljer du **Kör användarflöde**.
1. För **Ansökan**väljer du webbprogrammet *webapp1* som du tidigare har registrerat. **Svars-URL:en** ska visa `https://jwt.ms`.
1. Välj **Kör användarflöde**och logga sedan in med en identitetsprovider som du tidigare har lagt till.
1. Upprepa steg 1 till och med 3 för andra identitetsleverantörer som du har lagt till.

Om inloggningsåtgärden lyckas omdirigeras du `https://jwt.ms` till vilken visar den avkodade token, ungefär som:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa identitetsproviderprogram
> * Lägg till identitetsleverantörerna i din klientorganisation
> * Lägg till identitetsleverantörerna i ditt användarflöde

Läs sedan om hur du anpassar användargränssnittet för de sidor som visas för användarna som en del av deras identitetsupplevelse i dina program:

> [!div class="nextstepaction"]
> [Anpassa användargränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md)
