---
title: Lägga till anspråk och anpassa användarindata i anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användarindata och lägger till anspråk i registrerings- eller inloggningsresan i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473684"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Lägga till anspråk och anpassa användarindata med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln samlar du in ett nytt attribut under din registreringsresa i Azure Active Directory B2C (Azure AD B2C). Du får användarnas stad, konfigurerar den som en listruta och definierar om den måste tillhandahållas.

> [!NOTE]
> I det här exemplet används den inbyggda anspråksstaden. I stället kan du välja ett av de [inbyggda attributen Azure AD B2C som](user-profile-attributes.md) stöds eller ett anpassat attribut. Om du vill använda ett anpassat attribut [aktiverar du anpassade attribut i principen](custom-policy-custom-attributes.md). Om du vill använda ett annat inbyggt eller anpassat attribut ersätter du "city" med det attribut du väljer, till exempel det inbyggda *attributjobbetTitle* eller ett anpassat attribut som *extension_loyaltyId*.  

Du kan samla in initiala data från användarna med hjälp av registrerings- eller inloggningsanvändarresan. Ytterligare anspråk kan samlas in senare med hjälp av en användarresa för profilredigering. När som helst Azure AD B2C samlar in information direkt från användaren interaktivt, använder Identity Experience Framework sin [självsäkra tekniska profil](self-asserted-technical-profile.md). I det här exemplet:

1. Definiera ett "stad"-anspråk. 
1. Fråga användaren om deras stad.
1. Bevara staden till användarprofilen i Azure AD B2C-katalogen.
1. Läs stadsanspråket från Azure AD B2C-katalogen vid varje inloggning.
1. Returnera staden till ditt förlitande part-program efter inloggning eller registrering.  

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med sociala och lokala konton.

## <a name="define-a-claim"></a>Definiera ett anspråk

Ett anspråk ger en tillfällig lagring av data under en Azure AD B2C-principkörning. [Skadeschemat](claimsschema.md) är den plats där du deklarerar dina anspråk. Följande element används för att definiera anspråket:

- **DisplayName** - En sträng som definierar den användarinriktade etiketten.
- [DataType](claimsschema.md#datatype) - Typen av anspråk.
- **UserHelpText** - Hjälper användaren att förstå vad som krävs.
- [UserInputType](claimsschema.md#userinputtype) - Typ av indatakontroll, till exempel textruta, radioval, listruta eller flera val.

Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.

1. Sök efter elementet [BuildingBlocks.](buildingblocks.md) Om elementet inte finns lägger du till det.
1. Leta reda på elementet [ClaimsSchema.](claimsschema.md) Om elementet inte finns lägger du till det.
1. Lägg till stadsanspråket i elementet **ClaimsSchema.**  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Lägga till ett anspråk i användargränssnittet

Följande tekniska profiler är [självpåförda](self-asserted-technical-profile.md), anropas när en användare förväntas ge indata:

- **LocalAccountSignUpWithLogonEmail** - Lokalt kontoupplysningsflöde.
- **Självskyddad-Social** - Federerade konto första gången användaren logga in.
- **Självskyddad profilUppdatera** - Redigera profilflöde.

För att samla in staden anspråk under registreringen, måste `LocalAccountSignUpWithLogonEmail` det läggas till som en utdata anspråk på den tekniska profilen. Åsidosätt den här tekniska profilen i tilläggsfilen. Ange hela listan över utdataanspråk för att styra den ordning som anspråken visas på skärmen. Leta reda på elementet **ClaimsProviders.** Lägg till en ny ClaimsProviders enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Om du vill hämta in stadsanspråket efter första inloggningen med ett federerat konto måste det läggas till som ett utdataanspråk i den `SelfAsserted-Social` tekniska profilen. För lokala och federerade kontoanvändare att kunna redigera sina profildata senare `SelfAsserted-ProfileUpdate` lägger du till utdataanspråket i den tekniska profilen. Åsidosätt dessa tekniska profiler i tilläggsfilen. Ange hela listan över utdataanspråk för att styra den ordning som anspråken visas på skärmen. Leta reda på elementet **ClaimsProviders.** Lägg till en ny ClaimsProviders enligt följande:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Läsa och skriva ett anspråk

Följande tekniska profiler är [tekniska profiler i Active Directory](active-directory-technical-profile.md), som läser och skriver data till Azure Active Directory.  
Används `PersistedClaims` för att skriva data `OutputClaims` till användarprofilen och för att läsa data från användarprofilen i respektive active directory-teknikprofiler.

Åsidosätt dessa tekniska profiler i tilläggsfilen. Leta reda på elementet **ClaimsProviders.**  Lägg till en ny ClaimsProviders enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Inkludera ett anspråk i token 

Om du vill returnera stadsanspråket tillbaka till det <em> `SocialAndLocalAccounts/` </em> förlitande partprogrammet lägger du till ett utdataanspråk i filen. Utdataanspråket läggs till i token efter en lyckad användarresa och skickas till programmet. Ändra det tekniska profilelementet i avsnittet förlitande part för att lägga till staden som ett utdataanspråk.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
3. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
4. Välj **Identity Experience Framework**.
5. Välj **Ladda upp anpassad princip**och ladda sedan upp de två principfiler som du har ändrat.
2. Välj den registrerings- eller inloggningsprincip som du har laddat upp och klicka på knappen **Kör nu.**
3. Du bör kunna registrera dig med en e-postadress.

Registreringsskärmen ska se ut ungefär som följande skärmbild:

![Skärmbild av det ändrade registreringsalternativet](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Token som skickas tillbaka till `city` din ansökan innehåller anspråket.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [elementet ClaimsSchema](claimsschema.md) i IEF-referensen.
- Lär dig hur du [använder anpassade attribut i en anpassad profilredigeringsprincip](custom-policy-custom-attributes.md).
