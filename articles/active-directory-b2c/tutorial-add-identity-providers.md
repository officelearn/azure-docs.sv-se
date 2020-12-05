---
title: 'Självstudie: Lägg till identitets leverantörer i dina appar'
titleSuffix: Azure AD B2C
description: Följ den här självstudien för att lära dig hur du lägger till identitets leverantörer i dina program i Azure Active Directory B2C att använda Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 166bdb7a2cf15a84e1b826a9a798042c568bb227
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608239"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Självstudie: Lägg till identitets leverantörer till dina program i Azure Active Directory B2C

Du kanske vill att användarna ska kunna logga in på dina program med olika identitetsprovidrar. En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. Du kan lägga till identitets leverantörer som stöds av Azure Active Directory B2C (Azure AD B2C) till dina [användar flöden](user-flow-overview.md) med hjälp av Azure Portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa program för identitets leverantörer
> * Lägg till identitets leverantörer till din klient – både i Facebook och i Azure Active Directory
> * Lägg till identitets leverantörer i ditt användar flöde

Du använder vanligt vis bara en identitets leverantör i dina program, men du har möjlighet att lägga till fler. Den här självstudien visar hur du lägger till en Azure AD-identitetsprovider och en Facebook-identitetsprovider i ditt program. Att lägga till båda dessa identitets leverantörer i ditt program är valfritt. Du kan också lägga till andra identitets leverantörer, till exempel [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)eller [Twitter](identity-provider-twitter.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användare kan registrera sig och logga in i ditt program.

## <a name="create-applications"></a>Skapa program

Identitets leverantörs program ger identifieraren och nyckeln för att aktivera kommunikation med Azure AD B2C-klienten. I det här avsnittet av självstudien skapar du ett Azure AD-program och ett Facebook-program som du får identifierare och nycklar från för att lägga till identitets leverantörer till din klient organisation. Om du bara lägger till en identitets leverantör behöver du bara skapa programmet för providern.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory program

Om du vill aktivera inloggning för användare från Azure AD måste du registrera ett program i Azure AD-klienten. Azure AD-klienten är inte samma som din Azure AD B2C klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett namn för ditt program. Exempelvis `Azure AD B2C App`.
1. Godkänn bara valet av **konton i den här organisations katalogen** för det här programmet.
1. För **omdirigerings-URI: n**, godkänn värdet för **webben** och ange följande URL i gemener och Ersätt `your-B2C-tenant-name` med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Exempelvis `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Alla URL: er ska nu använda [b2clogin.com](b2clogin.md).

1. Välj **Registrera** och registrera sedan det **program-ID (klient)** som du använder i ett senare steg.
1. Under **Hantera** på program-menyn väljer du **certifikat & hemligheter** och väljer sedan **ny klient hemlighet**.
1. Ange en **Beskrivning** av klient hemligheten. Exempelvis `Azure AD B2C App Secret`.
1. Välj förfallo period. För det här programmet godkänner du valet på **1 år**.
1. Välj **Lägg till** och registrera sedan värdet för den nya klient hemlighet som du använder i ett senare steg.

### <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill använda ett Facebook-konto som identitets leverantör i Azure AD B2C måste du skapa ett program på Facebook. Om du inte redan har ett Facebook-konto kan du hämta det på [https://www.facebook.com/](https://www.facebook.com/) .

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontoautentiseringsuppgifter.
1. Om du inte redan har gjort det måste du registrera dig som en Facebook-utvecklare. Om du vill göra det väljer du **Kom igång** i det övre högra hörnet på sidan, accepterar Facebook-principer och slutför registrerings stegen.
1. Välj **Mina appar** och sedan **skapa app**.
1. Ange ett **visnings namn** och en giltig **kontakt-e-postadress**.
1. Klicka på **skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattforms principer och slutför en säkerhets kontroll online.
1. Välj **Inställningar**  >  **Basic**.
1. Välj en **kategori**, till exempel `Business and Pages` . Det här värdet krävs av Facebook, men används inte av Azure AD B2C.
1. Längst ned på sidan väljer du **Lägg till plattform** och väljer sedan **webbplats**.
1. I **webbplats-URL** skriver du `https://your-tenant-name.b2clogin.com/` ersätta `your-tenant-name` med namnet på din klient.
1. Ange en URL för **Sekretess policyns URL**, till exempel `http://www.contoso.com/` . URL: en till sekretess policyn är en sida som du upprätthåller för att tillhandahålla sekretess information för ditt program.
1. Välj **Spara ändringar**.
1. Överst på sidan registrerar du värdet för **app-ID**.
1. Bredvid **appens hemlighet** väljer du **Visa** och registrera dess värde. Du kan använda både appens ID och appens hemlighet för att konfigurera Facebook som en identitets leverantör i din klient organisation. **App Secret** är en viktig säkerhets autentiseringsuppgift som du bör lagra säkert.
1. Välj plus tecknet bredvid **produkter** och välj sedan **Konfigurera** på **Facebook-inloggning**.
1. Under **Facebook-inloggning** i den vänstra menyn väljer du **Inställningar**.
1. I **giltiga OAuth-omdirigerings-URI: er** anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient. Välj **Spara ändringar** längst ned på sidan.
1. Om du vill göra ditt Facebook-program tillgängligt för Azure AD B2C klickar du på **status** väljaren längst upp till höger på sidan och **aktiverar det för att göra** programmet offentligt, och klickar sedan på **Bekräfta**. I det här läget bör statusen ändras från **utveckling** till **Live**.

## <a name="add-the-identity-providers"></a>Lägg till identitets leverantörer

När du har skapat programmet för den identitetsprovider som du vill lägga till lägger du till identitets leverantören till din klient organisation.

### <a name="add-the-azure-active-directory-identity-provider"></a>Lägg till Azure Active Directory Identity Provider

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **ny OpenID Connect-Provider**.
1. Ange ett **namn**. Ange till exempel *contoso Azure AD*.
1. För **metadata-URL** anger du följande URL `{tenant}` som ersätter med domän namnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Exempelvis `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Exempelvis `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. För **klient-ID** anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet** anger du den klient hemlighet som du tidigare har registrerat.
1. För **scopet** anger du `openid profile` .
1. Lämna standardvärdena för **svars typ** och **svars läge**.
1. Valfritt I **domän tipset** anger du `contoso.com` . Mer information finns i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Under **identitets leverantör anspråks mappning** väljer du följande anspråk:

    * **Användar-ID**: *OID*
    * **Visnings namn**: *namn*
    * **Tilldelat namn**: *given_name*
    * Efter **namn**: *family_name*
    * **E-post**: *unique_name*

1. Välj **Spara**.

### <a name="add-the-facebook-identity-provider"></a>Lägg till Facebook Identity Provider

1. Välj **identitets leverantörer** och välj sedan **Facebook**.
1. Ange ett **namn**. Till exempel *Facebook*.
1. För **klient-ID** anger du app-ID: t för det Facebook-program som du skapade tidigare.
1. För **klient hemligheten** anger du appens hemlighet som du har spelat in.
1. Välj **Spara**.

## <a name="update-the-user-flow"></a>Uppdatera användar flödet

I självstudien som du avslutade som en del av kraven skapade du ett användar flöde för registrering och inloggning med namnet *B2C_1_signupsignin1*. I det här avsnittet lägger du till identitets leverantörer i *B2C_1_signupsignin1* användar flödet.

1. Välj **användar flöden** och välj sedan *B2C_1_signupsignin1* användar flödet.
2. Välj **identitets leverantörer**, Välj **Facebook** och **contoso Azure AD** Identity-providers som du har lagt till.
3. Välj **Spara**.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. På sidan Översikt i det användar flöde som du har skapat väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör användar flöde** och logga sedan in med en identitets leverantör som du har lagt till tidigare.
1. Upprepa steg 1 till 3 för de andra identitets leverantörer som du har lagt till.

Om inloggnings åtgärden lyckas omdirigeras du till `https://jwt.ms` som visar den avkodade token, ungefär som:

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
> * Skapa program för identitets leverantörer
> * Lägg till identitets leverantörer till din klient organisation
> * Lägg till identitets leverantörer i ditt användar flöde

Nu ska du lära dig hur du anpassar användar gränssnittet för de sidor som visas för användarna som en del av deras identitets upplevelse i dina program:

> [!div class="nextstepaction"]
> [Anpassa användar gränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md)
