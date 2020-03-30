---
title: Skicka en åtkomsttoken via en anpassad princip till din app
titleSuffix: Azure AD B2C
description: Lär dig hur du kan skicka en åtkomsttoken för OAuth 2.0-identitetsleverantörer som ett anspråk via en anpassad princip till ditt program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187803"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomsttoken via en anpassad princip till ditt program i Azure Active Directory B2C

En [anpassad princip](custom-policy-get-started.md) i Azure Active Directory B2C (Azure AD B2C) ger användare av ditt program en möjlighet att registrera dig eller logga in med en identitetsprovider. När detta inträffar får Azure AD B2C en [åtkomsttoken](tokens-overview.md) från identitetsprovidern. Azure AD B2C använder den token för att hämta information om användaren. Du lägger till en anspråkstyp och utdataanspråk i din anpassade princip för att skicka token till de program som du registrerar i Azure AD B2C.

Azure AD B2C stöder att skicka åtkomsttoken [för OAuth 2.0-](authorization-code-flow.md) och [OpenID](openid-connect.md) Connect-identitetsleverantörer. För alla andra identitetsleverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Krav

* Din anpassade princip är konfigurerad med en OAuth 2.0- eller OpenID Connect-identitetsprovider.

## <a name="add-the-claim-elements"></a>Lägg till anspråkselementen

1. Öppna filen *TrustframeworkExtensions.xml* och lägg till följande **ClaimType-element** med en identifierare `identityProviderAccessToken` för i **ClaimsSchema-elementet:**

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

2. Lägg till **Elementet OutputClaim** i **TechnicalProfile-elementet** för varje OAuth 2.0-identitetsprovider som du vill ha åtkomsttoken för. I följande exempel visas det element som lagts till i Facebooks tekniska profil:

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

3. Spara filen *TrustframeworkExtensions.xml.*
4. Öppna den förlitande part-principfilen, till exempel *SignUpOrSignIn.xml*, och lägg till **elementet OutputClaim** i **TechnicalProfile:**

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

## <a name="test-your-policy"></a>Testa din policy

När du testar dina program i Azure AD B2C kan det vara bra `https://jwt.ms` att ha Azure AD B2C-token returnerad för att kunna granska anspråken i den.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att klicka på **katalog + prenumerationsfiltret** i den övre menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Identity Experience Framework**.
5. Klicka på **Ladda upp princip**på sidan Anpassade principer.
6. Välj **Skriv över principen om den finns**och sök sedan efter och välj filen *TrustframeworkExtensions.xml.*
7. Välj **Överför**.
8. Upprepa steg 5 till och med 7 för den förlitande partfilen, till exempel *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna principen som du har ändrat. Till exempel *B2C_1A_signup_signin*.
2. För **Ansökan**väljer du det program som du tidigare har registrerat. Om du vill se token i exemplet `https://jwt.ms`nedan ska **svars-URL:en** visa .
3. Välj **Kör nu**.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Nästa steg

Läs mer om token i [Azure Active Directory B2C token referens](tokens-overview.md).
