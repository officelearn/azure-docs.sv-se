---
title: Håll mig inloggad i Azure Active Directory B2C
description: Lär dig att konfigurera Håll mig inloggad (KMSI avgör) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a27487fa69888b02883c3d9a2151887f41afc45
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189386"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aktivera Håll mig inloggad (KMSI avgör) i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera funktionen Håll mig inloggad (KMSI avgör) för användare av dina webb program och interna program som har lokala konton i din Azure Active Directory B2C (Azure AD B2C)-katalogen. Den här funktionen beviljar åtkomst till användare som återgår till programmet utan att be dem att ange sitt användar namn och lösen ord på annat sätt. Den här åtkomsten återkallas när en användare loggar ut.

Användarna bör inte aktivera det här alternativet på offentliga datorer.

![Exempel på registrerings inloggnings sida med kryss rutan Håll mig inloggad](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD B2C klient som är konfigurerad för att tillåta inloggning på lokalt konto. KMSI avgör stöds inte för externa identitets leverantörs konton.
- Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Konfigurera sid identifieraren

Om du vill aktivera KMSI avgör anger du innehålls definitionen `DataUri`-elementet till [sid identifierare](contentdefinitions.md#datauri) `unifiedssp` och [sid version](page-layout.md) *1.1.0* eller senare.

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>  . Den här tilläggs filen är en av de principfiler som ingår i den anpassade principens start paket, som du borde ha skaffat i förutsättningen, [Kom igång med anpassade principer](custom-policy-get-started.md).
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

1. Spara tilläggs filen.



## <a name="configure-a-relying-party-file"></a>Konfigurera en förlitande parts fil

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Öppna filen anpassad princip. Till exempel *SignUpOrSignin. XML*.
1. Om den inte redan finns lägger du till en `<UserJourneyBehaviors>` underordnad nod till noden `<RelyingParty>`. Den måste finnas omedelbart efter `<DefaultUserJourney ReferenceId="User journey Id" />`, till exempel: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Lägg till följande nod som underordnad till `<UserJourneyBehaviors>`-elementet.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** – anger hur sessionen utökas vid den tid som anges i `SessionExpiryInSeconds` och `KeepAliveInDays`. `Rolling` svärdet (default) visar att sessionen är utökad varje gång användaren utför autentisering. Värdet `Absolute` anger att användaren tvingas att autentisera igen efter den angivna tids perioden.

    - **SessionExpiryInSeconds** – livs längden för sessionscookies när *jag låter mig vara inloggad* är inte aktive rad, eller om en användare inte väljer *Jag vill förbli inloggad*. Sessionen upphör att gälla efter att `SessionExpiryInSeconds` har passerat, eller webbläsaren har stängts.

    - **KeepAliveInDays** – livs längden för sessionscookies när *jag låter mig vara inloggad* är aktive rad och användaren väljer *Håll mig inloggad*.  Värdet för `KeepAliveInDays` har företräde framför värdet för `SessionExpiryInSeconds` och avgör hur lång tid det tar att förfalla sessionen. Om en användare stänger webbläsaren och öppnar den igen senare kan de fortfarande logga in så länge de är inom KeepAliveInDays tids period.

    Mer information finns i [användar resans beteenden](relyingparty.md#userjourneybehaviors).

Vi rekommenderar att du anger värdet för SessionExpiryInSeconds till en kort period (1200 sekunder), medan värdet för KeepAliveInDays kan anges till en relativt lång period (30 dagar), vilket visas i följande exempel:

```XML
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

4. Spara ändringarna och ladda upp filen.
5. Testa den anpassade principen som du laddade upp genom att gå till sidan princip i Azure Portal och sedan välja **Kör nu**.

Du kan hitta exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
