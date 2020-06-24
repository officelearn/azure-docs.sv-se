---
title: Skicka en åtkomsttoken via en anpassad princip till din app
titleSuffix: Azure AD B2C
description: Lär dig hur du kan skicka en åtkomsttoken för OAuth 2,0-identitets leverantörer som ett anspråk via en anpassad princip till ditt program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7307d047861a4fd6ba2065d231fde8040a383cfb
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201845"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomsttoken via en anpassad princip till ditt program i Azure Active Directory B2C

En [anpassad princip](custom-policy-get-started.md) i Azure Active Directory B2C (Azure AD B2C) ger användare av programmet en möjlighet att registrera sig eller logga in med en identitets leverantör. När detta inträffar tar Azure AD B2C [emot en åtkomsttoken](tokens-overview.md) från identitets leverantören. Azure AD B2C använder denna token för att hämta information om användaren. Du lägger till en anspråks typ och ett utgående anspråk till din anpassade princip för att skicka token till de program som du registrerar i Azure AD B2C.

Azure AD B2C stöder överföring av åtkomsttoken för [OAuth 2,0](authorization-code-flow.md) och [OpenID Connect](openid-connect.md) Identity providers. För alla andra identitets leverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Krav

* Din anpassade princip konfigureras med en OAuth 2,0-eller OpenID Connect-identitetsprovider.

## <a name="add-the-claim-elements"></a>Lägg till anspråks elementen

1. Öppna *TrustframeworkExtensions.xml* -filen och Lägg till följande **claimType** -element med en identifierare för `identityProviderAccessToken` **ClaimsSchema** -elementet:

    ```xml
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

    ```xml
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

3. Spara *TrustframeworkExtensions.xml* -filen.
4. Öppna den förlitande part princip filen, till exempel *SignUpOrSignIn.xml*, och Lägg till **OutputClaim** -elementet till **TechnicalProfile**:

    ```xml
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

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha Azure AD B2C token som returneras för att `https://jwt.ms` kunna granska anspråk i det.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att klicka på filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns**och Sök sedan efter och välj den *TrustframeworkExtensions.xml* filen.
7. Välj **Överför**.
8. Upprepa steg 5 till 7 för den förlitande part filen, till exempel *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin*.
2. För **program**väljer du ditt program som du har registrerat tidigare. Om du vill se token i exemplet nedan ska **svars-URL:** en visas `https://jwt.ms` .
3. Välj **Kör nu**.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Nästa steg

Läs mer om tokens i [Azure Active Directory B2C token-referens](tokens-overview.md).
