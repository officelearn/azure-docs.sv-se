---
title: Lägg till AD FS som en SAML-identitetsprovider med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: En artikel om hur du konfigurerar AD FS 2016 med hjälp av SAML-protokoll och anpassade principer
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4380d21eded3f97e3b3107e78c9544a09d1b0bb2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338538"
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Lägga till AD FS som en SAML-identitetsprovider anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från AD FS-konto med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

De här stegen är:

1.  Skapa en AD FS förlitande part.
2.  Att lägga till certifikatet som ADFS förlitande part i Azure AD B2C.
3.  Lägga till anspråksprovidern till en princip.
4.  Registrerar du ADFS anspråk konto providern till en användarresa.
5.  Ladda upp principen till en Azure AD B2C-klient och testa den.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Du skapar ett anspråksmedvetet förtroende för förlitande part

För att använda AD FS som en identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa en AD FS förtroende för förlitande part och ange rätt parametrar.

Om du vill lägga till en ny förlitande part med snapin-modulen AD FS-hantering och konfigurera inställningarna manuellt, utför du följande procedur på en federationsserver.

Medlemskap i **administratörer**, eller motsvarande på den lokala datorn är minimikravet för att kunna slutföra den här proceduren. Granska information om hur du använder lämpliga konton och gruppmedlemskap i [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  I Serverhanteraren klickar du på **verktyg**, och välj sedan **AD FS Management**.

2.  Klicka på **Lägg till förtroende för förlitande part**.
    ![Lägg till förtroende för förlitande part](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  På den **Välkommen** väljer **anspråk medveten** och klicka på **starta**.
    ![Välj anspråk som är medvetna på välkomstsidan](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  På den **Välj datakälla** klickar du på **ange information om förlitande part manuellt**, och klicka sedan på **nästa**.
    ![Ange information om förlitande part](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  På den **ange visningsnamn** anger du ett namn i **visningsnamn**under **anteckningar** anger du en beskrivning för den här förlitande parten och klickar sedan på **nästa** .
    ![Ange visningsnamnet och anteckningar](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Valfri. Om du har ett valfritt token krypteringscertifikat, sedan på den **konfigurera certifikatet** klickar du på **Bläddra** att hitta din certifikatfil och klicka sedan på **nästa**.
    ![Konfigurera certifikat](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  På den **konfigurera URL** väljer den **aktivera stöd för protokollet SAML 2.0 WebSSO** markerar du kryssrutan. Under **tjänst-URL för förlitande part SAML 2.0 SSO**skriver Security Assertion Markup Language (SAML) tjänsten slutpunkts-URL för den här förlitande parten och klickar sedan på **nästa**.  För den **tjänst-URL för förlitande part SAML 2.0 SSO**, klistra in den `https://(tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policy}`. Ersätt {klient} med ditt klientnamn (till exempel contosob2c) och Ersätt {principen} med principnamnet tillägg (till exempel B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Principnamnet är det som signup_or_signin princip ärver från, i det här fallet är det: `B2C_1A_TrustFrameworkExtensions`.
    >URL: en kan till exempel vara: https://**contosob2c**.b2clogin.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Förlitande part URL för SAML 2.0 SSO](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. På den **konfigurera identifierare** sidan, anger du samma URL som i föregående steg, klicka på **Lägg till** lägger till dem i listan och klicka sedan på **nästa**.
    ![Förlitande part förtroende identifierare](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  På den **Välj principer för åtkomstkontroll** väljer du en princip och klicka på **nästa**.
    ![Välj principer för åtkomstkontroll](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  På den **redo att lägga till förtroende** sidan, granskar du inställningarna och klicka sedan på **nästa** att spara din förlitande part förtroende information.
    ![Spara din förlitande part förtroendeinformation](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  På den **Slutför** klickar du på **Stäng**, den här åtgärden visas automatiskt i **redigera Anspråksregler** dialogrutan.
    ![Redigera Anspråksregler](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Klicka på **Lägg till regel**.  
      ![Lägg till ny regel](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  I **anspråksregelmall**väljer **skicka LDAP-attribut som anspråk**.
    ![Välj Skicka LDAP-attribut som anspråksregel-mall](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Ange **Regelnamn för anspråk**. För den **attributarkiv** Välj **Välj Active Directory** lägga till följande anspråk och klicka sedan på **Slutför** och **OK**.
    ![Ange egenskaper för regeln](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  I Serverhanteraren väljer **förlitande Partsförtroenden** och sedan väljer du förlitande part förtroende du skapade och klicka på **egenskaper**.
    ![Redigera egenskaper för förlitande part](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  En förlitande part förtroende (B2C Demo) egenskapsfönstret klickar du på **signatur** fliken och klicka på **Lägg till**.  
    ![Set-signatur](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Lägg till din Signaturcertifikat (.cert-fil, utan privat nyckel).  
    ![Lägg till din Signaturcertifikat](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  I egenskapsfönstret för förlitande part förtroende (B2C Demo) klickar du på **Avancerat** fliken och ändra den **säkra hash-algoritm** till **SHA-1**, klickar du på **Ok**.  
    ![Ange säkra hash-algoritmen till SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Lägg till AD FS-kontonyckel för program i Azure AD B2C
Federation med AD FS-konton kräver en klienthemlighet för AD FS-konto för förtroende Azure AD B2C åt programmet. Du behöver lagra certifikatet AD FS i din Azure AD B2C-klient. 

1.  Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **Identitetsupplevelse**
2.  Välj **Principnycklar** att visa nycklarna som är tillgängliga i din klient.
3.  Klicka på **+ Lägg till**.
4.  För **alternativ**, använda **överför**.
5.  För **namn**, använda `ADFSSamlCert`.  
    Prefixet `B2C_1A_` kan läggas till automatiskt.
6.  I själva filuppladdningen ** Välj din .pfx-certifikatfil med privat nyckel. Obs: det här certifikatet (med den privata nyckeln) ska vara samma konto som utfärdats och används för den förlitande parten för AD FS.
![Ladda upp principnyckel](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Klicka på **Skapa**
8.  Bekräfta att du har skapat nyckeln `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Lägg till en anspråksprovider i din princip för tillägg
Om du vill att användarna att logga in med hjälp av AD FS-konto måste du definiera ADFS-konto som en anspråksprovider. Med andra ord måste du ange en slutpunkt som Azure AD B2C kommunicerar med. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Definiera ADFS som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i ditt tillägg principfil:

1. Öppna filen över princip tillägget (TrustFrameworkExtensions.xml) från din arbetskatalog. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), ett enkelt redigeringsprogram för olika plattformar.
2. Hitta den `<ClaimsProviders>` avsnittet
3. Lägg till följande XML-kodstycke under den `ClaimsProviders` elementet och Ersätt `identityProvider` med din DNS-server (godtyckligt värde som anger din domän) och spara filen. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrera anspråksprovider för AD FS-konto att logga in eller registrera i användarresan
Nu har identitetsprovidern ställts in.  Det är dock inte tillgängliga i någon av skärmarna registrerings-registreringen/logga in. Nu måste du lägga till identitetsleverantören för AD FS-konto i din användare `SignUpOrSignIn` användarresa. Om du vill göra den tillgänglig, kan vi skapa en dubblett av en befintlig mall för användarresan.  Sedan kan ändra vi den så att den omfattar identitetsprovidern som AD FS:

>[!NOTE]
>Om du tidigare har kopierat den `<UserJourneys>` element från bas-fil i din princip att tilläggsfil (TrustFrameworkExtensions.xml) kan du hoppa över det här avsnittet.

1.  Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).
2.  Hitta den `<UserJourneys>` element och kopiera hela innehållet i `<UserJourneys>` noden.
3.  Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och hitta den `<UserJourneys>` element. Om elementet inte finns kan du lägga till en.
4.  Klistra in hela innehållet i `<UserJournesy>` nod som du kopierade som underordnad till den `<UserJourneys>` element.

### <a name="display-the-button"></a>Visa knappen
Den `<ClaimsProviderSelections>` elementet definierar en lista med alternativ för val av anspråk providern och deras inbördes ordning.  `<ClaimsProviderSelection>` elementet är detsamma som en identitet provider-knappen på en registrerings-registreringen /-inloggningssida. Om du lägger till en `<ClaimsProviderSelection>` element för AD FS-konto, en ny knapp visas när en användare finns på sidan. Lägga till det här elementet:

1.  Hitta den `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i användarresan som du kopierade.
2.  Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`
3.  Lägga till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med AD FS-konto för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din AD FS-konto anspråksprovidern:

1.  Hitta den `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` noden.
2.  Lägga till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Se till att den `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
> * Se till att `TechnicalProfileReferenceId` har angetts till den tekniska profilen du skapade tidigare (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Ladda upp principen till din klient
1.  I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2.  Välj **Identitetsramverk**.
3.  Öppna den **alla principer** bladet.
4.  Välj **överföra princip**.
5.  Kontrollera **Skriv över principen om den finns** box.
6.  **Ladda upp** TrustFrameworkExtensions.xml och se till att inte misslyckas verifieringen

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av kör nu
1.  Öppna **Azure AD B2C-inställningar** och gå till **Identitetsramverk**.
2.  Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassade principen som du överförde. Välj **kör nu**.
3.  Du bör kunna logga in med AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Valfritt] Registrera anspråksprovidern för AD FS-konto för profilredigering användarresa
Du kanske vill lägga till identitetsleverantören för AD FS-konto också användaren `ProfileEdit` användarresa. Om du vill göra den tillgänglig, upprepa vi de två sista stegen:

### <a name="display-the-button"></a>Visa knappen
1.  Öppna tilläggsfilen av din princip (till exempel TrustFrameworkExtensions.xml).
2.  Hitta den `<UserJourney>` nod som innehåller `Id="ProfileEdit"` i användarresan som du kopierade.
3.  Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`
4.  Lägga till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
1.  Hitta den `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` noden.
2.  Lägga till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testa den anpassade profilredigering principen med hjälp av kör nu
1.  Öppna **Azure AD B2C-inställningar** och gå till **Identitetsramverk**.
2.  Öppna **B2C_1A_ProfileEdit**, den förlitande part (RP) anpassade principen som du överförde. Välj **kör nu**.
3.  Du bör kunna logga in med AD FS.

## <a name="download-the-complete-policy-files"></a>Hämta de fullständiga principfilerna
Valfritt: Vi rekommenderar att du skapar ditt scenario med en egen anpassad princip för igenom filer när du har slutfört komma igång med anpassade principer. [Princip-exempelfilerna endast för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
