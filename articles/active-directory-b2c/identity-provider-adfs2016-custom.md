---
title: Lägga till ADFS som EN SAML-identitetsprovider med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera ADFS 2016 med SAML-protokollet och anpassade principer i Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bfe39d9528927f995d14772e07e02b2a0528e5e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188536"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Lägga till ADFS som SAML-identitetsprovider med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för ett ADFS-användarkonto med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Du aktiverar inloggning genom att lägga till en [SAML-teknisk profil](saml-technical-profile.md) i en anpassad princip.

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Kontrollera att du har åtkomst till en PFX-fil med en privat nyckel. Du kan generera ditt eget signerade certifikat och överföra det till Azure AD B2C. Azure AD B2C använder det här certifikatet för att signera SAML-begäran som skickas till din SAML-identitetsprovider.
- För att Azure ska kunna acceptera pfx-fillösenordet måste lösenordet krypteras med alternativet TripleDES-SHA1 i Windows Certificate Store Export- verktyget i motsats till AES256-SHA256.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du måste lagra ditt certifikat i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **Identity Experience Framework**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **Alternativ** `Upload`väljer du .
7. Ange ett **namn** för principnyckeln. Till exempel `SamlCert`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
8. Bläddra till och välj din PFX-fil för certifikatet med den privata nyckeln.
9. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med ett ADFS-konto måste du definiera kontot som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera ett ADFS-konto som en anspråksleverantör genom att lägga till det i elementet **ClaimsProviders** i principets tilläggsfil. Mer information finns i [definiera en SAML-teknisk profil](saml-technical-profile.md).

1. Öppna *TrustFrameworkExtensions.xml*.
1. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
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

1. Ersätt `your-ADFS-domain` med namnet på ADFS-domänen och ersätt värdet för identityProvider-utdataanspråket med dns-programmet (godtyckligt värde som anger din domän). **identityProvider**

1. Leta `<ClaimsProviders>` reda på avsnittet och lägg till följande XML-kodavsnitt. Om din princip redan `SM-Saml-idp` innehåller den tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av en enda inloggningssession](custom-policy-reference-sso.md).

    ```XML
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

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Vid det här laget har du konfigurerat din princip så att Azure AD B2C vet hur man kommunicerar med ADFS-konto. Prova att ladda upp förlängningsfilen för din policy bara för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
3. Klicka på **Överför**.

> [!NOTE]
> Visual Studio-koden B2C-tillägget använder "socialIdpUserId". En socialpolitik krävs också för ADFS.
>

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är inte tillgänglig på någon av inloggnings- eller inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har ADFS-identitetsprovidern.

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
2. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
3. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
5. Byt namn på ID för användarens färd. Till exempel `SignUpSignInADFS`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings- eller inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för ett ADFS-konto visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du skapade.
2. Lägg till följande element under **ClaimsProviderSelections.** Ange värdet för **TargetClaimsExchangeId** till ett `ContosoExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med ett ADFS-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
2. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för ID:et som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID för den tekniska profil som du skapade tidigare. Till exempel `Contoso-SAML2`.

3. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurera ett ADFS-förtroende för en förlitande part för ADFS

Om du vill använda ADFS som identitetsprovider i Azure AD B2C måste du skapa ett ADFS Relying Party Trust med Azure AD B2C SAML-metadata. I följande exempel visas en URL-adress till SAML-metadata för en teknisk profil i Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **din-klient** med ditt klientnamn, till exempel your-tenant.onmicrosoft.com.
- **din policy** med ditt policynamn. Till exempel B2C_1A_signup_signin_adfs.
- **din-tekniska profil** med namnet på din SAML-identitetsleverantörs tekniska profil. Till exempel Contoso-SAML2.

Öppna en webbläsare och navigera till webbadressen. Kontrollera att du skriver rätt URL och att du har åtkomst till XML-metadatafilen. Om du vill lägga till ett nytt förtroende för en förlitande part med snapin-modulen ADFS Management och konfigurera inställningarna manuellt utför du följande procedur på en federationsserver. Medlemskap i **administratörer** eller motsvarande på den lokala datorn är det minsta som krävs för att slutföra den här proceduren.

1. Välj **Verktyg**i Serverhanteraren och välj sedan **ADFS Management**.
2. Välj **Lägg till förtroende för förlitande part**.
3. På **välkomstsidan** väljer du **Skademedveten**och klickar sedan på **Start**.
4. På sidan **Välj datakälla** väljer du **Importera data om den förlitande parten publicera online eller i ett lokalt nätverk,** ange din Azure AD B2C-metadata-URL och klicka sedan på **Nästa**.
5. På sidan **Ange visningsnamn** anger du ett **visningsnamn**under **Anteckningar,** anger en beskrivning för det förlitande partförtroendet och klickar sedan på **Nästa**.
6. På sidan **Välj åtkomstkontrollprincip** markerar du en princip och klickar sedan på **Nästa**.
7. På sidan **Klar att lägga till förtroende** granskar du inställningarna och klickar sedan på **Nästa** för att spara förtroendeinformation för den förlitande parten.
8. Klicka på **Stäng**på sidan **Slutför,** och den här åtgärden visar automatiskt dialogrutan **Redigera anspråksregler.**
9. Välj **Lägg till regel**.
10. I **mallen Anspråksregel**väljer du **Skicka LDAP-attribut som anspråk**.
11. Ange ett **anspråksregelnamn**. För **attributarkivet**väljer du **Välj Active Directory**, lägger till följande anspråk och klickar sedan på **Slutför** och **OK**.

    | LDAP-attribut | Typ av utgående anspråk |
    | -------------- | ------------------- |
    | Namn för användare-huvudnamn | userPrincipalName |
    | Efternamn | family_name |
    | Förnamn | given_name |
    | E-postadress | e-post |
    | Visningsnamn | namn |

    Observera att dessa namn inte visas i listrutan för utgående anspråkstyp. Du måste skriva in dem manuellt. (Listrutan är faktiskt redigerbar).

12.  Baserat på din certifikattyp kan du behöva ange HASH-algoritmen. Om egenskapsfönstret för förlitande part förtroende (B2C Demo) väljer du `SHA-256`fliken **Avancerat** och ändrar **algoritmen för säker hash** till och klickar på **Ok**.
13. Välj **Verktyg**i Serverhanteraren och välj sedan **ADFS Management**.
14. Välj det förlitande partförtroende som du skapade, välj **Uppdatera från federationsmetadata**och klicka sedan på **Uppdatera**.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInADFS.xml*.
2. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInADFS`.
3. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den nya användarresan som du skapade (SignUpSignInADFS).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrollera att Azure AD B2C-program som du har skapat har valts i fältet **Välj program** och testa det sedan genom att klicka på **Kör nu**.

