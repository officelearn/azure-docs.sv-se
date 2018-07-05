---
title: Lägg till en identitetsprovider för flera innehavare Azure AD med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lägg till en identitetsprovider för flera innehavare Azure AD med hjälp av anpassade principer – Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2a8a23245a17c9a80c70860588a8312dbbb5e926
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446100"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Tillåt användare att logga in på en identitetsprovider för flera innehavare Azure AD med hjälp av anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare som använder flera tenant-slutpunkten för Azure Active Directory (Azure AD) med [anpassade principer](active-directory-b2c-overview-custom.md). Detta gör att användare från flera Azure AD-klienter att logga in på Azure AD B2C utan att konfigurera en teknisk provider för varje klient. Gästen medlemmar i någon av dessa klienter dock **inte** att kunna logga in. För att du kommer behöva [konfigurerar varje klient](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Vi använder ”contoso.com” för i organisationens Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande anvisningar.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

De här stegen är:
     
1. Skapa en Azure Active Directory B2C (Azure AD B2C)-klient.
1. Skapa ett Azure AD B2C-program.    
1. Registrering av två principmodulen program.  
1. Konfigurera nycklar. 
1. Konfigurera startpaket.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Steg 1. Skapa en app för flera klienter Azure AD

Om du vill aktivera inloggning för användare som använder Azure AD-slutpunkten med flera innehavare, behöver du ett program för flera innehavare som registrerats i någon av dina Azure AD-klienter. I den här artikeln har hur vi du skapar ett program med flera innehavare Azure AD i din Azure AD B2C-klient. Aktivera inloggning för användare genom att använda den Azure AD-program med flera innehavare.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ditt konto i det översta fältet. Från den **Directory** väljer Azure AD B2C-klient för att kunna registrera Azure AD-program (fabrikamb2c.onmicrosoft.com).
1. Välj **fler tjänster** i det vänstra fönstret och Sök efter ”appregistreringar”.
1. Välj **Ny programregistrering**.
1. Ange ett namn för ditt program (till exempel `Azure AD B2C App`).
1. Välj **Webbapp/API** som programtyp.
1. För **inloggnings-URL**, ange följande URL, där `yourtenant` ersätts med namnet på din Azure AD B2C-klient (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Värdet för ”yourtenant” måste skrivas med små bokstäver i de **inloggnings-URL**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Spara program-ID.
1. Välj den nyligen skapade programmet.
1. Under den **inställningar** bladet väljer **egenskaper**.
1. Ange **med flera innehavare** till **Ja**.
1. Under den **inställningar** bladet väljer **nycklar**.
1. Skapa en ny nyckel och spara den. Du använder den i stegen i nästa avsnitt.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Steg 2. Lägg till Azure AD-nyckel i Azure AD B2C

Du behöver registrera programnyckeln i Azure AD B2C-inställningar. Gör så här:

1. Gå till inställningsmenyn för Azure AD B2C
1. Klicka på **Identitetsramverk** > **Principnycklar**.
1. Välj **+ Lägg till**.
1. Välj eller ange dessa alternativ:
   * Välj **manuell**.
   * För **namn**, Välj ett namn som matchar namnet på din Azure AD-klientorganisation (till exempel `AADAppSecret`).  Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
   * Klistra in din programnyckel i den **hemlighet** box.
   * Välj **signatur**.
1. Välj **Skapa**.
1. Bekräfta att du har skapat nyckeln `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Steg 3. Lägg till en anspråksprovider i din basprincipen

Om du vill att användarna ska logga in med hjälp av Azure AD, måste du definiera Azure AD som en anspråksprovider. Med andra ord måste du ange en slutpunkt som Azure AD B2C kommer att kommunicera med. Slutpunkten som ger en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera Azure AD som en anspråksprovider genom att lägga till Azure AD för att den `<ClaimsProvider>` nod i tilläggsfilen i din princip:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) från din arbetskatalog.
1. Hitta den `<ClaimsProviders>` avsnittet. Om det inte finns, lägger du till det under rotnoden.
1. Lägga till en ny `<ClaimsProvider>` noden på följande sätt:

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

1. Under den `<ClaimsProvider>` noden och uppdatera värdet för `<Domain>` till ett unikt värde som kan användas för att skilja den från andra identitetsleverantörer.
1. Under den `<TechnicalProfile>` noden och uppdatera värdet för `<DisplayName>`. Det här värdet visas på knappen Logga in på skärmen inloggning.
1. Uppdatera värdet för `<Description>`.
1. Ange `<Item Key="client_id">` till program-ID från Azure AD mulity klient registreringen.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Steg 3.1 begränsa åtkomsten till en viss lista med Azure AD-klienter

> [!NOTE]
> Med hjälp av `https://sts.windows.net` som värde för **ValidTokenIssuerPrefixes** gör att alla Azure AD-användare att logga in på din app.

Du behöver uppdatera listan över giltiga token utfärdare och begränsa åtkomsten till specifik lista med Azure AD-klienter användare kan logga in. Om du vill hämta värdena, måste du titta på metadata för varje Azure AD-klienter som du skulle vilja ha användare logga in från. Formatet för data som returneras liknar följande: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, där `yourAzureADtenant` är ditt Azure AD-klientnamn (contoso.com eller andra Azure AD-klient).
1. Öppna webbläsaren och gå till metadata-URL.
1. Leta efter objektet ”utfärdare” i webbläsaren och kopiera värdet. Det bör se ut så här: `https://sts.windows.net/{tenantId}/`.
1. Klistra in värdet för den `ValidTokenIssuerPrefixes` nyckel. Du kan lägga till flera genom att avgränsa dem med kommatecken. Ett exempel på detta är kommenterade i exemplet ovan XML.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Steg 4. Registrera anspråksprovider för Azure AD-konto

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Steg 4.1 göra en kopia av användarresa

Du måste nu lägga till identitetsprovidern Azure AD till en av dina användare-utbildning. Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrerings-registreringen/logga in.

Om du vill göra den tillgänglig, ska vi skapa en dubblett av en befintlig mall för användarresa och ändra den så att de innehåller även identitetsprovidern Azure AD:

1. Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).
1. Hitta den `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
1. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och hitta den `<UserJourneys>` element. Om elementet inte finns kan du lägga till en.
1. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
1. Byt namn på ID för den nya användarresan (till exempel byta namn på som `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Steg 4.2 visning på ”knappen”

Den `<ClaimsProviderSelection>` element är detsamma som en identitet provider-knappen på en registrerings-registreringen /-inloggningsskärmen. Om du lägger till en `<ClaimsProviderSelection>` element för Azure AD, en ny knapp visas när en användare finns på sidan. Lägga till det här elementet:

1. Hitta den `<OrchestrationStep>` nod som innehåller `Order="1"` i användarresan som du skapade.
1. Lägg till följande:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Ange `TargetClaimsExchangeId` till ett lämpligt värde. Vi rekommenderar att du följer samma regler som andra:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Steg 4.3 länk på knappen för att en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta den `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` noden.
1. Lägg till följande:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Uppdatera `Id` till samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
1. Uppdatera `TechnicalProfileReferenceId` -ID: t för den tekniska profilen du skapade tidigare (Common-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Steg 5: Skapa en ny RP-princip

Nu måste du uppdatera filen för förlitande part (RP) som kommer att initieras användarresa som du nyss skapade:
 
1. Gör en kopia av SignUpOrSignIn.xml i din arbetskatalog och Byt namn på den (till exempel byta namn till SignUpOrSignInWithAAD.xml).  
1. Öppna i ny fil och uppdatera den `PolicyId` attributet för `<TrustFrameworkPolicy>` med ett unikt värde (till exempel SignUpOrSignInWithAAD). Det här är namnet på din princip (till exempel B2C\_1A\_SignUpOrSignInWithAAD). 
1. Ändra den `ReferenceId` attributet i `<DefaultUserJourney>` att matcha ID för den nya användarresa som du skapade (SignUpOrSignUsingAzureAD). 
1. Spara dina ändringar och ladda upp filen. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Steg 6: Ladda upp principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.
1. Välj **Identitetsramverk**.
1. Välj **alla principer**.
1. Välj **överföra princip**.
1. Välj den **Skriv över principen om den finns** markerar du kryssrutan.
1. Ladda upp den `TrustFrameworkExtensions.xml` RP- fil (t.ex. `SignUpOrSignInWithAAD.xml`) och se till att de valideras.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Steg 7: Testa den anpassade principen med hjälp av kör nu

1. Välj **Azure AD B2C-inställningar**, och välj sedan **Identitetsramverk**.
    > [!NOTE]
    > Kör nu kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.

1. Öppna den förlitande part (RP) anpassade principen som du överförde (*B2C\_1A\_SignUpOrSignInWithAAD*), och välj sedan **kör nu**.
1. Du bör nu kunna logga in med hjälp av Azure AD-kontot.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Valfritt) Steg 8: Registrera anspråksprovidern för Azure AD-konto för profilredigering användarresa

Du kanske också vill lägga till identitetsprovider för Azure AD-konto till din `ProfileEdit` användarresa. Frigör användarresa genom att upprepa steg 4 till 6. Den här gången väljer den `<UserJourney>` nod som innehåller `Id="ProfileEdit"`. Spara, ladda upp och testa din princip.

## <a name="troubleshooting"></a>Felsökning

Läs mer om för att diagnostisera problem, [felsökning](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Nästa steg

Skicka feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
