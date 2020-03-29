---
title: Referens för visningskontroll
titleSuffix: Azure AD B2C
description: Referens för Azure AD B2C-visningskontroller. Använd visningskontroller för att anpassa användarresor som definierats i dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188740"
---
# <a name="display-controls"></a>Visa kontroller

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En **bildskärmskontroll** är ett användargränssnittselement som har särskilda funktioner och interagerar med Azure Active Directory B2C(Azure AD B2C) backend-tjänst. Det gör det möjligt för användaren att utföra åtgärder på sidan som anropar en [teknisk profil](validation-technical-profile.md) för validering i serverdelen. Visningskontroller visas på sidan och refereras av en [självpåförd teknisk profil](self-asserted-technical-profile.md).

Följande bild illustrerar en självpåförd registreringssida med två visningskontroller som validerar en primär och sekundär e-postadress.

![Exempelåtergivningad visningskontroll](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Krav

 I avsnittet [Metadata i](self-asserted-technical-profile.md#metadata) en [självförsäkrad teknisk profil](self-asserted-technical-profile.md)måste den refererade [ContentDefinition](contentdefinitions.md) ha `DataUri` angett att sidkontrakt version 2.0.0 eller senare. Ett exempel:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definiera visningskontroller

**DisplayControl-elementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för visningskontrollen. Det kan [refereras](#referencing-display-controls). |
| AnvändareInterfaceControlType | Ja | Typ av displaykontroll. För närvarande stöds är [VerificationControl](display-control-verification.md) |

**DisplayControl-elementet** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** används för att fylla i värdet på de anspråk som ska samlas in från användaren. |
| DisplayClaims | 0:1 | **DisplayClaims** används för att representera anspråk som ska samlas in från användaren. |
| OutputClaims | 0:1 | **OutputClaims** används för att representera anspråk som ska sparas tillfälligt för den här **DisplayControl**. |
| Åtgärder | 0:1 | **Åtgärder** används för att lista de tekniska verifieringsprofilerna som ska anropas för användaråtgärder som inträffar i frontend. |

### <a name="input-claims"></a>Ingående anspråk

I en visningskontroll kan du använda **InputClaims-element** för att fylla i värdet av anspråk som ska samlas in från användaren på sidan. Alla **InputClaimsTransformationer** kan definieras i den självsäkra tekniska profilen som refererar till den här visningskontrollen.

I följande exempel fylls den e-postadress som ska verifieras med den adress som redan finns.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Visa anspråk

Varje typ av visningskontroll kräver en annan uppsättning visningsanspråk, [utdataanspråk](#output-claims)och [åtgärder](#display-control-actions) som ska utföras.

I likhet med de **visningsanspråk** som definieras i en [självpåförd teknisk profil](self-asserted-technical-profile.md#display-claims)representerar visningsanspråken de anspråk som ska samlas in från användaren i visningskontrollen. Det **refererade ClaimType-elementet** måste ange **Elementet UserInputType** för en användarindatatyp som stöds av Azure AD B2C, till exempel `TextBox` eller `DropdownSingleSelect`. Om ett visningsanspråksvärde krävs av en `true` **åtgärd**anger du **attributet Obligatoriskt** för att tvinga användaren att ange ett värde för det specifika visningsanspråket.

Vissa visningsanspråk krävs för vissa typer av bildskärmskontroll. **Verifieringskod** krävs till exempel för visningskontrollen av typen **VerificationControl**. Använd attributet **ControlClaimType** för att ange vilken DisplayClaim som är avsedd för det begärda anspråket. Ett exempel:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Utgående fordringar

**Utdataanspråken för** en visningskontroll skickas inte till nästa orchestration-steg. De sparas tillfälligt endast för den aktuella visningskontrollsessionen. Dessa tillfälliga anspråk kan delas mellan de olika åtgärderna i samma bildskärmskontroll.

Om du vill bubbla upp utdataanspråken till nästa orchestration-steg använder du **OutputClaims** för den faktiska självpåförda tekniska profilen som refererar till den här visningskontrollen.

### <a name="display-control-actions"></a>Visa kontrollåtgärder

**Åtgärderna för** en bildskärmskontroll är procedurer som inträffar i Azure AD B2C-backend när en användare utför en viss åtgärd på klientsidan (webbläsaren). Till exempel de valideringar som ska utföras när användaren väljer en knapp på sidan.

En åtgärd definierar en lista över **tekniska profiler**för validering . De används för att validera vissa eller alla visningsanspråk för bildskärmskontrollen. Den tekniska profilen för validering validerar användarens indata och kan returnera ett fel till användaren. Du kan använda **ContinueOnError,** **ContinueOnSuccess**och **Förutsättningar** i displaykontrollen Åtgärd som liknar det sätt på vilket de används i [valideringstekniska profiler](validation-technical-profile.md) i en självpåförd teknisk profil.

Följande exempel skickar en kod antingen i e-post eller SMS baserat på användarens val av **mfaType-anspråket.**

```XML
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

## <a name="referencing-display-controls"></a>Referera till visningskontroller

Visningskontroller refereras i [visningsanspråken](self-asserted-technical-profile.md#display-claims) för den [självpåstränsa tekniska profilen](self-asserted-technical-profile.md).

Ett exempel:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
