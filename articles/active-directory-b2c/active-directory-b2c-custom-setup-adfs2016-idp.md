---
title: "Azure Active Directory B2C: Lägga till AD FS som en SAML-identitetsprovider anpassade principer"
description: En artikel om hur du konfigurerar AD FS 2016 med SAML-protokoll och anpassade principer
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8713fc7dd27023e1244ccb00673dd1652689baf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Lägga till AD FS som en SAML-identitetsprovider anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från AD FS-konto med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Krav

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

De här stegen innefattar:

1.  Skapa en AD FS förlitande part.
2.  Lägger till certifikatet som ADFS förlitande part i Azure AD B2C.
3.  Lägger till anspråksleverantören till en princip.
4.  Registrerar ADFS anspråk konto providern till en användare resa.
5.  Ladda upp principen till en Azure AD B2C-klient och testa den.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Så här skapar du ett anspråksmedvetet förtroende för förlitande part

Om du vill använda AD FS som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa en AD FS förtroende för förlitande part och ange rätt parametrar.

Om du vill lägga till en ny förlitande part med snapin-modulen AD FS-hantering och konfigurera inställningarna manuellt, utför du följande procedur på en federationsserver.

Medlemskap i **administratörer**, eller motsvarande på den lokala datorn krävs för att slutföra den här proceduren. Läs mer om kontomedlemskap och gruppmedlemskap i [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  I Serverhanteraren klickar du på **verktyg**, och välj sedan **AD FS Management**.

2.  Klicka på **Lägg till förlitande part**.
    ![Lägg till förlitande part](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  På den **Välkommen** väljer **anspråk medveten** och på **starta**.
    ![Välj anspråk medveten på välkomstsidan](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  På den **Välj datakälla** klickar du på **in data om förlitande part manuellt**, och klicka sedan på **nästa**.
    ![Ange information om förlitande part](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  På den **ange visningsnamn** anger du ett namn i **visningsnamn**under **anteckningar** Skriv en beskrivning för den här förlitande parten och klickar sedan på **nästa**.
    ![Ange namn och anteckningar](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Valfri. Om du har ett certifikat med valfritt tokenkryptering, sedan på den **konfigurera certifikat** klickar du på **Bläddra** att hitta certifikatfilen och klicka sedan på **nästa**.
    ![Konfigurera certifikat](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  På den **konfigurera URL** väljer den **aktivera stöd för protokollet SAML 2.0 WebSSO** kryssrutan. Under **URL för förlitande part SAML 2.0 SSO**skriver Security Assertion Markup Language (SAML) tjänsten slutpunkts-URL för den här förlitande parten och klickar sedan på **nästa**.  För den **URL för förlitande part SAML 2.0 SSO**, klistra in den `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Ersätt {klient} med namnet för din klient (till exempel contosob2c.onmicrosoft.com) och Ersätt {principen} med tillägg-principnamn (till exempel B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Principnamnet är den som signup_or_signin princip ärver från, i det här fallet är det: `B2C_1A_TrustFrameworkExtensions`.
    >URL: en kan till exempel vara: https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Förlitande part SAML 2.0 SSO-tjänstens URL](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. På den **konfigurera identifierare** , ange samma Webbadress som i föregående steg, klickar du på **Lägg till** lägger till dem i listan och klicka sedan på **nästa**.
    ![Förlitande parts förtroende identifierare](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  På den **Välj princip för åtkomstkontroll** Välj en princip och klicka på **nästa**.
    ![Välj princip för åtkomstkontroll](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  På den **redo att lägga till förtroende** sidan, granskar du inställningarna och klicka sedan på **nästa** att spara din förlitande part förtroende information.
    ![Spara informationen om förlitande part förtroende](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  På den **Slutför** klickar du på **Stäng**, den här åtgärden visas automatiskt i **redigera Anspråksregler** dialogrutan.
    ![Redigera Anspråksregler](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Klicka på **Lägg till regel**.  
      ![Lägg till ny regel](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  I **anspråksregelmall**väljer **skicka LDAP-attribut som anspråk**.
    ![Välj Skicka LDAP-attribut som anspråk mall-regel](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Ange **Regelnamn för anspråk**. För den **attributarkiv** Välj **Välj Active Directory** lägga till följande anspråk och klicka sedan på **Slutför** och **OK**.
    ![Ange egenskaper för regeln](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  I Serverhanteraren väljer **förtroende för förlitande part** sedan välja den förlitande parten litar du skapade och klicka på **egenskaper**.
    ![Redigera egenskaper för förlitande part](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Egenskapsfönstret för förlitande part förtroende (B2C Demo) klickar du på en **signatur** och klicka på **Lägg till**.  
    ![Ange signatur](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Lägg till din Signaturcertifikat (.cert-fil, utan privat nyckel).  
    ![Lägg till din Signaturcertifikat](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  Klicka på egenskapsfönstret för förlitande part förtroende (B2C Demo) **Avancerat** fliken och ändra den **Secure hash algorithm** till **SHA-1**, klickar du på **Ok**.  
    ![Ange säkra hash-algoritmen till SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Lägg till program för AD FS kontonyckel i Azure AD B2C
Federation med AD FS-konton kräver en klienthemlighet för AD FS-konto för Azure AD B2C-förtroende för programmet. Du måste lagra AD FS-certifikat i din Azure AD B2C-klient. 

1.  Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **identitet upplevelse Framework**
2.  Välj **princip nycklar** att visa nycklarna som är tillgängliga i din klient.
3.  Klicka på **+ Lägg till**.
4.  För **alternativ**, använda **överför**.
5.  För **namn**, Använd `ADFSSamlCert`.  
    Prefixet `B2C_1A_` kan läggas till automatiskt.
6.  I ladda upp filen ** väljer din PFX-fil för certifikat med privat nyckel. Obs: det här certifikatet (med den privata nyckeln) ska vara samma som utfärdas och används för den förlitande parten för AD FS.
![Ladda upp nyckeln för principen](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Klicka på **Skapa**
8.  Bekräfta att du har skapat nyckeln `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Lägg till en anspråksprovider i din princip för tillägg
Om du vill att användarna att logga in med hjälp av AD FS-konto måste du definiera ADFS-kontot som en anspråksprovider. Med andra ord, måste du ange en slutpunkt som Azure AD B2C kommunicerar med. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats.

Definiera ADFS som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i tillägget-principfil:

1. Öppna filen för princip för tillägg (TrustFrameworkExtensions.xml) från arbetskatalogen. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), en enkel plattformsoberoende redigerare.
2. Hitta de `<ClaimsProviders>` avsnitt
3. Lägg till följande XML-kodstycke under den `ClaimsProviders` element och Ersätt `identityProvider` med din DNS (godtyckligt värde som anger din domän) och spara filen. 

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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrera anspråksprovider för AD FS-konto att logga in eller loggar in användare resa
Nu har identitetsleverantören ställts in.  Det är dock inte tillgänglig i alla skärmar sign-upp/inloggning. Nu måste du lägga till identitetsleverantören ADFS-konto i din användare `SignUpOrSignIn` användare resa. Om du vill göra den tillgänglig, kan vi skapa en dubblett av en befintlig mall användaren resa.  Sedan ändra vi den så att den omfattar identitetsleverantör AD FS:

>[!NOTE]
>Om du tidigare har kopierat den `<UserJourneys>` element från bas-filen för din princip för tilläggsfilen (TrustFrameworkExtensions.xml) kan du hoppa över det här avsnittet.

1.  Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
2.  Hitta de `<UserJourneys>` element och kopiera hela innehållet i `<UserJourneys>` nod.
3.  Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
4.  Klistra in hela innehållet i `<UserJournesy>` nod som du kopierade som underordnad till den `<UserJourneys>` element.

### <a name="display-the-button"></a>Visa knappen
Den `<ClaimsProviderSelections>` elementet definierar en lista över alternativ för val av anspråk providern och deras inbördes ordning.  `<ClaimsProviderSelection>`elementet är detsamma som knappen identity-providern på en sign-upp/inloggningssidan. Om du lägger till en `<ClaimsProviderSelection>` element för AD FS-konto, en ny knapp visas när en användare de hamnar på sidan. Lägg till det här elementet:

1.  Hitta de `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i transporten användare som du kopierade.
2.  Leta upp den `<OrchestrationStep>` nod som innehåller`Order="1"`
3.  Lägg till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste länka till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med AD FS-konto för att ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för din AD FS-konto anspråksleverantör:

1.  Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
2.  Lägg till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Se till att den `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
> * Se till att `TechnicalProfileReferenceId` har angetts för teknisk profilen du skapade tidigare (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Ladda upp principen till din klient
1.  I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2.  Välj **identitet upplevelse Framework**.
3.  Öppna den **alla principer** bladet.
4.  Välj **överföra princip**.
5.  Kontrollera **skriva över principen om den finns** rutan.
6.  **Överför** TrustFrameworkExtensions.xml och se till att inte misslyckas verifieringen

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen genom att använda Kör nu
1.  Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.
2.  Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.
3.  Du ska kunna logga in med AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Valfritt] Registrera anspråksprovidern ADFS-konto för profilen Redigera användare resa
Du kanske vill lägga till identitetsleverantören ADFS kontot också till dina användare `ProfileEdit` användare resa. Vi Upprepa de två sista stegen för att göra den tillgänglig:

### <a name="display-the-button"></a>Visa knappen
1.  Öppna filen för tillägg av principen (till exempel TrustFrameworkExtensions.xml).
2.  Hitta de `<UserJourney>` nod som innehåller `Id="ProfileEdit"` i transporten användare som du kopierade.
3.  Leta upp den `<OrchestrationStep>` nod som innehåller`Order="1"`
4.  Lägg till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
1.  Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
2.  Lägg till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testa en anpassad profil-Redigera princip genom att använda Kör nu
1.  Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.
2.  Öppna **B2C_1A_ProfileEdit**, förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.
3.  Du ska kunna logga in med AD FS.

## <a name="download-the-complete-policy-files"></a>Hämta de fullständiga principfilerna
Valfritt: Vi rekommenderar att du skapar ditt scenario med en egen anpassad princip för filer när du har slutfört komma igång med principer för anpassade igenom. [Principen exempelfiler endast för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
