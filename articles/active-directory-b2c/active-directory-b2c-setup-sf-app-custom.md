---
title: 'Azure Active Directory B2C: Lägga till en Salesforce SAML-provider med hjälp av anpassade principer | Microsoft Docs'
description: Lär dig mer om hur du skapar och hanterar Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.openlocfilehash: 1ccf5c58eab9df9016224a91ddda952a05457e2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: Logga in med hjälp av Salesforce konton via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du använder [anpassade principer](active-directory-b2c-overview-custom.md) att ställa in inloggning för användare från en viss Salesforce-organisation.

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-ad-b2c-setup"></a>Installationsprogram för Azure AD B2C

Kontrollera att du har slutfört alla steg som visar hur till [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) i Azure Active Directory B2C (Azure AD B2C).

Exempel på dessa är:

* Skapa en Azure AD B2C-klient.
* Skapa ett program med Azure AD B2C.
* Registrera två motorn för program.
* Ställ in nycklar.
* Ställ in startpaket.

### <a name="salesforce-setup"></a>Salesforce-installationen

I den här artikeln förutsätter vi att du redan har gjort följande:

* Registrerat dig för en Salesforce-konto. Du kan registrera dig för en [kostnadsfritt konto Developer Edition](https://developer.salesforce.com/signup).
* [Konfigurera min domän](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) för organisationen Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Konfigurera Salesforce så att användare kan federera

Du måste hämta metadata Salesforce-URL för att Azure AD B2C kommunicera med Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Ställ in Salesforce som en identitetsleverantör

> [!NOTE]
> I den här artikeln förutsätter vi att du använder [Salesforce blixtsnabb upplevelse](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Logga in till Salesforce](https://login.salesforce.com/). 
2. På den vänstra menyn under **inställningar**, expandera **identitet**, och klicka sedan på **identitetsleverantör**.
3. Klicka på **aktivera identitetsleverantör**.
4. Under **Välj certifikatet**, Välj det certifikat du vill Salesforce som du använder för att kommunicera med Azure AD B2C. (Du kan använda standardcertifikatet.) Klicka på **Spara**. 

### <a name="create-a-connected-app-in-salesforce"></a>Skapa en ansluten app i Salesforce

1. På den **identitetsleverantör** går du till **leverantörer**.
2. Klicka på **leverantörer skapas nu via anslutna appar. Klicka här.**
3. Under **grundläggande Information**, ange obligatoriska värden för anslutna appen.
4. Under **Webbprograminställningarna**, Välj den **aktivera SAML** kryssrutan.
5. I den **enhets-ID** , ange följande URL-adress. Se till att ersätta värdet för `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. I den **ACS URL** , ange följande URL-adress. Se till att ersätta värdet för `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Lämna standardvärdena för alla andra inställningar.
8. Bläddra längst ned i listan och klicka sedan på **spara**.

### <a name="get-the-metadata-url"></a>Hämta metadata-URL

1. På översiktssidan för anslutna appen **hantera**.
2. Kopiera värdet för **Metadata slutpunktsidentifiering**, och spara den. Du ska använda senare i den här artikeln.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurera Salesforce användare att federera

1. På den **hantera** sidan av dina anslutna appen, gå till **profiler**.
2. Klicka på **hantera profiler**.
3. Välj profiler (eller grupper av användare) som du vill federera med Azure AD B2C. Som systemadministratör bör du välja den **systemadministratören** kryssrutan så att du kan federera med hjälp av ditt Salesforce-konto.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generera ett signeringscertifikat för Azure AD B2C

Förfrågningar som skickas till Salesforce måste signeras av Azure AD B2C. Öppna Azure PowerShell och kör följande kommandon för att generera ett signeringscertifikat.

> [!NOTE]
> Kontrollera att du uppdaterar innehavarens namn och lösenord i de två översta raderna.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Lägg till SAML-signeringscertifikat i Azure AD B2C

Överför signeringscertifikatet till din Azure AD B2C-klient: 

1. Gå till din Azure AD B2C-klient. Klicka på **inställningar** > **identitet upplevelse Framework** > **princip nycklar**.
2. Klicka på **+ Lägg till**, och sedan:
    1. Klicka på **alternativ** > **överför**.
    2. Ange en **namn** (till exempel SAMLSigningCert). Prefixet *B2C_1A_* läggs automatiskt till namnet på din nyckel.
    3. Om du vill välja certifikatet **överför filkontroll**. 
    4. Ange lösenord för certifikatet som du anger i PowerShell-skript.
3. Klicka på **Skapa**.
4. Kontrollera att du har skapat en nyckel (till exempel B2C_1A_SAMLSigningCert). Anteckna det fullständiga namnet (inklusive *B2C_1A_*). Du kommer att referera till den här nyckeln senare i principen.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Skapa anspråksprovider Salesforce SAML i din grundläggande princip

Du måste definiera Salesforce som en anspråksprovider så att användarna kan logga in med hjälp av Salesforce. Med andra ord, måste du ange den slutpunkt som Azure AD B2C kommer att kommunicera med. Slutpunkten ska *ange* en uppsättning *anspråk* som Azure AD B2C använder för att verifiera att en specifik användare har autentiserats. Det gör du genom att lägga till en `<ClaimsProvider>` för Salesforce i tilläggsfilen i principen:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) i arbetskatalogen.
2. Hitta de `<ClaimsProviders>` avsnitt. Om det inte finns kan du skapa det under rotnoden.
3. Lägg till en ny `<ClaimsProvider>`:

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

### <a name="update-the-technical-profile"></a>Uppdatera tekniska profilen

Om du vill hämta en SAML-token från Salesforce, definiera de protokoll som använder Azure AD B2C för att kommunicera med Azure Active Directory (AD Azure). Gör detta i den `<TechnicalProfile>` element av `<ClaimsProvider>`:

1. Uppdatera ID för den `<TechnicalProfile>` nod. Detta ID används för att referera till den här tekniska profilen från andra delar av principen.
2. Uppdatera värdet för `<DisplayName>`. Det här värdet visas på knappen Logga in på sidan logga in.
3. Uppdatera värdet för `<Description>`.
4. Salesforce använder SAML 2.0-protokollet. Kontrollera att värdet för `<Protocol>` är **SAML2**.

Uppdatering av `<Metadata>` avsnitt i föregående XML att visa inställningarna för ditt specifika Salesforce-konto. Uppdatera värdena metadata i XML:

1. Uppdatera värdet i `<Item Key="PartnerEntity">` med Salesforce-metadata-URL som du kopierade tidigare. Det har följande format: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. I den `<CryptographicKeys>` och uppdatera värdet för båda instanser av `StorageReferenceId` till namnet på nyckeln i ditt signeringscertifikat (till exempel B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Principen har nu konfigurerats så att Azure AD B2C vet hur att kommunicera med Salesforce. Försök att ladda upp tilläggsfilen av din princip för att kontrollera att det inte finns några problem hittills. Att överföra tilläggsfilen i principen:

1. I din Azure AD B2C-klient går du till den **alla principer** bladet.
2. Välj den **skriva över principen om den finns** kryssrutan.
3. Ladda upp fil (TrustFrameworkExtensions.xml). Se till att den inte inte kan valideras.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrera Salesforce SAML anspråksprovidern för en användare resa

Lägg sedan till Salesforce SAML-identitetsprovider till någon av dina användare resor. Nu identitetsleverantören har ställts in, men den är inte tillgänglig på någon av sidorna användaren registrering eller inloggning. Om du vill lägga till identitetsleverantören till en inloggningssida, först skapa en dubblett av en befintlig mall användaren resa. Ändra sedan mallen så att den har även Azure AD-identitetsleverantör.

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
2. Hitta de `<UserJourneys>` element, och sedan kopiera hela `<UserJourney>` värde, inklusive Id = ”SignUpOrSignIn”.
3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml). Hitta de `<UserJourneys>` element. Om det inte finns elementet, skapa en.
4. Klistra in hela kopieras `<UserJourney>` som underordnad till den `<UserJourneys>` element.
5. Byt namn på ID för den nya `<UserJourney>` (till exempel SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Visa knappen för providern identitet

Den `<ClaimsProviderSelection>` element är detsamma som knappen identity-providern på en registrering eller inloggning. Genom att lägga till en `<ClaimsProviderSelection>` element för Salesforce nya knappen visas när en användare till den här sidan. Knappen ska visas identitet providern:

1. I den `<UserJourney>` som du har skapat, söka efter den `<OrchestrationStep>` med `Order="1"`.
2. Lägg till följande XML-filen:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Ange `TargetClaimsExchangeId` till ett logiskt värde. Vi rekommenderar följande samma regler som andra (exempelvis  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Länka knappen leverantör identitet till en åtgärd

Nu när du har en identity-providern knapp på plats kan du länka den till en åtgärd. I det här fallet är åtgärden för Azure AD B2C att kommunicera med Salesforce ta emot en SAML-token. Du kan göra detta genom att länka tekniska profilen för dina Salesforce SAML anspråksleverantör:

1. I den `<UserJourney>` kan hitta den `<OrchestrationStep>` med `Order="2"`.
2. Lägg till följande XML-filen:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Uppdatera `Id` till samma värde som du använde tidigare för `TargetClaimsExchangeId`.
4. Uppdatera `TechnicalProfileReferenceId` till den `Id` av tekniska profilen du skapade tidigare (till exempel ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Ladda upp filen uppdaterade tillägg

Du är klar ändra tilläggsfilen. Spara och ladda upp den här filen. Se till att alla verifieringar lyckas.

### <a name="update-the-relying-party-file"></a>Uppdatera filen förlitande part

Därefter uppdaterar du filen förlitande part (RP) som initierar transporten användare som du skapade:

1. Gör en kopia av SignUpOrSignIn.xml i arbetskatalogen. Sedan, byta namn på den (till exempel SignUpOrSignInWithAAD.xml).
2. Öppna ny fil och uppdatera den `PolicyId` attribut för `<TrustFrameworkPolicy>` med ett unikt värde. Detta är namnet på principen (till exempel SignUpOrSignInWithAAD).
3. Ändra den `ReferenceId` attribut i `<DefaultUserJourney>` så att den matchar den `Id` transporten för nya användare som du skapade (till exempel SignUpOrSignUsingContoso).
4. Spara ändringarna och sedan ladda upp filen.

## <a name="test-and-troubleshoot"></a>Testa och felsöka

Om du vill testa den anpassade principen som du just har överfört i Azure portal, gå till principbladet och klicka sedan på **kör nu**. Om den inte finns [felsöka principer för anpassade](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Ge feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
