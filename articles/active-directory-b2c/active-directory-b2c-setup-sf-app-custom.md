---
title: Ställ in logga in med ett Salesforce SAML-providern med hjälp av anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Ställ in logga in med ett Salesforce SAML-providern med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8d0d37b988f36e23d8bd12b5a225b1714205b3fc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158085"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Ställ in logga in med ett Salesforce SAML-providern med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från ett Salesforce-organisation med [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C. Du aktiverar inloggning genom att lägga till en [tekniska SAML-profilen](saml-technical-profile.md) till en anpassad princip.

## <a name="prerequisites"></a>Förutsättningar

- Utför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Om du inte redan gjort det, registrera dig för en [kostnadsfritt Developer Edition konto](https://developer.salesforce.com/signup). Den här artikeln används den [Salesforce Lightning upplevelse](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Konfigurera en min domän](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) för din Salesforce-organisation.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitetsprovider

1. [Logga in till Salesforce](https://login.salesforce.com/). 
2. På den vänstra menyn under **inställningar**, expandera **identitet**, och välj sedan **identitetsprovider**.
3. Välj **aktivera identitetsprovider**.
4. Under **Välj certifikatet**, Välj det certifikat du vill Salesforce som du använder för att kommunicera med Azure AD B2C. Du kan använda standard-certifikatet. 
5. Klicka på **Spara**. 

### <a name="create-a-connected-app-in-salesforce"></a>Skapa en ansluten app i Salesforce

1. På den **identitetsprovidern** väljer **tjänstleverantörer skapas nu via appar som är anslutna. Klicka här.**
2. Under **basinformation**, ange värdena som krävs för dina anslutna appar.
3. Under **Webbprograminställningarna**, kontrollera den **aktivera SAML** box.
4. I den **entitets-ID** fältet, anger du följande URL. Se till att du ersätter värdet för `your-tenant` med namnet på din Azure AD B2C-klient.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. I den **ACS URL** fältet, anger du följande URL. Se till att du ersätter värdet för `your-tenant` med namnet på din Azure AD B2C-klient.
      
      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Rulla till slutet av listan och klicka sedan på **spara**.

### <a name="get-the-metadata-url"></a>Hämta metadata-URL

1. På översiktssidan av dina anslutna appar **hantera**.
2. Kopiera värdet för **slutpunkt för identifiering av Metadata**, och spara den. Du ska använda den senare i den här artikeln.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurera användare av Salesforce för att federera

1. På den **hantera** sidan för din ansluten app, klickar du på **Spravovat Profily**.
2. Välj profiler (eller grupper av användare) som du vill federera med Azure AD B2C. Som systemadministratör, Välj den **systemadministratören** kryssrutan så att du kan federera med hjälp av ditt Salesforce-konto.

## <a name="generate-a-signing-certificate"></a>Generera ett signeringscertifikat

Begäranden som skickas till Salesforce måste signeras av Azure AD B2C. Om du vill generera ett signeringscertifikat, öppnar du Azure PowerShell och kör sedan följande kommandon.

> [!NOTE]
> Se till att du uppdaterar innehavarens namn och lösenord i de två översta raderna.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du behöver lagra certifikatet som du skapade i din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer **Identitetsupplevelse – FÖRHANDSVERSION**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **alternativ**, Välj `Upload`.
7. Ange ett **Namn** för principen. Till exempel SAMLSigningCert. Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
8. Bläddra till och välj det B2CSigningCert.pfx-certifikat som du skapade. 
9. Ange den **lösenord** för certifikatet.
3. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråksprovider

Om du vill att användarna ska logga in med ett Salesforce-konto, måste du definiera kontot som en anspråksprovider som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera en Salesforce-konto som en anspråksprovider genom att lägga till den **ClaimsProviders** elementet i tilläggsfilen av din princip.

1. Öppna den *TrustFrameworkExtensions.xml*.
2. Hitta den **ClaimsProviders** element. Om det inte finns, lägger du till det under rotelementet.
3. Lägga till en ny **ClaimsProvider** på följande sätt:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

4. Uppdatera värdet för **PartnerEntity** med Salesforce-metadata-URL som du kopierade tidigare.
5. Uppdatera värdet för båda instanserna av **StorageReferenceId** till namnet på nyckeln för ditt signeringscertifikat. Till exempel B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet att kommunicera med ditt Salesforce-konto. Försök att överföra tilläggsfilen i din princip bara för att bekräfta att den inte har några problem hittills.

1. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
2. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrering eller inloggning. Du skapar en dubblett av en befintlig mall för användarresa så att den blir tillgänglig, och ändra den så att den har också identitetsprovider Salesforce.

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
2. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
3. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Byt namn på ID för användarresa. Till exempel `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en knapp med identity-providern på en skärm för registrering eller inloggning. Om du lägger till en **ClaimsProviderSelection** element för ett LinkedIn-konto, en ny knapp visas när en användare finns på sidan.

1. Hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du nyss skapade.
2. Under **ClaimsProviderSelects**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med ett Salesforce-konto för att ta emot en token.

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
2. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för **Id** som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```
    
    Uppdatera värdet för **TechnicalProfileReferenceId** till den **Id** för den tekniska profilen du skapade tidigare. Till exempel `LinkedIn-OAUTH`.

3. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2c sker via ett program som du skapar i din klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **program**, och välj sedan **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du nyss skapade:

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInSalesforce.xml*.
2. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInSalesforce`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för den nya användarresa som du skapade (SignUpSignInSalesforce).
5. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
6. Se till att Azure AD B2C-program som du skapat är markerad i den **Välj program** fältet och sedan testa den genom att klicka på **kör nu**.
