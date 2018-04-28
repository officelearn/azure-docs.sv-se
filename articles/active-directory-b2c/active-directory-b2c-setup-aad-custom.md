---
title: 'Azure Active Directory B2C: Lägg till en Azure AD-provider med hjälp av anpassade principer | Microsoft Docs'
description: Lär dig mer om Azure Active Directory B2C anpassade principer
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 40943c135746925929daf7ebae4714ef70eeda51
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: Logga in med hjälp av Azure AD-konton

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure Active Directory (AD Azure) organisation med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

De här stegen innefattar:

1. Skapa ett Azure Active Directory B2C-klient (Azure AD B2C).
2. Skapar ett program för Azure AD B2C.
3. Registrering av två principmodulen program.
4. Inställning av nycklar.
5. Konfigurera startpaket.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en specifik Azure AD-organisation, måste du registrera ett program i organisations Azure AD-klient.

>[!NOTE]
> Vi använder ”contoso.com” för organisations Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande instruktioner.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ditt konto på den översta raden. Från den **Directory** Välj organisations Azure AD-klient som du vill registrera ditt program (contoso.com).
3. Välj **fler tjänster** i den vänstra rutan och söka efter ”App registreringar”.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program (till exempel `Azure AD B2C App`).
6. Välj **Webbapp/API** som programtyp.
7. För **inloggnings-URL**, ange följande URL, där `yourtenant` ersättas med namnet på din Azure AD B2C-klient (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Värdet för ”yourtenant” måste vara gemener i den **inloggnings-URL**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. Spara programmet-ID.
9. Välj nyligen skapade programmet.
10. Under den **inställningar** bladet väljer **nycklar**.
11. Anger en beskrivning av nyckeln, markerar du en varaktighet och klicka sedan på **spara**. Värdet för nyckeln visas. Kopiera den eftersom det används i stegen i nästa avsnitt.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Lägg till Azure AD-nyckel till Azure AD B2C

Du måste lagra nyckeln contoso.com program i din Azure AD B2C-klient. Gör så här:
1. Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **identitet upplevelse Framework** > **princip nycklar**.
1. Välj **+ Lägg till**.
1. Välj eller ange dessa alternativ:
   * Välj **manuell**.
   * För **namn**, Välj ett namn som matchar din Azure AD-klientnamn (till exempel `ContosoAppSecret`).  Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
   * Klistra in nyckeln program i den **hemlighet** rutan.
   * Välj **signatur**.
1. Välj **Skapa**.
1. Bekräfta att du har skapat nyckeln `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Lägg till en anspråksprovider i en grundläggande princip

Om du vill att användarna att logga in med hjälp av Azure AD, måste du definiera Azure AD som en anspråksprovider. Med andra ord, måste du ange en slutpunkt som Azure AD B2C kommer att kommunicera med. Slutpunkten ger en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats. 

Du kan definiera Azure AD som en anspråksprovider genom att lägga till Azure AD för att den `<ClaimsProvider>` nod i tilläggsfilen i principen:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) från arbetskatalogen.
1. Hitta de `<ClaimsProviders>` avsnitt. Om det inte finns lägger du till den under rotnoden.
1. Lägg till en ny `<ClaimsProvider>` nod på följande sätt:

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
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
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

1. Under den `<ClaimsProvider>` nod, uppdatera värdet för `<Domain>` till ett unikt värde som kan användas för att skilja den från andra identitetsleverantörer.
1. Under den `<ClaimsProvider>` nod, uppdatera värdet för `<DisplayName>` till ett eget namn för anspråksprovidern. Det här värdet används inte för närvarande.

### <a name="update-the-technical-profile"></a>Uppdatera tekniska profilen

Om du vill hämta en token från Azure AD-slutpunkten måste du definiera de protokoll som Azure AD B2C ska använda för att kommunicera med Azure AD. Detta görs i den `<TechnicalProfile>` element av `<ClaimsProvider>`.
1. Uppdatera ID för den `<TechnicalProfile>` nod. Detta ID används för att referera till den här tekniska profilen från andra delar av principen.
1. Uppdatera värdet för `<DisplayName>`. Det här värdet kommer att visas på knappen Logga in på skärmen inloggning.
1. Uppdatera värdet för `<Description>`.
1. Azure AD används OpenID Connect-protokollet, så se till att värdet för `<Protocol>` är `"OpenIdConnect"`.

Du måste uppdatera de `<Metadata>` i XML-filen som anges ovan motsvarar konfigurationsinställningarna för ditt specifika Azure AD-klient. I XML-filen att uppdatera metadatavärden enligt följande:

1. Ange `<Item Key="METADATA">` till `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, där `yourAzureADtenant` är Azure AD-klientnamn (contoso.com).
1. Öppna webbläsaren och gå till den `METADATA` URL som du precis har uppdaterats.
1. Leta efter 'issuer-objektet i webbläsaren och kopiera värdet. Det bör se ut ungefär så här: `https://sts.windows.net/{tenantId}/`.
1. Klistra in värdet för `<Item Key="ProviderName">` i XML-filen.
1. Ange `<Item Key="client_id">` till det program-ID från appregistrering.
1. Ange `<Item Key="IdTokenAudience">` till det program-ID från appregistrering.
1. Se till att `<Item Key="response_types">` är inställd på `id_token`.
1. Se till att `<Item Key="UsePolicyInRedirectUri">` är inställd på `false`.

Du måste också länka Azure AD-hemlighet som du har registrerat i din Azure AD B2C-klient med Azure AD `<ClaimsProvider>` information:

* I den `<CryptographicKeys>` i föregående XML-filen och uppdatera värdet för `StorageReferenceId` referens-ID: t för den hemlighet som du har definierat (till exempel `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet hur att kommunicera med Azure AD-katalogen. Försök att ladda upp tilläggsfilen av din princip bara för att bekräfta att den inte har några problem hittills. Gör så här:

1. Öppna den **alla principer** bladet i Azure AD B2C-klient.
1. Kontrollera den **skriva över principen om den finns** rutan.
1. Ladda upp fil (TrustFrameworkExtensions.xml) och se till att inte misslyckas verifieringen.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrera Azure AD-anspråksprovidern för en användare resa

Du måste nu lägga till Azure AD-identitetsleverantör till någon av dina användare resor. Nu identitetsleverantören har ställts in, men den är inte tillgänglig i alla skärmar sign-upp/inloggning. Om du vill göra den tillgänglig, kommer vi skapar en dubblett av en befintlig mall användaren resa och sedan ändra den så att den också har identitetsleverantören som Azure AD:

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
1. Hitta de `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
1. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
1. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
1. Byt namn på ID för den nya användare resan (t.ex, Byt namn på det `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Visa knappen ””

Den `<ClaimsProviderSelection>` element är detsamma som knappen identity-providern på en sign-upp/inloggningssida. Om du lägger till en `<ClaimsProviderSelection>` element för Azure AD, en ny knapp visas när en användare de hamnar på sidan. Lägg till det här elementet:

1. Hitta de `<OrchestrationStep>` nod som innehåller `Order="1"` i transporten användare som du nyss skapade.
1. Lägg till följande:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Ange `TargetClaimsExchangeId` till ett lämpligt värde. Vi rekommenderar följande samma regler som andra:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste länka till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med Azure AD ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
1. Lägg till följande:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Uppdatera `Id` till samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
1. Uppdatera `TechnicalProfileReferenceId` -ID: t för den tekniska profilen du skapade tidigare (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Ladda upp filen uppdaterade tillägg

Du är klar ändra tilläggsfilen. Spara den. Sedan överföra filen och se till att alla verifieringar lyckas.

### <a name="update-the-rp-file"></a>Uppdatera RP-filen

Nu måste du uppdatera filen förlitande part (RP) som initierar användaren resan som du just skapat:

1. Gör en kopia av SignUpOrSignIn.xml i arbetskatalogen och byta namn på den (till exempel ändra dess namn till SignUpOrSignInWithAAD.xml).
1. Öppna ny fil och uppdatera den `PolicyId` attribut för `<TrustFrameworkPolicy>` med ett unikt värde (till exempel SignUpOrSignInWithAAD). <br> Det här är namnet på principen (till exempel B2C\_1A\_SignUpOrSignInWithAAD).
1. Ändra den `ReferenceId` attribut i `<DefaultUserJourney>` att matcha ID för den nya användare resa som du skapade (SignUpOrSignUsingContoso).
1. Spara ändringarna och ladda upp filen.

## <a name="troubleshooting"></a>Felsökning

Testa den anpassade principen som du just har överfört öppna dess bladet och klicka på **kör nu**. Läs mer om för att diagnostisera problem [felsökning](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Ge feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
