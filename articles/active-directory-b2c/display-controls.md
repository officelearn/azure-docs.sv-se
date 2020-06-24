---
title: Visa kontroll referens
titleSuffix: Azure AD B2C
description: Referens för Azure AD B2C Visa kontroller. Använd visnings kontroller för att anpassa användar resan som definierats i dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 131ecd010cba55f08199f713654792c0844a47e1
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85202304"
---
# <a name="display-controls"></a>Visa kontroller

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En **visnings kontroll** är ett användar gränssnitts element som har särskilda funktioner och som samverkar med backend-tjänsten Azure Active Directory B2C (Azure AD B2C). Det gör att användaren kan utföra åtgärder på sidan som anropar en [teknisk profil för verifiering](validation-technical-profile.md) på Server sidan. Visa kontroller visas på sidan och refereras till av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md).

Följande bild illustrerar en självkontrollerad registrerings sida med två visnings kontroller som verifierar en primär och sekundär e-postadress.

![Exempel på renderad visnings kontroll](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Krav

 I avsnittet [metadata](self-asserted-technical-profile.md#metadata) i en [självkontrollerad teknisk profil](self-asserted-technical-profile.md)måste den refererade [ContentDefinition](contentdefinitions.md) ha `DataUri` ställts in på sid kontrakt version 2.0.0 eller högre. Ett exempel:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definiera visnings kontroller

Elementet **Visa** poster innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Yes | En identifierare som används för visnings kontrollen. Den kan [refereras till](#referencing-display-controls). |
| UserInterfaceControlType | Yes | Typen av visnings kontroll. Stöds för närvarande [VerificationControl](display-control-verification.md) |

Elementet Visa poster innehåller **följande element:**

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** används för att förkonfigurera värdet för de anspråk som ska samlas in från användaren. |
| DisplayClaims | 0:1 | **DisplayClaims** används för att representera anspråk som ska samlas in från användaren. |
| OutputClaims | 0:1 | **OutputClaims** används för att representera anspråk som ska sparas temporärt för **den här**visaren. |
| Åtgärder | 0:1 | **Åtgärder** används för att visa en lista med de tekniska profilerna som ska anropas för användar åtgärder på klient sidan. |

### <a name="input-claims"></a>Inmatade anspråk

I en visnings kontroll kan du använda **InputClaims** -element för att förkonfigurera värdet för anspråk som ska samlas in från användaren på sidan. Alla **InputClaimsTransformations** kan definieras i den självkontrollerade tekniska profilen som refererar till den här visnings kontrollen.

I följande exempel förfylls e-postadressen som ska verifieras med adressen som redan finns.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Visa anspråk

Varje typ av visnings kontroll kräver en annan uppsättning visnings anspråk, [utgående anspråk](#output-claims)och [åtgärder](#display-control-actions) som ska utföras.

På liknande sätt som de **visnings anspråk** som definierats i en [självkontrollerad teknisk profil](self-asserted-technical-profile.md#display-claims)representerar visnings anspråken de anspråk som ska samlas in från användaren i visnings kontrollen. **ClaimType** -elementet som refereras måste ange **UserInputType** -elementet för en indatatyp som stöds av Azure AD B2C, till exempel `TextBox` eller `DropdownSingleSelect` . Om ett visnings anspråks värde krävs av en **åtgärd**anger du det attribut som **krävs** för `true` att tvinga användaren att ange ett värde för det angivna visnings kravet.

Vissa visnings anspråk krävs för vissa typer av visnings kontroll. Till exempel krävs **VerificationCode** för visnings kontrollen av typen **VerificationControl**. Använd attributet **ControlClaimType** för att ange vilken DisplayClaim som är avsedd för det begärda anspråket. Ett exempel:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Utgående anspråk

De **utgående anspråken** för en visnings kontroll skickas inte till nästa Orchestration-steg. De sparas tillfälligt för den aktuella sessionen för bildskärms kontroll. Dessa tillfälliga anspråk kan delas mellan olika åtgärder för samma visnings kontroll.

Om du vill bubbla ut anspråk till nästa Orchestration-steg använder du **OutputClaims** för den faktiska självkontrollerade tekniska profilen som refererar till den här visnings kontrollen.

### <a name="display-control-actions"></a>Visa kontroll åtgärder

**Åtgärder** för en visnings kontroll är procedurer som inträffar i Azure AD B2C Server del när en användare utför en viss åtgärd på klient sidan (webbläsaren). Det kan till exempel vara verifieringar som ska utföras när användaren väljer en knapp på sidan.

En åtgärd definierar en lista över **tekniska verifierings profiler**. De används för att verifiera vissa eller alla visnings anspråk för visnings kontrollen. Den tekniska verifierings profilen verifierar indata från användaren och kan returnera ett fel till användaren. Du kan använda **ContinueOnError**, **ContinueOnSuccess**och **villkor** i visnings kontroll åtgärden som liknar det sätt som de används för att [validera tekniska profiler](validation-technical-profile.md) i en egen kontrollerad teknisk profil.

I följande exempel skickas en kod i e-post eller SMS baserat på användarens val av **mfaType** -anspråket.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Referera till visnings kontroller

Visa kontroller refereras till i [Visa anspråk](self-asserted-technical-profile.md#display-claims) för den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md).

Ett exempel:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
