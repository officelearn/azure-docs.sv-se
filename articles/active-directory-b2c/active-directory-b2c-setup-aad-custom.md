---
title: Lägg till en Azure AD-provider med hjälp av anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cb70828960220bbc1e37321b77375fe2c42ee1b6
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059580"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: Logga in med hjälp av Azure AD-konton

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure Active Directory (Azure AD)-organisation med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

De här stegen är:

1. Skapa en Azure Active Directory B2C (Azure AD B2C)-klient.
2. Skapa ett Azure AD B2C-program.
3. Registrering av två principmodulen program.
4. Konfigurera nycklar.
5. Konfigurera startpaket.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en viss Azure AD-organisation kan du behöva registrera ett program i organisationen Azure AD-klient.

>[!NOTE]
> Vi använder ”contoso.com” för i organisationens Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande anvisningar.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ditt konto i det översta fältet. Från den **Directory** väljer du den organisationens Azure AD-klient där du vill registrera ditt program (contoso.com).
3. Välj **fler tjänster** i det vänstra fönstret och Sök efter ”appregistreringar”.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program (till exempel `Azure AD B2C App`).
6. Välj **Webbapp/API** som programtyp.
7. För **inloggnings-URL**, ange följande URL, där `yourtenant` ersätts med namnet på din Azure AD B2C-klient (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Värdet för ”yourtenant” måste skrivas med små bokstäver i de **inloggnings-URL**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. Spara program-ID.
9. Välj den nyligen skapade programmet.
10. Under den **inställningar** bladet väljer **nycklar**.
11. Ange nyckelbeskrivningen, Välj en varaktighet och klicka sedan på **spara**. Värdet för nyckeln visas. Kopiera den eftersom du kommer att använda i stegen i nästa avsnitt.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Lägg till Azure AD-nyckel i Azure AD B2C

Du måste lagra contoso.com programnyckel i din Azure AD B2C-klient. Gör så här:
1. Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **Identitetsramverk** > **Principnycklar**.
1. Välj **+ Lägg till**.
1. Välj eller ange dessa alternativ:
   * Välj **manuell**.
   * För **namn**, Välj ett namn som matchar namnet på din Azure AD-klientorganisation (till exempel `ContosoAppSecret`).  Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
   * Klistra in din programnyckel i den **hemlighet** box.
   * Välj **signatur**.
1. Välj **Skapa**.
1. Bekräfta att du har skapat nyckeln `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Lägg till en anspråksprovider i din basprincipen

Om du vill att användarna ska logga in med hjälp av Azure AD, måste du definiera Azure AD som en anspråksprovider. Med andra ord måste du ange en slutpunkt som Azure AD B2C kommer att kommunicera med. Slutpunkten som ger en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera Azure AD som en anspråksprovider genom att lägga till Azure AD för att den `<ClaimsProvider>` nod i tilläggsfilen i din princip:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) från din arbetskatalog.
1. Hitta den `<ClaimsProviders>` avsnittet. Om det inte finns, lägger du till det under rotnoden.
1. Lägga till en ny `<ClaimsProvider>` noden på följande sätt:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
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

1. Under den `<ClaimsProvider>` noden och uppdatera värdet för `<Domain>` till ett unikt värde som kan användas för att skilja den från andra identitetsleverantörer.
1. Under den `<ClaimsProvider>` noden och uppdatera värdet för `<DisplayName>` till ett eget namn för anspråksprovidern. Det här värdet används inte för närvarande.

### <a name="update-the-technical-profile"></a>Uppdatera den tekniska profilen

Om du vill hämta en token från Azure AD-slutpunkten som du behöver definiera de protokoll som Azure AD B2C ska använda vid kommunikation med Azure AD. Detta görs i den `<TechnicalProfile>` element i `<ClaimsProvider>`.
1. Uppdatera ID för den `<TechnicalProfile>` noden. Detta ID används för att referera till den här tekniska profilen från andra delar av principen.
1. Uppdatera värdet för `<DisplayName>`. Det här värdet visas på knappen Logga in på skärmen inloggning.
1. Uppdatera värdet för `<Description>`.
1. Azure AD använder OpenID Connect-protokollet, så se till att värdet för `<Protocol>` är `"OpenIdConnect"`.

Du måste uppdatera den `<Metadata>` avsnittet i XML-filen som anges tidigare för att återspegla konfigurationsinställningarna för ditt specifika Azure AD-klient. I XML-fil att uppdatera metadatavärden enligt följande:

1. Ange `<Item Key="METADATA">` till `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, där `yourAzureADtenant` är ditt Azure AD-klientnamn (contoso.com).
1. Öppna webbläsaren och gå till den `METADATA` URL som du uppdaterade nyss.
1. Leta efter objektet ”utfärdare” i webbläsaren och kopiera värdet. Det bör se ut så här: `https://sts.windows.net/{tenantId}/`.
1. Klistra in värdet för `<Item Key="ProviderName">` i XML-filen.
1. Ange `<Item Key="client_id">` till program-ID från registreringen.
1. Ange `<Item Key="IdTokenAudience">` till program-ID från registreringen.
1. Se till att `<Item Key="response_types">` är inställd på `id_token`.
1. Se till att `<Item Key="UsePolicyInRedirectUri">` är inställd på `false`.

Du måste också länka Azure AD-hemlighet som du registrerade i din Azure AD B2C-klient med Azure AD `<ClaimsProvider>` information:

* I den `<CryptographicKeys>` i föregående XML-filen och uppdatera värdet för `StorageReferenceId` referens-ID: t för hemlighet som du definierade (till exempel `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet att kommunicera med Azure AD-katalogen. Försök att överföra tilläggsfilen i din princip bara för att bekräfta att den inte har några problem hittills. Gör så här:

1. Öppna den **alla principer** bladet i Azure AD B2C-klienten.
1. Kontrollera den **Skriv över principen om den finns** box.
1. Ladda upp tilläggsfilen med (TrustFrameworkExtensions.xml) och se till att inte misslyckas verifieringen.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrera Azure AD-anspråksprovidern till en användarresa

Du måste nu lägga till identitetsprovidern Azure AD till en av dina användare-utbildning. Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrerings-registreringen/logga in. Om du vill göra den tillgänglig, ska vi skapa en dubblett av en befintlig mall för användarresa och ändra den så att de innehåller även identitetsprovidern Azure AD:

1. Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).
1. Hitta den `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
1. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och hitta den `<UserJourneys>` element. Om elementet inte finns kan du lägga till en.
1. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
1. Byt namn på ID för den nya användarresan (till exempel byta namn på som `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Visa på ”knappen”

Den `<ClaimsProviderSelection>` element är detsamma som en identitet provider-knappen på en registrerings-registreringen /-inloggningsskärmen. Om du lägger till en `<ClaimsProviderSelection>` element för Azure AD, en ny knapp visas när en användare finns på sidan. Lägga till det här elementet:

1. Hitta den `<OrchestrationStep>` nod som innehåller `Order="1"` i användarresan som du nyss skapade.
1. Lägg till följande:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Ange `TargetClaimsExchangeId` till ett lämpligt värde. Vi rekommenderar att du följer samma regler som andra:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta den `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` noden.
1. Lägg till följande:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Uppdatera `Id` till samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
1. Uppdatera `TechnicalProfileReferenceId` -ID: t för den tekniska profilen du skapade tidigare (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Ladda upp filen uppdaterade tillägg

Du är klar ändra tilläggsfilen. Spara den. Sedan överföra filen och se till att alla verifieringar lyckas.

### <a name="update-the-rp-file"></a>Uppdatera filen RP

Nu måste du uppdatera filen för förlitande part (RP) som kommer att initieras användarresa som du nyss skapade:

1. Gör en kopia av SignUpOrSignIn.xml i din arbetskatalog och Byt namn på den (till exempel byta namn till SignUpOrSignInWithAAD.xml).
1. Öppna i ny fil och uppdatera den `PolicyId` attributet för `<TrustFrameworkPolicy>` med ett unikt värde (till exempel SignUpOrSignInWithAAD). <br> Det här är namnet på din princip (till exempel B2C\_1A\_SignUpOrSignInWithAAD).
1. Ändra den `ReferenceId` attributet i `<DefaultUserJourney>` att matcha ID för den nya användarresa som du skapade (SignUpOrSignUsingContoso).
1. Spara dina ändringar och ladda upp filen.

## <a name="troubleshooting"></a>Felsökning

Testa den anpassade principen som du laddade upp genom att öppna tillhörande blad och klicka på **kör nu**. Läs mer om för att diagnostisera problem, [felsökning](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Skicka feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
