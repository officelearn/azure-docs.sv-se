---
title: Lägg till en identitetsleverantör för flera innehavare Azure AD med anpassade principer - Azure Active Directory B2C | Microsoft Docs
description: Lägg till en identitetsleverantör för flera innehavare Azure AD med anpassade principer - Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: e751272222c493f3061eb15b8106f1c13eec960d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-into-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Tillåt användare att logga in på en identitetsleverantör för flera innehavare Azure AD med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare som använder gemensamma slutpunkten för Azure Active Directory (AD Azure) med [anpassade principer](active-directory-b2c-overview-custom.md).

>[!NOTE]
> Vi använder ”contoso.com” för organisations Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande instruktioner.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

De här stegen innefattar:

1. Skapa ett Azure Active Directory B2C-klient (Azure AD B2C).
2. Skapar ett program för Azure AD B2C.
3. Registrering av två principmodulen program.
4. Inställning av nycklar.
5. Konfigurera startpaket.

## <a name="create-a-multi-tenant-azure-ad-app"></a>Skapa en app för flera innehavare Azure AD

Om du vill aktivera inloggning för användare med flera innehavare Azure AD-slutpunkten måste du ha ett program för flera innehavare som registrerats i någon av dina klienter

I den här artikeln visas hur du skapar ett flera innehavare Azure AD-program i din Azure AD B2C-klient.

>[!NOTE]
> Om du vill att Azure AD-användare **och användare med Microsoft-konton** för att logga in, hoppa över det här avsnittet och i stället registrera ett program i den [Microsoft developer-portalen](https://apps.dev.microsoft.com).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ditt konto på den översta raden. Från den **Directory** Välj Azure AD B2C-klient som du vill registrera ditt program (fabrikamb2c.onmicrosoft.com).
1. Välj **fler tjänster** i den vänstra rutan och söka efter ”App registreringar”.
1. Välj **Ny programregistrering**.
1. Ange ett namn för ditt program (till exempel `Azure AD B2C App`).
1. Välj **Webbapp/API** som programtyp.
1. För **inloggnings-URL**, ange följande URL, där `yourtenant` ersättas med namnet på din Azure AD B2C-klient (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Värdet för ”yourtenant” måste vara gemener i den **inloggnings-URL**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Spara programmet-ID.
1. Välj nyligen skapade programmet.
1. Under den **inställningar** bladet väljer **egenskaper**.
1. Ange **flera innehavare** till **Ja**.
1. Under den **inställningar** bladet väljer **nycklar**.
1. Skapa en ny nyckel och spara den. Du använder den i stegen i nästa avsnitt.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Lägg till Azure AD-nyckel till Azure AD B2C

Du måste registrera nyckeln för programmet i Azure AD B2C-inställningar. Gör så här:

1. Gå till inställningsmenyn för Azure AD B2C
1. Klicka på **identitet upplevelse Framework** > **princip nycklar**.
1. Välj **+ Lägg till**.
1. Välj eller ange dessa alternativ:
   * Välj **manuell**.
   * För **namn**, Välj ett namn som matchar din Azure AD-klientnamn (till exempel `AADAppSecret`).  Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
   * Klistra in nyckeln program i den **hemlighet** rutan.
   * Välj **signatur**.
5. Välj **Skapa**.
6. Bekräfta att du har skapat nyckeln `B2C_1A_ContosoAppSecret`.

## <a name="add-a-claims-provider-in-your-base-policy"></a>Lägg till en anspråksprovider i en grundläggande princip

Om du vill att användarna att logga in med hjälp av Azure AD, måste du definiera Azure AD som en anspråksprovider. Med andra ord, måste du ange en slutpunkt som Azure AD B2C kommer att kommunicera med. Slutpunkten ger en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats. 

Du kan definiera Azure AD som en anspråksprovider genom att lägga till Azure AD för att den `<ClaimsProvider>` nod i tilläggsfilen i principen:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) från arbetskatalogen.
1. Hitta de `<ClaimsProviders>` avsnitt. Om det inte finns lägger du till den under rotnoden.
1. Lägg till en ny `<ClaimsProvider>` nod på följande sätt:

    ```XML
      <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the client ID below to Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below -->
            <Key Id="client_secret" StorageReferenceId="Reference to Client Secret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
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
1. Ange `<Item Key="client_id">` till det program-ID från appregistrering.
1. Se till att `<Item Key="UsePolicyInRedirectUri">` är inställd på `false`.

Du måste uppdatera listan över giltiga token-utfärdare. Om du vill hämta värdena du behöver titta på metadata för varje Azure AD-klienter som du vill att användare loggar in från. Formatet för data som ser ut ungefär så här: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, där `yourAzureADtenant` är Azure AD-klientnamn (contoso.com).
1. Öppna webbläsaren och gå till metadata-URL.
1. Leta efter 'issuer-objektet i webbläsaren och kopiera värdet. Det bör se ut ungefär så här: `https://sts.windows.net/{tenantId}/`.
1. Klistra in värdet för den `ValidTokenIssuerPrefixes` nyckel. Du kan lägga till flera som separeras med dem med kommatecken. 

> [!NOTE]
> Med hjälp av `https://sts.windows.net` som prefixvärdet tillåter alla Azure AD-användare kan logga in på din app.

Du måste också länka Azure AD-programhemlighet som du har registrerat i din Azure AD B2C-klient med Azure AD `<ClaimsProvider>` information:

* I den `<CryptographicKeys>` i föregående XML-filen och uppdatera värdet för `StorageReferenceId` referens-ID: t för den hemlighet som du har definierat (till exempel `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet hur att kommunicera med den gemensamma slutpunkten för Azure AD. Försök att ladda upp tilläggsfilen av din princip bara för att bekräfta att den inte har några problem hittills. Gör så här:

1. Öppna den **alla principer** bladet i Azure AD B2C-klient.
1. Kontrollera den **skriva över principen om den finns** rutan.
1. Ladda upp fil (TrustFrameworkExtensions.xml) och se till att inte misslyckas verifieringen.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrera Azure AD-anspråksprovidern för en användare resa

Du måste nu lägga till Azure AD-identitetsleverantör till någon av dina användare resor. Nu identitetsleverantören har ställts in, men den är inte tillgänglig i alla skärmar sign-upp/inloggning. Om du vill göra den tillgänglig, kommer vi skapar en dubblett av en befintlig mall användaren resa och sedan ändra den så att den också har identitetsleverantören som Azure AD:

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
1. Hitta de `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
1. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
1. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
1. Byt namn på ID för den nya användare resan (t.ex, Byt namn på det `SignUpOrSignUsingAzureAD`).

### <a name="display-the-button"></a>Visa knappen ””

Den `<ClaimsProviderSelection>` element är detsamma som knappen identity-providern på en sign-upp/inloggningssida. Om du lägger till en `<ClaimsProviderSelection>` element för Azure AD, en ny knapp visas när en användare de hamnar på sidan. Lägg till det här elementet:

1. Hitta de `<OrchestrationStep>` nod som innehåller `Order="1"` i transporten användare som du nyss skapade.
1. Lägg till följande:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Ange `TargetClaimsExchangeId` till ett lämpligt värde. Vi rekommenderar följande samma regler som andra:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste länka till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med Azure AD ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
1. Lägg till följande:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Uppdatera `Id` till samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
1. Uppdatera `TechnicalProfileReferenceId` -ID: t för den tekniska profilen du skapade tidigare (Common-AAD).

### <a name="upload-the-updated-extension-file"></a>Ladda upp filen uppdaterade tillägg

Du är klar ändra tilläggsfilen. Spara den. Sedan överföra filen och se till att alla verifieringar lyckas.

### <a name="update-the-rp-file"></a>Uppdatera RP-filen

Nu måste du uppdatera filen förlitande part (RP) som initierar användaren resan som du just skapat:

1. Gör en kopia av SignUpOrSignIn.xml i arbetskatalogen och byta namn på den (till exempel ändra dess namn till SignUpOrSignInWithAAD.xml).
1. Öppna ny fil och uppdatera den `PolicyId` attribut för `<TrustFrameworkPolicy>` med ett unikt värde (till exempel SignUpOrSignInWithAAD). <br> Det här är namnet på principen (till exempel B2C\_1A\_SignUpOrSignInWithAAD).
1. Ändra den `ReferenceId` attribut i `<DefaultUserJourney>` att matcha ID för den nya användare resa som du skapade (SignUpOrSignUsingAzureAD).
1. Spara ändringarna och ladda upp filen.

## <a name="troubleshooting"></a>Felsökning

Testa den anpassade principen som du just har överfört öppna dess bladet och klicka på **kör nu**. Läs mer om för att diagnostisera problem [felsökning](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Ge feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
