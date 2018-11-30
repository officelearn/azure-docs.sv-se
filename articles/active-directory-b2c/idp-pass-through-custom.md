---
title: Skicka en åtkomst-token via en anpassad princip till ditt program i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du överföra en åtkomsttoken för OAuth2.0 Identitetsproviders som ett anspråk genom en anpassad princip till ditt program i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e32e62a050b54334a37b09f001798b9a62e2e6b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642377"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomst-token via en anpassad princip till ditt program i Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är för närvarande i offentlig förhandsversion.

En [anpassad princip](active-directory-b2c-get-started-custom.md) i Azure Active Directory (Azure AD) B2C ger ditt programs användare möjlighet att registrera dig eller logga in med en identitetsprovider. Då kan Azure AD B2C får en [åtkomsttoken](active-directory-b2c-reference-tokens.md) från identitetsprovidern. Azure AD B2C använder den token för att hämta information om användaren. Du lägger till en typ av anspråk och utgående anspråksuppsättningar till en egen princip och skicka token via till de program som du registrerar i Azure AD B2C. 

Azure AD-B2C stöder för närvarande endast skicka åtkomsttoken [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) identitetsleverantörer, bland annat Facebook och [Google](active-directory-b2c-custom-setup-goog-idp.md). För alla andra identitetsleverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Förutsättningar

- Den anpassade principen har konfigurerats med en OAuth 2.0-identitetsprovider.

## <a name="add-the-claim-elements"></a>Lägg till anspråk-element 

1. Öppna din *TrustframeworkExtensions.xml* filen och Lägg till följande **ClaimType** element med en identifierare för `identityProviderAccessToken` till den **ClaimsSchema** element:

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

2. Lägg till den **OutputClaim** elementet så att den **TechnicalProfile** element för varje OAuth 2.0-identitetsprovider som du vill använda åtkomsttoken för. I följande exempel visas de element som lagts till i den tekniska profilen Facebook:

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

3. Spara den *TrustframeworkExtensions.xml* fil.
4. Öppna din förlitande part principfil som *SignUpOrSignIn.xml*, och Lägg till den **OutputClaim** elementet så att den **TechnicalProfile**:

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

5. Spara principfilen.

## <a name="test-your-policy"></a>Testa din princip

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha en Azure AD B2C-token som returneras till `https://jwt.ms` för att kunna granska anspråk i den.

### <a name="upload-the-files"></a>Ladda upp filer

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **Identitetsramverk**.
5. På sidan anpassade principer **ladda upp principen**.
6. Välj **Skriv över principen om den finns**, och sök sedan efter och välj den *TrustframeworkExtensions.xml* fil.
7. Klicka på **Överför**.
8. Upprepa steg 5 till 7 för filen förlitande part, till exempel *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin*.
2. För **programmet**, Välj ditt program som du tidigare har registrerat. Se token i exemplet nedan, den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **Kör nu**.

    Du bör se något som liknar följande exempel:

    ![Avkodade token](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Nästa steg

Mer information om token i den [tokenreferens för Azure Active Directory](active-directory-b2c-reference-tokens.md).





