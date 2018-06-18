---
title: Lägg till en identitetsleverantör för flera innehavare Azure AD med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lägg till en identitetsleverantör för flera innehavare Azure AD med anpassade principer - Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 83a2ce5d885a446713470c92fc3a638d37d4517d
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709231"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Tillåt användare att logga in på en identitetsleverantör för flera innehavare Azure AD med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare med flera innehavare slutpunkten för Azure Active Directory (AD Azure) med [anpassade principer](active-directory-b2c-overview-custom.md). Detta gör att användare från flera Azure AD-klienter att logga in på Azure AD B2C utan att konfigurera en teknisk provider för varje klient. Gästen medlemmar i någon av dessa klienter dock **inte** att kunna logga in. För att du behöver [konfigurerar varje klient](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Vi använder ”contoso.com” för organisations Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande instruktioner.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

De här stegen innefattar:
     
1. Skapa ett Azure Active Directory B2C-klient (Azure AD B2C).
1. Skapar ett program för Azure AD B2C.    
1. Registrering av två principmodulen program.  
1. Inställning av nycklar. 
1. Konfigurera startpaket.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Steg 1. Skapa en app för flera innehavare Azure AD

Om du vill aktivera inloggning för användare med flera innehavare Azure AD-slutpunkten måste ha ett program för flera innehavare som registrerats i något av Azure AD-klienter. I den här artikeln visas hur du skapar ett flera innehavare Azure AD-program i din Azure AD B2C-klient. Aktivera inloggning för användare genom att använda den Azure AD-program med flera innehavaren.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ditt konto på den översta raden. Från den **Directory** Välj Azure AD B2C-klient för att registrera Azure AD-program (fabrikamb2c.onmicrosoft.com).
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

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Steg 2. Lägg till Azure AD-nyckel till Azure AD B2C

Du måste registrera nyckeln för programmet i Azure AD B2C-inställningar. Gör så här:

1. Gå till inställningsmenyn för Azure AD B2C
1. Klicka på **identitet upplevelse Framework** > **princip nycklar**.
1. Välj **+ Lägg till**.
1. Välj eller ange dessa alternativ:
   * Välj **manuell**.
   * För **namn**, Välj ett namn som matchar din Azure AD-klientnamn (till exempel `AADAppSecret`).  Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
   * Klistra in nyckeln program i den **hemlighet** rutan.
   * Välj **signatur**.
1. Välj **Skapa**.
1. Bekräfta att du har skapat nyckeln `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Steg 3. Lägg till en anspråksprovider i en grundläggande princip

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
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
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
1. Under den `<TechnicalProfile>` nod, uppdatera värdet för `<DisplayName>`. Det här värdet kommer att visas på knappen Logga in på skärmen inloggning.
1. Uppdatera värdet för `<Description>`.
1. Ange `<Item Key="client_id">` program-ID: t från Azure AD mulity klienten app-registrering.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Steg 3.1 begränsa åtkomsten till en specifik lista med Azure AD-klienter

> [!NOTE]
> Med hjälp av `https://sts.windows.net` som värde för **ValidTokenIssuerPrefixes** tillåter alla Azure AD-användare kan logga in på din app.

Du måste uppdatera listan över giltiga token utfärdare och begränsa åtkomsten till specifik lista med Azure AD-klienter användare kan logga in. Om du vill hämta värdena du behöver titta på metadata för varje Azure AD-klienter som du vill att användare loggar in från. Formatet för data som ser ut ungefär så här: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, där `yourAzureADtenant` är ditt Azure AD-klientnamn (contoso.com eller andra Azure AD-klient).
1. Öppna webbläsaren och gå till metadata-URL.
1. Leta efter 'issuer-objektet i webbläsaren och kopiera värdet. Det bör se ut ungefär så här: `https://sts.windows.net/{tenantId}/`.
1. Klistra in värdet för den `ValidTokenIssuerPrefixes` nyckel. Du kan lägga till flera genom att avgränsa dem med kommatecken. Ett exempel på detta är kommenterade i exemplet ovan XML.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Steg 4. Registrera anspråksprovider för Azure AD-konto

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Steg 4.1 gör en kopia av användaren resa

Du måste nu lägga till Azure AD-identitetsleverantör till någon av dina användare resor. Nu identitetsleverantören har ställts in, men den är inte tillgänglig i alla skärmar sign-upp/inloggning.

Om du vill göra den tillgänglig, kommer vi skapar en dubblett av en befintlig mall användaren resa och sedan ändra den så att den också har identitetsleverantören som Azure AD:

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
1. Hitta de `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
1. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
1. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
1. Byt namn på ID för den nya användare resan (t.ex, Byt namn på det `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Steg 4.2 visas knappen ””

Den `<ClaimsProviderSelection>` element är detsamma som knappen identity-providern på en sign-upp/inloggningssida. Om du lägger till en `<ClaimsProviderSelection>` element för Azure AD, en ny knapp visas när en användare de hamnar på sidan. Lägg till det här elementet:

1. Hitta de `<OrchestrationStep>` nod som innehåller `Order="1"` i transporten användare som du skapade.
1. Lägg till följande:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Ange `TargetClaimsExchangeId` till ett lämpligt värde. Vi rekommenderar följande samma regler som andra:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Steg 4.3 länk för att en åtgärd

Nu när du har en knapp på plats måste länka till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med Azure AD ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
1. Lägg till följande:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Uppdatera `Id` till samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
1. Uppdatera `TechnicalProfileReferenceId` -ID: t för den tekniska profilen du skapade tidigare (Common-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Steg 5: Skapa en ny RP-princip

Nu måste du uppdatera filen förlitande part (RP) som initierar användaren resan som du just skapat:
 
1. Gör en kopia av SignUpOrSignIn.xml i arbetskatalogen och byta namn på den (till exempel ändra dess namn till SignUpOrSignInWithAAD.xml).  
1. Öppna ny fil och uppdatera den `PolicyId` attribut för `<TrustFrameworkPolicy>` med ett unikt värde (till exempel SignUpOrSignInWithAAD). Det här är namnet på principen (till exempel B2C\_1A\_SignUpOrSignInWithAAD). 
1. Ändra den `ReferenceId` attribut i `<DefaultUserJourney>` att matcha ID för den nya användare resa som du skapade (SignUpOrSignUsingAzureAD). 
1. Spara ändringarna och ladda upp filen. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Steg 6: Överför principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.
1. Välj **identitet upplevelse Framework**.
1. Välj **alla principer**.
1. Välj **överföra princip**.
1. Välj den **skriva över principen om den finns** kryssrutan.
1. Överför den `TrustFrameworkExtensions.xml` RP- fil (t.ex. `SignUpOrSignInWithAAD.xml`) och se till att de valideras.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Steg 7: Testa den anpassade principen genom att använda Kör nu

1. Välj **Azure AD B2C inställningar**, och välj sedan **identitet upplevelse Framework**.
    > [!NOTE]
    > Kör nu kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.

1. Öppna förlitande part (RP) anpassade principer som du har överfört (*B2C\_1A\_SignUpOrSignInWithAAD*), och välj sedan **kör nu**.
1. Du bör nu kunna logga in med hjälp av Azure AD-kontot.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Valfritt) Steg 8: Registrera anspråksprovidern Azure AD-konto för profilen Redigera användare resa

Du kanske också vill lägga till identitetsleverantören som Azure AD-konto till din `ProfileEdit` användaren resa. Frigör transporten användare genom att upprepa steg 4 till 6. Den här gången väljer den `<UserJourney>` nod som innehåller `Id="ProfileEdit"`. Spara, ladda upp och testa din princip.

## <a name="troubleshooting"></a>Felsökning

Läs mer om för att diagnostisera problem [felsökning](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Ge feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
