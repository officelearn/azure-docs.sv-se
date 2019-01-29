---
title: Lägg till AD FS som en SAML-identitetsprovider med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Konfigurera AD FS 2016 med SAML-protokoll och anpassade principer i Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7435a1879963b90bb05b6f1a617e9d1bc3de27a5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203716"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Lägg till AD FS som en SAML-identitetsprovider med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för ett användarkonto i AD FS med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C. Du aktiverar inloggning genom att lägga till en [tekniska SAML-profilen](saml-technical-profile.md) till en anpassad princip.

## <a name="prerequisites"></a>Förutsättningar

- Utför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Kontrollera att du har åtkomst till en .pfx-certifikatfil med en privat nyckel. Du kan skapa egna certifikat och ladda upp den till Azure AD B2C. Azure AD B2C använder det här certifikatet för att signera SAML-begäran skickas till SAML-identitetsprovider.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du behöver lagra certifikatet i din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer **Identitetsupplevelse – FÖRHANDSVERSION**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **alternativ**, Välj `Upload`.
7. Ange en **namn** för principnyckeln. Till exempel `SamlCert`. Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
8. Bläddra till och välj din .pfx-certifikatfil med privat nyckel.
9. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråksprovider

Om du vill att användarna att logga in med ett konto för AD FS måste du definiera kontot som en anspråksprovider som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera ett AD FS-konto som en anspråksprovider genom att lägga till den **ClaimsProviders** elementet i tilläggsfilen av din princip.

1. Öppna den *TrustFrameworkExtensions.xml*.
2. Hitta den **ClaimsProviders** element. Om det inte finns, lägger du till det under rotelementet.
3. Lägga till en ny **ClaimsProvider** på följande sätt:

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
            <Item Key=" XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ersätt `your-ADFS-domain` med namnet på din AD FS-domän och Ersätt värdet för den **identityProvider** utdataanspråket med din DNS-server (godtyckligt värde som anger din domän).
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet hur du kommunicerar med AD FS-konto. Försök att överföra tilläggsfilen i din princip bara för att bekräfta att den inte har några problem hittills.

1. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
2. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrering eller inloggning. Om du vill göra den tillgänglig, skapa en dubblett av en befintlig mall för användarresan och ändra den så att den även har AD FS-identitetsprovider.

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
2. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
3. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Byt namn på ID för användarresa. Till exempel `SignUpSignInADFS`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en knapp med identity-providern på en skärm för registrering eller inloggning. Om du lägger till en **ClaimsProviderSelection** element för ett AD FS-konto, en ny knapp visas när en användare finns på sidan.

1. Hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du skapade.
2. Under **ClaimsProviderSelections**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med en AD FS-konto för att ta emot en token.

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
2. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för **Id** som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Uppdatera värdet för **TechnicalProfileReferenceId** till den **Id** för den tekniska profilen du skapade tidigare. Till exempel `Contoso-SAML2`.

3. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurera en AD FS förtroende för förlitande part

Om du vill använda AD FS som en identitetsprovider i Azure AD B2C måste du skapa en AD FS förtroende för förlitande part med Azure AD B2C SAML-metadata. I följande exempel visas en URL-adress till SAML-metadata för en Azure AD B2C-tekniska profilen:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **din klient** med klientnamnet på din, till exempel din tenant.onmicrosoft.com.
- **din princip** med namnet på din. Till exempel B2C_1A_signup_signin_adfs.
- **din tekniska profil** med namnet på din identitet providern tekniska SAML-profilen. Till exempel Contoso-SAML2.
 
Öppna en webbläsare och gå till URL: en. Kontrollera att du anger rätt URL och att du har åtkomst till metadata XML-filen. Om du vill lägga till en ny förlitande part med snapin-modulen AD FS-hantering och konfigurera inställningarna manuellt, utför du följande procedur på en federationsserver. Medlemskap i **administratörer** eller motsvarande på den lokala datorn är minimikravet för att kunna slutföra den här proceduren.

1. I Serverhanteraren väljer **verktyg**, och välj sedan **AD FS Management**.
2. Välj **Lägg till förtroende för förlitande part**.
3. På den **Välkommen** väljer **anspråk medveten**, och klicka sedan på **starta**.
4. På den **Välj datakälla** väljer **importera data om förlitande part publicera online eller i ett lokalt nätverk**, ange din Azure AD B2C metadata-URL och klicka sedan på **nästa**.
5. På den **ange visningsnamn** anger en **visningsnamn**under **anteckningar**, ange en beskrivning för den här förlitande parten och klickar sedan på **nästa**.
6. På den **Välj principer för åtkomstkontroll** sidan, väljer en princip och klicka sedan på **nästa**.
7. På den **redo att lägga till förtroende** sidan, granskar du inställningarna och klicka sedan på **nästa** att spara din förlitande part förtroende information.
8. På den **Slutför** klickar du på **Stäng**, den här åtgärden visas automatiskt i **redigera Anspråksregler** dialogrutan.
9. Välj **Lägg till regel**.  
10. I **anspråksregelmall**väljer **skicka LDAP-attribut som anspråk**.
11. Ange en **Regelnamn för anspråk**. För den **attributarkiv**väljer **Välj Active Directory**, lägga till följande anspråk och sedan på **Slutför** och **OK**.

    | LDAP-attrubute | Typ av utgående anspråk |
    | -------------- | ------------------- |
    | User-Principal-Name | userPricipalName |
    | Efternamn | family_name |
    | Angivna namn | given_name |
    | E-Mail-Address | e-post |
    | Visningsnamn | namn |
    
12.  Baserat på din typ av certifikat kan behöva du ange HASH-algoritmen. I egenskapsfönstret förlitande part förtroende (B2C Demo) väljer den **Avancerat** fliken och ändra den **säkra hash-algoritm** till `SHA-256`, och klicka på **Ok**.  
13. I Serverhanteraren väljer **verktyg**, och välj sedan **AD FS Management**.
14. Välj det förlitande partsförtroendet som du har skapat, Välj **uppdateringen från Federationsmetadata**, och klicka sedan på **uppdatering**. 

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2c sker via ett program som du skapar i din klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **program**, och välj sedan **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

### <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInADFS.xml*.
2. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInADFS`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Till exempel ”http://contoso.com/B2C_1A_signup_signin_adfs” >
4. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för den nya användarresa som du skapade (SignUpSignInADFS).
5. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
6. Se till att Azure AD B2C-program som du skapat är markerad i den **Välj program** fältet och sedan testa den genom att klicka på **kör nu**.

