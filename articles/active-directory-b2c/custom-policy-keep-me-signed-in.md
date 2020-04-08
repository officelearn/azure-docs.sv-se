---
title: Håll mig inloggad i Azure Active Directory B2C
description: Lär dig hur du konfigurerar Keep Me Signed In (KMSI) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803168"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aktivera Behåll mig inloggad (KMSI) i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera KMSI-funktioner (Keep Me Signed In) för användare av din webbplats och dina inbyggda program som har lokala konton i din Azure Active Directory B2C-katalog (Azure AD B2C). Den här funktionen ger åtkomst till användare som återvänder till ditt program utan att du uppmanar dem att ange sitt användarnamn och lösenord igen. Den här åtkomsten återkallas när en användare loggar ut.

Användare bör inte aktivera det här alternativet på offentliga datorer.

![Exempel på registreringssida för registrering som visar kryssrutan Håll mig inloggad](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Krav

- En Azure AD B2C-klient som är konfigurerad för att tillåta lokal kontosignering. KMSI stöds inte för externa identitetsproviderkonton.
- Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Konfigurera sididentifieraren

Om du vill aktivera KMSI ställer du in `DataUri` innehållsdefinitionselementet på [sididentifierare](contentdefinitions.md#datauri) `unifiedssp` och [sidversion](page-layout.md) *1.1.0* eller högre.

1. Öppna principfilens tilläggsfil. Till exempel <em> `SocialAndLocalAccounts/` </em>. Den här tilläggsfilen är en av de principfiler som ingår i det anpassade startpaketet för principen, som du borde ha fått i förutsättningen, [Kom igång med anpassade principer](custom-policy-get-started.md).
1. Sök efter elementet **BuildingBlocks.** Om elementet inte finns lägger du till det.
1. Lägg till **ContentDefinitions-elementet** i elementet **BuildingBlocks** i principen.

    Din anpassade princip ska se ut som följande kodavsnitt:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Lägga till metadata i den självsäkra tekniska profilen

Om du vill lägga till KMSI-kryssrutan på registrerings- `setting.enableRememberMe` och inloggningssidan anger du metadata till true. Åsidosätt de självhärnade-lokala rätkontonSignin-E-post tekniska profilerna i tilläggsfilen.

1. Leta reda på elementet ClaimsProviders. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråksprovider i elementet ClaimsProviders:

```XML
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

1. Spara tilläggsfilen.

## <a name="configure-a-relying-party-file"></a>Konfigurera en förlitande partfil

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Öppna din anpassade principfil. Registrera till exempel *Registrera DigOrSignin.xml*.
1. Om den inte redan finns `<UserJourneyBehaviors>` lägger du `<RelyingParty>` till en underordnad nod i noden. Det måste placeras `<DefaultUserJourney ReferenceId="User journey Id" />`omedelbart efter `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`, till exempel: .
1. Lägg till följande nod som `<UserJourneyBehaviors>` underordnad för elementet.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Anger hur sessionen förlängs med `SessionExpiryInSeconds` `KeepAliveInDays`den tid som anges i och . Värdet `Rolling` (standard) anger att sessionen utökas varje gång användaren utför autentisering. Värdet `Absolute` anger att användaren tvingas att omauktisera efter den angivna tidsperioden.

    - **SessionExpiryInSeconds** - Livstiden för sessionscookies när *jag hålls inloggad* är inte aktiverad, eller om en användare inte väljer håll mig *inloggad*. Sessionen upphör att `SessionExpiryInSeconds` gälla när den har godkänts, eller så stängs webbläsaren.

    - **KeepAliveInDays** - Livstiden för sessionscookies när *jag hålls inloggad* är aktiverad och användaren väljer att hålla mig *inloggad*.  Värdet för `KeepAliveInDays` har företräde `SessionExpiryInSeconds` framför värdet och dikterar sessionens förfallotid. Om en användare stänger webbläsaren och öppnar den igen senare kan han eller hon fortfarande logga in tyst så länge den är inom keepaliveindays-tidsperioden.

    Mer information finns i [användarfärdsbeteenden](relyingparty.md#userjourneybehaviors).

Vi rekommenderar att du ställer in värdet för SessionExpiryInSeconds till en kort period (1200 sekunder), medan värdet för KeepAliveInDays kan ställas in på en relativt lång period (30 dagar), som visas i följande exempel:

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

## <a name="test-your-policy"></a>Testa din policy

1. Spara ändringarna och ladda sedan upp filen.
1. Om du vill testa den anpassade principen som du laddade upp går du till principsidan i Azure-portalen och väljer sedan **Kör nu**.
1. Skriv ditt **användarnamn** och **lösenord,** välj **Håll mig inloggad**och klicka sedan på logga **in**.
1. Gå tillbaka till Azure-portalen. Gå till principsidan och välj sedan **Kopiera** för att kopiera inloggnings-URL:en.
1. I webbläsarens adressfält `&prompt=login` tar du bort frågesträngparametern, vilket tvingar användaren att ange sina autentiseringsuppgifter på den begäran.
1. Klicka på **Gå**i webbläsaren . Nu Azure AD B2C kommer att utfärda en åtkomsttoken utan att du uppmanas att logga in igen. 

## <a name="next-steps"></a>Nästa steg

Hitta exempelprincipen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
