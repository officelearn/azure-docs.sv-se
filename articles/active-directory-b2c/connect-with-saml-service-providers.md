---
title: Konfigurera Azure AD B2C som ett SAML-IDP till dina program
title-suffix: Azure AD B2C
description: Konfigurera Azure AD B2C för att tillhandahålla SAML-protokollpåståenden till dina program (tjänsteleverantörer). Azure AD B2C fungerar som en identitetsprovider (IdP) till ditt SAML-program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051608"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrera ett SAML-program i Azure AD B2C

I den här artikeln får du lära dig hur du konfigurerar Azure Active Directory B2C (Azure AD B2C) för att fungera som en SAML-identitetsprovider (Security Assertion Markup Language) till dina program.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Organisationer som använder Azure AD B2C som sin lösning för hantering av kundidentitet och åtkomst kan kräva interaktion med identitetsleverantörer eller program som är konfigurerade för att autentisera med SAML-protokollet.

Azure AD B2C uppnår SAML-interoperabilitet på två sätt:

* Genom att fungera som en *identitetsleverantör* (IdP) och uppnå enkel inloggning (SSO) med SAML-baserade tjänsteleverantörer (dina program)
* Genom att fungera som *tjänsteleverantör* (SP) och interagera med SAML-baserade identitetsleverantörer som Salesforce och ADFS

![Diagram med B2C som identitetsprovider till vänster och B2C som tjänsteleverantör till höger](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Sammanfattar de två icke-exklusiva kärnscenarierna med SAML:

| Scenario | Azure AD B2C-roll | Så här gör du |
| -------- | ----------------- | ------- |
| Mitt program förväntar sig att ett SAML-påstående slutför en autentisering. | **Azure AD B2C fungerar som identitetsprovider (IdP)**<br />Azure AD B2C fungerar som en SAML IdP till programmen. | Denna artikel. |
| Mina användare behöver enkel inloggning med en SAML-kompatibel identitetsleverantör som ADFS, Salesforce eller Shibboleth.  | **Azure AD B2C fungerar som tjänsteleverantör (SP)**<br />Azure AD B2C fungerar som en tjänsteleverantör när du ansluter till SAML-identitetsprovidern. Det är en federationsproxy mellan ditt program och SAML-identitetsprovidern.  | <ul><li>[Konfigurera inloggning med ADFS som SAML IdP med hjälp av anpassade principer](identity-provider-adfs2016-custom.md)</li><li>[Konfigurera inloggning med en Salesforce SAML-leverantör med hjälp av anpassade principer](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Krav

* Slutför stegen i [Komma igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md). Du behöver den anpassade principen *SocialAndLocalAccounts* från det anpassade startpaketet för principen som beskrivs i artikeln.
* Grundläggande förståelse av SAML-protokollet (Security Assertion Markup Language).
* Ett webbprogram som konfigurerats som en SAML-tjänstleverantör (SP). För den här självstudien kan du använda ett [SAML-testprogram][samltest] som vi tillhandahåller.

## <a name="components-of-the-solution"></a>Komponenter i lösningen

Det finns tre huvudkomponenter som krävs för det här scenariot:

* **SAML-tjänstprovider** med möjlighet att skicka SAML-begäranden och ta emot, avkoda och svara på SAML-påståenden från Azure AD B2C. Detta är också känt som den förlitande parten.
* Offentligt tillgänglig **SAML-metadataslutpunkt** för din tjänsteleverantör.
* [Azure AD B2C-klient](tutorial-create-tenant.md)

Om du ännu inte har en SAML-tjänsteleverantör och en associerad metadataslutpunkt kan du använda det här exemplet SAML-program som vi har gjort tillgängligt för testning:

[ANSÖKAN OM SAML-test][samltest]

## <a name="1-set-up-certificates"></a>1. Ställ in certifikat

Om du vill skapa en förtroenderelation mellan din tjänsteleverantör och Azure AD B2C måste du tillhandahålla webbappen X509-certifikat.

* **Certifikat för tjänsteleverantörer**
  * Certifikat med en privat nyckel som lagras i din webbapp. Det här certifikatet används av din tjänsteleverantör för att signera SAML-begäran som skickas till Azure AD B2C. Azure AD B2C läser den offentliga nyckeln från tjänstleverantörens metadata för att validera signaturen.
  * (Valfritt) Certifikat med en privat nyckel som lagras i din webbapp. Azure AD B2C läser den offentliga nyckeln från tjänsteleverantörens metadata för att kryptera SAML- påståendet. Tjänsteleverantören använder sedan den privata nyckeln för att dekryptera påståendet.
* **Azure AD B2C-certifikat**
  * Certifikat med en privat nyckel i Azure AD B2C. Det här certifikatet används av Azure AD B2C för att signera SAML-svaret som skickas till din tjänsteleverantör. Din tjänsteleverantör läser den offentliga nyckeln för Azure AD B2C-metadata för att verifiera signaturen för SAML-svaret.

Du kan använda ett certifikat som utfärdats av en offentlig certifikatutfärdar myndighet eller, för den här självstudien, ett självsignerat certifikat.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Förbereda ett självsignerat certifikat

Om du inte redan har ett certifikat kan du använda ett självsignerat certifikat för den här självstudien. På Windows kan du använda PowerShells [cmdlet New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) för att generera ett certifikat.

1. Kör det här PowerShell-kommandot för att generera ett självsignerat certifikat. Ändra `-Subject` argumentet efter behov för ditt program och Azure AD B2C-klientnamn. Du kan också `-NotAfter` justera datumet för att ange ett annat förfallodatum för certifikatet.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Öppna **Hantera användarcertifikat** > **Aktuella personliga** > **Personal** > **användarcertifikat** > *yourappname.yourtenant.onmicrosoft.com*
1. Markera certifikatet > **åtgärd** > **alla aktiviteter** > **exportera**
1. Välj **Ja** > **Nästa** > **Ja, exportera den privata nyckeln** > **Nästa**
1. Acceptera standardvärdena för **exportfilformat**
1. Ange ett lösenord för certifikatet

### <a name="12-upload-the-certificate"></a>1.2 Ladda upp certifikatet

Ladda sedan upp SAML-certifikatet för kontroll- och svarssignering till Azure AD B2C.

1. Logga in på [Azure-portalen](https://portal.azure.com) och bläddra till din Azure AD B2C-klientorganisation.
1. Under **Principer**väljer du **Ramverk för identitetsupplevelse** och sedan **principnycklar**.
1. Välj **Lägg till**och välj sedan **Alternativ** > ladda**upp**.
1. Ange ett **namn**, till exempel *SamlIdpCert*. Prefixet *B2C_1A_* läggs automatiskt till i namnet på nyckeln.
1. Ladda upp certifikatet med hjälp av datakontrollen för uppladdningsfiler.
1. Ange lösenordet för certifikatet.
1. Välj **Skapa**.
1. Kontrollera att nyckeln visas som förväntat. Till exempel *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Förbered din policy

### <a name="21-create-the-saml-token-issuer"></a>2.1 Skapa SAML-tokenutfärdaren

Lägg nu till möjligheten för din klient att utfärda SAML-token med hjälp av [SAML-tokenutfärdare](saml-issuer-technical-profile.md) och [TEKNISKA PROFILER för SAML-sessionsprovidern.](custom-policy-reference-sso.md#samlssosessionprovider)

Öppna `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** i startpaketet för anpassad princip.

Leta `<ClaimsProviders>` reda på avsnittet och lägg till följande XML-kodavsnitt.

Du kan ändra värdet `IssuerUri` på metadata. Det här är utfärdaren URI som returneras i SAML-svaret från Azure AD B2C. Ditt förlitande part-program bör konfigureras för att acceptera en utfärdare URI under SAML-kontrollverifiering.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Lägg till SAML-förlitande part politik

Nu när din klient kan utfärda SAML-påståenden måste du skapa den SAML-förlitande partens policy och ändra användarfärden så att den utfärdar ett SAML-påstående i stället för en JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Skapa anmälan eller inloggningspolicy

1. Skapa en kopia av filen *SignUpOrSignin.xml* i arbetskatalogen för startpaketet och spara den med ett nytt namn. Registrera dig till exempel *Registrera dig iSAML.xml*. Det här är din principfil för förlitande part.

1. Öppna filen *SignUpOrSigninSAML.xml* i önskad redigerare.

1. Ändra `PolicyId` och `PublicPolicyUri` av politiken för att `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` _B2C_1A_signup_signin_saml_ och som visas nedan.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Lägg till följande XML-kodavsnitt strax före elementet. `<RelyingParty>` Den här XML-koden skriver över orchestration steg nummer 7 i _registreringsresan För SignUpOrSignIn._ Om du har startat från en annan mapp i startpaketet eller anpassat användarens färd genom `order` att lägga till eller ta bort orchestration-steg kontrollerar du att numret (i elementet) är justerat med `LocalAccounts`det som `SocialAccounts` anges i `SocialAndLocalAccountsWithMfa`användarresan för tokenutfärdarsteget (till exempel i de andra startpaketmapparna är det stegnummer 4 för , 6 för och 9 för ).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Ersätt hela `<TechnicalProfile>` elementet `<RelyingParty>` i elementet med följande tekniska profil-XML.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Uppdatera `tenant-name` med namnet på din Azure AD B2C-klient.

Den slutliga förlitande partens principfil ska se ut så här:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Ladda upp och testa dina policymetadata

Spara ändringarna och ladda upp den nya principfilen. När du har laddat upp båda principerna (tillägget och de förlitande partfilerna) öppnar du en webbläsare och navigerar till principmetadata.

Azure AD B2C princip IDP-metadata är information som används i SAML-protokollet för att exponera konfigurationen av en SAML-identitetsprovider. Metadata definierar platsen för tjänsterna, till exempel inloggning och ut signering, certifikat, inloggningsmetod med mera. Azure AD B2C-principmetadata är tillgängliga på följande URL. Ersätt `tenant-name` med namnet på din Azure AD `policy-name` B2C-klient och med namnet (ID) för principen:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Din anpassade princip och Azure AD B2C-klient är nu klara. Skapa sedan en programregistrering i Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Installationsprogram i Azure AD B2C-katalogen

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Registrera ditt program i Azure Active Directory

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *SAMLApp1*.
1. Under **Kontotyper som stöds**väljer du Konton i den här **organisationskatalogen**
1. Under **Omdirigera URI**väljer du `https://localhost` **Webb**och anger sedan . Du ändrar det här värdet senare i programregistreringens manifest.
1. Välj **Registrera**.

### <a name="42-update-the-app-manifest"></a>4.2 Uppdatera appmanifestet

För SAML-appar finns det flera egenskaper som du måste konfigurera i programregistreringens manifest.

1. I [Azure-portalen](https://portal.azure.com)navigerar du till programregistreringen som du skapade i föregående avsnitt.
1. Under **Hantera**väljer du **Manifest för** att öppna manifestredigeraren. Du kan ändra flera egenskaper i följande avsnitt.

#### <a name="identifieruris"></a>identifierarUris

Är `identifierUris` en strängsamling som innehåller användardefinierade URI:er som unikt identifierar en webbapp i dess Azure AD B2C-klientorganisation. Tjänsteleverantören måste ange det `Issuer` här värdet i elementet i en SAML-begäran.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Den här egenskapen representerar tjänsteleverantörens offentligt tillgängliga metadata-URL. Metadata-URL:en kan peka på en metadatafil som överförs till en anonymt tillgänglig slutpunkt, till exempel blob-lagring.

Metadata är information som används i SAML-protokollet för att exponera konfigurationen av en SAML-part, till exempel en tjänsteleverantör. Metadata definierar platsen för tjänsterna som inloggning och ut logga ut, certifikat, inloggningsmetod med mera. Azure AD B2C läser tjänsteleverantörens metadata och agerar därefter. Metadata krävs inte. Du kan också ange några av attribut som svar URI och utloggning URI direkt i appmanifestet.

Om det finns egenskaper som anges i *både* SAML-metadata-URL:en och i programregistreringens manifest **slås**de samman . De egenskaper som anges i metadata-URL:en bearbetas först och har företräde.

För den här självstudien, som använder SAML-testprogrammet, använder du följande värde för: `samlMetadataUrl`

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (valfritt)

Om du inte tillhandahåller en metadata-URI kan du uttryckligen ange svars-URL:en. Den här valfria `AssertionConsumerServiceUrl` egenskapen representerar (URL`SingleSignOnService` i tjänsteleverantörens metadata) och antas `BindingType` vara `HTTP POST`.

Om du väljer att konfigurera svars-URL:en och utloggnings-URL:en i programmanifestet utan att använda tjänstproviderns metadata, validerar azure AD B2C inte SAML-begäransignaturen eller krypterar SAML-svaret.

För den här självstudien, där du använder `url` SAML-testprogrammet, anger du egenskapen `replyUrlsWithType` för det värde som visas i följande JSON-kodavsnitt.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (valfritt)

Den här `Logout` valfria`SingleLogoutService` egenskapen representerar URL:en `BindingType` ( URL i `Http-Redirect`den förlitande partens metadata) och för detta antas vara .

För den här självstudien, som `logoutUrl` använder `https://samltestapp2.azurewebsites.net/logout`SAML-testprogrammet, lämnar du inställd på:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Uppdatera din programkod

Det sista steget är att aktivera Azure AD B2C som ett SAML-IDP i ditt SAML-förlitande partsprogram. Varje program är olika och stegen för att göra det varierar. Mer information finns i appens dokumentation.

Vissa eller alla följande krävs vanligtvis:

* **Metadata**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Utfärdare**: Använd entityID i metadatafilen
* **Inloggningsadress/SAML-slutpunkt/SAML-url:** Kontrollera värdet i metadatafilen
* **Certifikat**: Detta är *B2C_1A_SamlIdpCert*, men utan den privata nyckeln. Så här hämtar du certifikatets offentliga nyckel:

    1. Gå till metadata-URL:en som anges ovan.
    1. Kopiera värdet i `<X509Certificate>` elementet.
    1. Klistra in den i en textfil.
    1. Spara textfilen som en *CER-fil.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Testa med SAML-testappen (tillval)

För att slutföra den här guiden med hjälp av vår [SAML Test Application:][samltest]

* Uppdatera klientnamnet
* Uppdatera principnamn, till exempel *B2C_1A_signup_signin_saml*
* Ange den här utfärdar-URI::`https://contoso.onmicrosoft.com/app-name`

Välj **Logga in** och du bör presenteras med en användare inloggningsskärm. Vid inloggning utfärdas ett SAML-påstående tillbaka till exempelprogrammet.

## <a name="sample-policy"></a>Exempel-princip

Vi tillhandahåller en fullständig exempelprincip som du kan använda för testning med SAML-testappen.

1. Hämta [exempelpolicyn för SAML-SP-initierade inloggningar](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Uppdatera `TenantId` för att matcha ditt klientnamn, till exempel *contoso.b2clogin.com*
1. Behåll principnamnet *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Saml-modaliteter som stöds och inte stöds

Följande SAML-förlitande part (RP) scenarier stöds via din egen metadata slutpunkt:

* Flera utloggningsadresser eller POST-bindning för utloggnings-URL i huvudobjektet för program/tjänst.
* Ange signeringsnyckel för att verifiera RP-begäranden i huvudobjektet för program/tjänst.
* Ange tokenkrypteringsnyckel i huvudobjektet för program/tjänst.
* Identifierarproviderinitierade inloggningar stöds för närvarande inte i förhandsversionen.

## <a name="next-steps"></a>Nästa steg

- Du hittar mer information om [SAML-protokollet på OASIS webbplats](https://www.oasis-open.org/).
- Hämta SAML-testwebbappen från [Azure AD B2C GitHub community repo](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
