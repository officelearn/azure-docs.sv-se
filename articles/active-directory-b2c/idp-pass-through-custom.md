---
title: Skicka en åtkomsttoken via en anpassad princip till ditt program i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du kan skicka en åtkomsttoken för OAuth 2.0-identitets leverantörer som ett anspråk via en anpassad princip till ditt program i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2253e9f6331662f0ead0251f9affd7996e02aa31
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846882"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomsttoken via en anpassad princip till ditt program i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

En [anpassad princip](active-directory-b2c-get-started-custom.md) i Azure Active Directory (Azure AD) B2C ger användare av programmet en möjlighet att registrera sig eller logga in med en identitets leverantör. När detta inträffar tar Azure AD B2C [emot en åtkomsttoken](active-directory-b2c-reference-tokens.md) från identitets leverantören. Azure AD B2C använder denna token för att hämta information om användaren. Du lägger till en anspråks typ och ett utgående anspråk till din anpassade princip för att skicka token till de program som du registrerar i Azure AD B2C.

Azure AD B2C stöder överföring av åtkomsttoken för [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) och [OpenID Connect](active-directory-b2c-reference-oidc.md) Identity providers. För alla andra identitets leverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Förutsättningar

- Din anpassade princip konfigureras med en OAuth 2,0-eller OpenID Connect-identitetsprovider.

## <a name="add-the-claim-elements"></a>Lägg till anspråks elementen

1. Öppna filen *TrustframeworkExtensions. XML* och Lägg till följande **claimType** -element med en identifierare för `identityProviderAccessToken` elementet **ClaimsSchema** :

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Lägg till elementet **OutputClaim** i **TechnicalProfile** -elementet för varje OAuth 2,0-identitetsprovider som du vill ha åtkomsttoken för. I följande exempel visas det element som har lagts till i den nya Facebook-tekniska profilen:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Spara filen *TrustframeworkExtensions. XML* .
4. Öppna den förlitande part princip filen, till exempel *SignUpOrSignIn. XML*, och Lägg till **OutputClaim** -elementet i **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Spara princip filen.

## <a name="test-your-policy"></a>Testa principen

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha Azure AD B2C token som returneras `https://jwt.ms` för att kunna granska anspråk i det.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns**och Sök sedan efter och välj *TrustframeworkExtensions. XML-* filen.
7. Klicka på **Överför**.
8. Upprepa steg 5 till 7 för den förlitande part filen, till exempel *SignUpOrSignIn. XML*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin*.
2. För **program**väljer du ditt program som du har registrerat tidigare. Om du vill se token i exemplet nedan ska **svars-URL:** en visas `https://jwt.ms`.
3. Klicka på **Kör nu**.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token-block markerat](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Nästa steg

Läs mer om tokens i [Azure Active Directory token-referens](active-directory-b2c-reference-tokens.md).
