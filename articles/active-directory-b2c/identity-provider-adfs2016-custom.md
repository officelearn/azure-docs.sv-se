---
title: Lägg till ADFS som en SAML-identitetsprovider genom att använda anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera ADFS 2016 med hjälp av SAML-protokollet och anpassade principer i Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20cb5f70a5844cb2d56fc9ff357fcaf640a6c56b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388586"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Lägg till ADFS som en SAML Identity Provider med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för ett ADFS-användarkonto genom att använda [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Du aktiverar inloggning genom att lägga till en [teknisk profil för SAML Identity Provider](saml-identity-provider-technical-profile.md) i en anpassad princip.

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Se till att du har åtkomst till en Certificate. pfx-fil med en privat nyckel. Du kan skapa ett eget signerat certifikat och överföra det till Azure AD B2C. Azure AD B2C använder det här certifikatet för att signera SAML-begäran som skickats till din SAML Identity-Provider.
- För att Azure ska kunna godkänna. pfx-filens lösen ord måste lösen ordet vara krypterat med alternativet TripleDES-SHA1 i export verktyget för Windows Certificate Store i stället för AES256-SHA256.

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra ditt certifikat i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ**väljer du `Upload` .
7. Ange ett **namn** för princip nyckeln. Exempelvis `SamlCert`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. Bläddra till och välj din Certificate. pfx-fil med den privata nyckeln.
9. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett ADFS-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett ADFS-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil. Mer information finns i [definiera en teknisk profil för SAML Identity Provider](saml-identity-provider-technical-profile.md).

1. Öppna *TrustFrameworkExtensions.xml*.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ersätt `your-ADFS-domain` med namnet på din ADFS-domän och Ersätt värdet för **identityProvider** utgående anspråk med DNS (valfritt värde som anger din domän).

1. Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment. Om principen redan innehåller den `SM-Saml-idp` tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av enkel inloggnings session](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur man kommunicerar med ADFS-kontot. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
3. Klicka på **Överför**.

> [!NOTE]
> Visual Studio Code B2C-tillägget använder "socialIdpUserId". Det krävs också en social policy för ADFS.
>

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av registrerings-eller inloggnings skärmarna. För att göra det tillgängligt, skapar du en dubblett av en befintlig användar resa för användare och ändrar den så att den även har ADFS-identitetsprovider.

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
3. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Exempelvis `SignUpSignInADFS`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-eller inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för ett ADFS-konto visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade.
2. Lägg till följande-element under **ClaimsProviderSelections**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är att Azure AD B2C kommunicera med ett ADFS-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID: t för den tekniska profil som du skapade tidigare. Exempelvis `Contoso-SAML2`.

3. Spara *TrustFrameworkExtensions.xml* -filen och ladda upp den igen för verifiering.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurera ett förtroende för ADFS-förlitande part

Om du vill använda ADFS som identitets leverantör i Azure AD B2C måste du skapa ett AD FS-förlitande parts förtroende med Azure AD B2C SAML-metadata. I följande exempel visas en URL-adress till SAML-metadata för en Azure AD B2C teknisk profil:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **din klient** organisation med ditt klient namn, till exempel Your-Tenant.onmicrosoft.com.
- **din princip** med ditt princip namn. Till exempel B2C_1A_signup_signin_adfs.
- **din tekniska profil** med namnet på din SAML Identity Provider-tekniska profil. Till exempel contoso-SAML2.

Öppna en webbläsare och gå till URL: en. Se till att du anger rätt URL och att du har åtkomst till XML-metadatafilen. Om du vill lägga till ett nytt förtroende för förlitande part med hjälp av snapin-modulen för AD FS-hantering och konfigurera inställningarna manuellt, utför följande procedur på en Federations Server. Det krävs minst medlemskap i **Administratörer** eller motsvarande på den lokala datorn för att kunna slutföra den här proceduren.

1. I Serverhanteraren väljer du **verktyg**och sedan ADFS- **hantering**.
2. Välj **Lägg till förtroende för förlitande part**.
3. På sidan **Välkommen** väljer du **anspråks medveten**och klickar sedan på **Starta**.
4. På sidan **Välj data källa** väljer du **Importera data om den förlitande parten publicera online eller i ett lokalt nätverk**, anger din Azure AD B2C metadata-URL och klickar sedan på **Nästa**.
5. På sidan **Ange visnings namn** anger du ett **visnings namn**under **anteckningar**, anger en beskrivning för det här förlitande part förtroendet och klickar sedan på **Nästa**.
6. På sidan **välj Access Control princip** väljer du en princip och klickar sedan på **Nästa**.
7. På sidan **redo att lägga till förtroende** granskar du inställningarna och klickar sedan på **Nästa** för att spara information om förtroende för förlitande part.
8. På sidan **Slutför** klickar du på **Stäng**. den här åtgärden visar automatiskt dialog rutan **Redigera anspråks regler** .
9. Välj **Lägg till regel**.
10. I **anspråks regel mal len**väljer du **Skicka LDAP-attribut som anspråk**.
11. Ange ett **namn på anspråks regeln**. För **attributarkivet**väljer du **Välj Active Directory**, lägger till följande anspråk och klickar sedan på **Slutför** och **OK**.

    | LDAP-attribut | Utgående anspråks typ |
    | -------------- | ------------------- |
    | Användarens huvud namn | userPrincipalName |
    | Efternamn | family_name |
    | Given-Name | given_name |
    | E-post adress | e-post |
    | Display-Name | name |

    Observera att dessa namn inte visas i list rutan Utgående anspråks typer. Du måste skriva in dem manuellt i. (List rutan kan redige ras i själva verket).

12.  Utifrån din certifikat typ kan du behöva ange HASH-algoritmen. I fönstret Egenskaper för förlitande part förtroende (B2C demo) väljer du fliken **Avancerat** och ändrar sedan den **säkra hash-algoritmen** till `SHA-256` och klickar på **OK**.
13. I Serverhanteraren väljer du **verktyg**och sedan ADFS- **hantering**.
14. Välj det förtroende för förlitande part som du har skapat, Välj **Uppdatera från federationsmetadata**och klicka sedan på **Uppdatera**.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i B2C-klienten. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn.xml* i din arbets katalog och Byt namn på den. Du kan till exempel byta namn på den till *SignUpSignInADFS.xml*.
2. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Exempelvis `SignUpSignInADFS`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Till exempel`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID för den nya användar resan som du skapade (SignUpSignInADFS).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrol lera att Azure AD B2C programmet som du har skapat är markerat i fältet **Välj program** och testa det genom att klicka på **Kör nu**.

