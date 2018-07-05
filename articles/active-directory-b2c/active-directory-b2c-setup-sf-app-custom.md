---
title: Att lägga till en Salesforce SAML-provider med hjälp av anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Mer information om hur du skapar och hanterar anpassade principer för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1307fc455cacde81cb25ad58c5e99df21f126568
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448262"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: Logga in med Salesforce-konton via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du använder [anpassade principer](active-directory-b2c-overview-custom.md) att konfigurera inloggning för användare från en viss Salesforce-organisation.

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-ad-b2c-setup"></a>Konfigurationen av Azure AD B2C

Kontrollera att du har slutfört alla steg som visar hur du gör att [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) i Azure Active Directory B2C (Azure AD B2C).

Exempel på dessa är:

* Skapa en Azure AD B2C-klient.
* Skapa ett Azure AD B2C-program.
* Registrera två princip motorn för program.
* Ställ in nycklar.
* Ställ in startpaket.

### <a name="salesforce-setup"></a>Salesforce-installation

I den här artikeln förutsätter vi att du redan har gjort följande:

* Registrerat dig för en Salesforce-konto. Du kan registrera dig för en [kostnadsfritt Developer Edition konto](https://developer.salesforce.com/signup).
* [Konfigurera en min domän](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) för din Salesforce-organisation.

## <a name="set-up-salesforce-so-users-can-federate"></a>Konfigurera Salesforce så att användare kan federera

För att Azure AD B2C kommunicera med Salesforce, måste du hämta URL: en för Salesforce-metadata.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitetsprovider

> [!NOTE]
> I den här artikeln förutsätter vi att du använder [Salesforce Lightning upplevelse](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Logga in till Salesforce](https://login.salesforce.com/). 
2. På den vänstra menyn under **inställningar**, expandera **identitet**, och klicka sedan på **identitetsprovider**.
3. Klicka på **aktivera identitetsprovider**.
4. Under **Välj certifikatet**, Välj det certifikat du vill Salesforce som du använder för att kommunicera med Azure AD B2C. (Du kan använda standard-certifikatet.) Klicka på **Spara**. 

### <a name="create-a-connected-app-in-salesforce"></a>Skapa en ansluten app i Salesforce

1. På den **identitetsprovidern** går du till **tjänstleverantörer**.
2. Klicka på **tjänstleverantörer skapas nu via appar som är anslutna. Klicka här.**
3. Under **basinformation**, ange värdena som krävs för dina anslutna appar.
4. Under **Webbprograminställningarna**väljer den **aktivera SAML** markerar du kryssrutan.
5. I den **entitets-ID** fältet, anger du följande URL. Kontrollera att du ersätter värdet för `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. I den **ACS URL** fältet, anger du följande URL. Kontrollera att du ersätter värdet för `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Lämna standardvärdena för alla andra inställningar.
8. Rulla till slutet av listan och klicka sedan på **spara**.

### <a name="get-the-metadata-url"></a>Hämta metadata-URL

1. På översiktssidan av dina anslutna appar **hantera**.
2. Kopiera värdet för **slutpunkt för identifiering av Metadata**, och spara den. Du ska använda den senare i den här artikeln.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurera användare av Salesforce för att federera

1. På den **hantera** sidan av dina ansluten app, gå till **profiler**.
2. Klicka på **hantera profiler**.
3. Välj profiler (eller grupper av användare) som du vill federera med Azure AD B2C. Som systemadministratör, Välj den **systemadministratören** kryssrutan så att du kan federera med hjälp av ditt Salesforce-konto.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generera ett signeringscertifikat för Azure AD B2C

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

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Lägga till SAML-signeringscertifikat i Azure AD B2C

Ladda upp signeringscertifikatet till din Azure AD B2C-klient: 

1. Gå till din Azure AD B2C-klient. Klicka på **inställningar** > **Identitetsramverk** > **Principnycklar**.
2. Klicka på **+ Lägg till**, och sedan:
    1. Klicka på **alternativ** > **överför**.
    2. Ange en **namn** (till exempel SAMLSigningCert). Prefixet *B2C_1A_* läggs automatiskt till namnet på din nyckel.
    3. Om du ditt certifikat markerar du **överför filkontroll**. 
    4. Ange lösenord för certifikatet som du angav i PowerShell-skriptet.
3. Klicka på **Skapa**.
4. Kontrollera att du har skapat en nyckel (till exempel B2C_1A_SAMLSigningCert). Anteckna det fullständiga namnet (inklusive *B2C_1A_*). Vi kommer att hänvisa till den här nyckeln senare i principen.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Skapa Salesforce SAML anspråksprovidern i din basprincipen

Du måste definiera Salesforce som en anspråksprovider så att användarna kan logga in med hjälp av Salesforce. Med andra ord måste du ange den slutpunkt som Azure AD B2C kommer att kommunicera med. Slutpunkten kommer *ger* en uppsättning *anspråk* som Azure AD B2C använder för att verifiera att en viss användare har autentiserats. Gör detta genom att lägga till en `<ClaimsProvider>` för Salesforce i tilläggsfilen av din princip:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) i din arbetskatalog.
2. Hitta den `<ClaimsProviders>` avsnittet. Om det inte finns, kan du skapa det under rotnoden.
3. Lägga till en ny `<ClaimsProvider>`:

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
            <Item Key="RequestsSigned">false</Item>
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
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
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

Under den `<ClaimsProvider>` nod:

1. Ändra värdet för `<Domain>` till ett unikt värde som särskiljer `<ClaimsProvider>` från andra identitetsleverantörer.
2. Uppdatera värdet för `<DisplayName>` till ett visningsnamn för anspråksprovidern. Det här värdet används för närvarande inte.

### <a name="update-the-technical-profile"></a>Uppdatera den tekniska profilen

Definiera de protokoll som Azure AD B2C använder för att kommunicera med Azure Active Directory (Azure AD) för att få en SAML-token från Salesforce. Gör detta den `<TechnicalProfile>` element i `<ClaimsProvider>`:

1. Uppdatera ID för den `<TechnicalProfile>` noden. Detta ID används för att referera till den här tekniska profilen från andra delar av principen.
2. Uppdatera värdet för `<DisplayName>`. Det här värdet visas på knappen Logga in på din inloggningssida.
3. Uppdatera värdet för `<Description>`.
4. Salesforce använder SAML 2.0-protokollet. Kontrollera att värdet för `<Protocol>` är **SAML2**.

Uppdatera den `<Metadata>` avsnittet i föregående XML-filen så att inställningarna för ditt specifika Salesforce-konto. Uppdatera metadatavärdena i XML-filen:

1. Uppdatera värdet för `<Item Key="PartnerEntity">` med Salesforce-metadata-URL som du kopierade tidigare. Den har följande format: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. I den `<CryptographicKeys>` och uppdatera värdet för båda instanserna av `StorageReferenceId` till namnet på nyckeln för ditt signeringscertifikat (till exempel B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Din princip har nu konfigurerats så att Azure AD B2C vet hur du kommunicerar med Salesforce. Försök att överföra tilläggsfilen i din princip att verifiera att det inte finns några problem hittills. Ladda upp tilläggsfilen i din princip:

1. I din Azure AD B2C-klient går du till den **alla principer** bladet.
2. Välj den **Skriv över principen om den finns** markerar du kryssrutan.
3. Ladda upp tilläggsfil (TrustFrameworkExtensions.xml). Se till att det inte misslyckas verifieringen.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrera Salesforce SAML anspråksprovidern till en användarresa

Lägg sedan till Salesforce SAML-identitetsprovider till en av dina användare-utbildning. Nu identitetsprovidern har ställts in, men det är inte tillgängligt på någon av sidorna för användarens registrering eller inloggning. Skapa först en dubblett av en befintlig mall för användarresan för att lägga till identitetsleverantören till en inloggningssida. Sedan kan ändra mallen så att de innehåller även identitetsprovidern Azure AD.

1. Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).
2. Hitta den `<UserJourneys>` element och sedan kopiera hela `<UserJourney>` värde, inklusive Id = ”SignUpOrSignIn”.
3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml). Hitta den `<UserJourneys>` element. Om elementet inte finns kan du skapa en.
4. Klistra in hela kopieras `<UserJourney>` som underordnad till den `<UserJourneys>` element.
5. Byt namn på ID för den nya `<UserJourney>` (till exempel SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Visa knappen identitet leverantör

Den `<ClaimsProviderSelection>` element är detsamma som en knapp med identity-providern på en registrering eller inloggning. Genom att lägga till en `<ClaimsProviderSelection>` element för Salesforce, en ny knapp visas när en användare går till den här sidan. Så här visar knappen identity-providern:

1. I den `<UserJourney>` som du skapade, hitta den `<OrchestrationStep>` med `Order="1"`.
2. Lägg till följande XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Ange `TargetClaimsExchangeId` till ett logiskt värde. Vi rekommenderar att du följer samma regler som andra (till exempel  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Länka knappen identitet leverantör till en åtgärd

Nu när du har en identity-provider-knappen på plats kan du länka den till en åtgärd. I det här fallet är åtgärden för Azure AD B2C att kommunicera med Salesforce för att ta emot en SAML-token. Du kan göra detta genom att länka den tekniska profilen för din Salesforce SAML anspråksleverantör:

1. I den `<UserJourney>` nod, hitta den `<OrchestrationStep>` med `Order="2"`.
2. Lägg till följande XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Uppdatera `Id` till samma värde som du använde tidigare för `TargetClaimsExchangeId`.
4. Uppdatera `TechnicalProfileReferenceId` till den `Id` av tekniskt profilen du skapade tidigare (till exempel ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Ladda upp filen uppdaterade tillägg

Du är klar ändra tilläggsfilen. Spara och ladda upp den här filen. Se till att alla verifieringar lyckas.

### <a name="update-the-relying-party-file"></a>Uppdatera filen förlitande part

Därefter uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade:

1. Göra en kopia av SignUpOrSignIn.xml i din arbetskatalog. Sedan, byta namn (till exempel SignUpOrSignInWithAAD.xml).
2. Öppna i ny fil och uppdatera den `PolicyId` attributet för `<TrustFrameworkPolicy>` med ett unikt värde. Det här är namnet på din princip (till exempel SignUpOrSignInWithAAD).
3. Ändra den `ReferenceId` attributet i `<DefaultUserJourney>` så att den matchar den `Id` för nya användarresa som du skapade (till exempel SignUpOrSignUsingContoso).
4. Spara dina ändringar och överför sedan filen.

## <a name="test-and-troubleshoot"></a>Testa och felsöka

Om du vill testa den anpassade principen som du just har överfört i Azure-portalen, gå till principbladet och klicka sedan på **kör nu**. Om den inte finns i [anpassade Felsökningsprinciper](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Skicka feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
