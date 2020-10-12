---
title: Håll mig inloggad i Azure Active Directory B2C
description: Lär dig att konfigurera Håll mig inloggad (KMSI avgör) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389198"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aktivera Håll mig inloggad (KMSI avgör) i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera funktionen Håll mig inloggad (KMSI avgör) för användare av dina webb program och interna program som har lokala konton i din Azure Active Directory B2C (Azure AD B2C)-katalogen. Den här funktionen beviljar åtkomst till användare som återgår till programmet utan att be dem att ange sitt användar namn och lösen ord på annat sätt. Den här åtkomsten återkallas när en användare loggar ut.

Användarna bör inte aktivera det här alternativet på offentliga datorer.

![Exempel på registrerings inloggnings sida med kryss rutan Håll mig inloggad](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Krav

- En Azure AD B2C klient som är konfigurerad för att tillåta inloggning på lokalt konto. KMSI avgör stöds inte för externa identitets leverantörs konton.
- Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Konfigurera sid identifieraren

Om du vill aktivera KMSI avgör anger du innehålls definitions `DataUri` elementet till [sid-ID](contentdefinitions.md#datauri) `unifiedssp` och [sid version](page-layout.md) *1.1.0* eller senare.

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Den här tilläggs filen är en av de principfiler som ingår i den anpassade principens start paket, som du borde ha skaffat i förutsättningen, [Kom igång med anpassade principer](custom-policy-get-started.md).
1. Sök efter **BuildingBlocks** -elementet. Om elementet inte finns lägger du till det.
1. Lägg till elementet **ContentDefinitions** i **BuildingBlocks** -elementet för principen.

    Den anpassade principen bör se ut som följande kodfragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Lägg till metadata till den självkontrollerade tekniska profilen

För att lägga till KMSI avgör-rutan på sidan för registrering och inloggning ställer du in `setting.enableRememberMe` metadata på True. Åsidosätt de tekniska profilerna för SelfAsserted-LocalAccountSignin-email i tilläggs filen.

1. Hitta ClaimsProviders-elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråks leverantör i ClaimsProviders-elementet:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Spara tilläggs filen.

## <a name="configure-a-relying-party-file"></a>Konfigurera en förlitande parts fil

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Öppna din anpassade princip fil. Till exempel *SignUpOrSignin.xml*.
1. Lägg till en `<UserJourneyBehaviors>` underordnad nod till noden om den inte redan finns `<RelyingParty>` . Den måste finnas omedelbart efter `<DefaultUserJourney ReferenceId="User journey Id" />` , till exempel: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Lägg till följande nod som underordnad till `<UserJourneyBehaviors>` elementet.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** – anger hur sessionen utökas med den tid som anges i `SessionExpiryInSeconds` och  `KeepAliveInDays` . `Rolling`Värdet (standard) visar att sessionen är utökad varje gång användaren utför autentiseringen. `Absolute`Värdet anger att användaren tvingas att autentisera igen efter den angivna tids perioden.

    - **SessionExpiryInSeconds**  – livs längden för sessionscookies när *jag låter mig vara inloggad* är inte aktive rad, eller om en användare inte väljer *Jag vill förbli inloggad*. Sessionen upphör att gälla när den `SessionExpiryInSeconds` har gått ut, eller så har webbläsaren stängts.

    - **KeepAliveInDays** – livs längden för sessionscookies när *jag låter mig vara inloggad* är aktive rad och användaren väljer *Håll mig inloggad*.  Värdet för `KeepAliveInDays` har företräde framför `SessionExpiryInSeconds` värdet och avgör förfallo tiden för sessionen. Om en användare stänger webbläsaren och öppnar den igen senare kan de fortfarande logga in så länge de är inom KeepAliveInDays tids period.

    Mer information finns i [användar resans beteenden](relyingparty.md#userjourneybehaviors).

Vi rekommenderar att du anger värdet för SessionExpiryInSeconds till en kort period (1200 sekunder), medan värdet för KeepAliveInDays kan anges till en relativt lång period (30 dagar), vilket visas i följande exempel:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Testa principen

1. Spara ändringarna och ladda upp filen.
1. Testa den anpassade princip som du laddade upp genom att gå till sidan princip i Azure Portal och sedan välja **Kör nu**.
1. Ange ditt **användar namn** och **lösen ord**, Välj **Behåll mig inloggad**och klicka sedan på **Logga**in.
1. Gå tillbaka till Azure-portalen. Gå till princip sidan och välj **Kopiera** för att kopiera inloggnings-URL: en.
1. I adress fältet i webbläsaren tar du bort `&prompt=login` parametern frågesträng, som tvingar användaren att ange sina autentiseringsuppgifter för begäran.
1. Klicka på **gå**till webbläsaren. Nu Azure AD B2C att utfärda en åtkomsttoken utan att du uppmanas att logga in igen. 

## <a name="next-steps"></a>Nästa steg

Hitta exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
