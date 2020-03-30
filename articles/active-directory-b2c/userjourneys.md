---
title: UserJourneys | Microsoft-dokument
description: Ange elementet UserJourneys i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226995"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Användarfärder anger explicita sökvägar genom vilka en princip tillåter ett förlitande part-program att hämta önskade anspråk för en användare. Användaren tas via dessa sökvägar för att hämta de anspråk som ska presenteras för den förlitande parten. Med andra ord definierar användarresor affärslogiken för vad en slutanvändare går igenom när Azure AD B2C Identity Experience Framework behandlar begäran.

Dessa användarresor kan betraktas som mallar tillgängliga för att tillgodose det grundläggande behovet hos de olika beroende parterna i intressegemenskapen. Användarresor underlättar definitionen av den förlitande partens del av en policy. En princip kan definiera flera användarresor. Varje användarresa är en sekvens av orchestration steg.

Om du vill definiera de användarfärder som stöds av principen läggs ett **UserJourneys-element** till under elementet på den översta nivån i principfilen.

Elementet **UserJourneys** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| AnvändareJour | 1:n | En användarresa som definierar alla konstruktioner som krävs för ett fullständigt användarflöde. |

**Elementet UserJourney** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för en användarresa som kan användas för att referera den från andra element i principen. Elementet **DefaultUserJourney** i den [förlitande partiprincipen](relyingparty.md) pekar på det här attributet. |

**Elementet UserJourney** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | En orkestreringssekvens som måste följas upp för en lyckad transaktion. Varje användarfärd består av en ordnad lista över orchestration-steg som körs i följd. Om något steg misslyckas misslyckas transaktionen. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

En användarresa representeras som en orchestration-sekvens som måste följas upp för en lyckad transaktion. Om något steg misslyckas misslyckas transaktionen. Dessa orchestration steg referens både byggstenar och anspråk leverantörer som tillåts i principfilen. Alla orchestration-steg som är ansvariga för att visa eller återge en användarupplevelse har också en referens till motsvarande innehållsdefinitionsidentifierare.

Orchestration-steg kan utföras villkorligt baserat på förkonditioner som definierats i orchestration-stegelementet. Du kan till exempel kontrollera att du bara utför ett orchestration-steg om det finns en viss anspråk eller om ett anspråk är lika med eller inte det angivna värdet.

Om du vill ange den ordnade listan över orchestration-steg läggs ett **OrchestrationSteps-element** till som en del av principen. Det här elementet krävs.

**OrchestrationSteps-elementet** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| OrchestrationStep (OrkestreringSteg) | 1:n | Ett ordnat orkestreringssteg. |

**OrchestrationStep-elementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Order` | Ja | Ordningen på orchestration steg. |
| `Type` | Ja | Typ av orkestreringssteg. Möjliga värden: <ul><li>**ClaimsProviderSelection** - Anger att orchestration-steget presenterar olika anspråksleverantörer för användaren att välja en.</li><li>**CombinedSignInAndSignUp** - Anger att orchestration-steget presenterar en kombinerad inloggning för sociala leverantörer och lokal kontoanpassningssida.</li><li>**ClaimsExchange** - Anger att orchestration-steget utbyter anspråk med en anspråksprovider.</li><li>**GetClaims** - Anger att orchestration-steget läser indataanspråken.</li><li>**SendClaims** - Anger att orchestration-steget skickar anspråken till den förlitande parten med en token som utfärdats av en anspråksutfärdare.</li></ul> |
| InnehållDefinitionReferenceId | Inga | Identifieraren för [innehållsdefinitionen som](contentdefinitions.md) är associerad med det här orchestration-steget. Vanligtvis definieras referensidentifieraren för innehållsdefinition i den självsäkra tekniska profilen. Men det finns vissa fall när Azure AD B2C behöver visa något utan en teknisk profil. Det finns två exempel – om typen av orchestration-steget är ett av följande: `ClaimsProviderSelection` eller `CombinedSignInAndSignUp`måste Azure AD B2C visa valet av identitetsprovider utan att ha en teknisk profil. |
| CpimIssuerTekniskProfileReferenceId | Inga | Typ av orkestreringssteg är `SendClaims`. Den här egenskapen definierar den tekniska profilidentifieraren för anspråksprovidern som utfärdar token för den förlitande parten.  Om det inte finns någon förlitande part token skapas. |


**OrchestrationStep-elementet** kan innehålla följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Förutsättningar | 0:n | En lista över förutsättningar som måste uppfyllas för att orchestration-steget ska kunna köras. |
| FordringarProviderVäljning | 0:n | En lista över anspråksproviderval för orchestration-steget. |
| Skadeväxlingar | 0:n | En lista över anspråksutbyten för orchestration-steget. |

### <a name="preconditions"></a>Förutsättningar

Elementet **Förutsättningar** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Förutsättning | 1:n | Beroende på vilken teknisk profil som används omdirigerar du klienten enligt anspråksleverantörens val eller ringer ett serveranrop för att utbyta anspråk. |


#### <a name="precondition"></a>Förutsättning

Elementet **Förutsättning** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Den typ av check eller fråga som ska utföras för den här förutsättningen. Värdet kan vara **ClaimsExist**, som anger att åtgärderna ska utföras om de angivna anspråken finns i användarens aktuella anspråksuppsättning, eller **ClaimEquals**, som anger att åtgärderna ska utföras om det angivna anspråket finns och dess värde är lika med det angivna värdet. |
| `ExecuteActionsIf` | Ja | Använd ett sant eller falskt test för att avgöra om åtgärderna i förutsättningarna ska utföras. |

**Förkonditionselementen** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Värde | 1:n | En ClaimTypeReferenceId som ska efterfrågas. Ett annat värdeelement innehåller det värde som ska kontrolleras.</li></ul>|
| Åtgärd | 1:1 | Åtgärden som ska utföras om förutsättningskontrollen i ett orchestration-steg är sann. Om värdet för `Action` värdet `SkipThisOrchestrationStep`för värdet `OrchestrationStep` är inställt på ska den associerade inte köras. |

#### <a name="preconditions-examples"></a>Exempel på förutsättningar

Följande förutsättningar kontrollerar om användarens objectId finns. I användarfärden har användaren valt att logga in med lokala konton. Om objectId finns hoppar du över det här orchestration-steget.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Följande förutsättningar kontrollerar om användaren har loggat in med ett socialt konto. Ett försök görs att hitta användarkontot i katalogen. Om användaren loggar in eller registrerar sig med ett lokalt konto hoppar du över det här orchestration-steget.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Förutsättningarna kan kontrollera flera förutsättningar. I följande exempel kontrolleras om det finns "objectId" eller "e-post". Om det första villkoret är sant hoppar resan till nästa orchestration-steg.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>FordringarProviderVäljning

Ett orkestreringssteg av typen `ClaimsProviderSelection` eller `CombinedSignInAndSignUp` kan innehålla en lista över anspråksleverantörer som en användare kan logga in med. Ordningen på elementen `ClaimsProviderSelections` i elementen styr ordningen på de identitetsleverantörer som presenteras för användaren.

Elementet **ClaimsProviderSelections** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| FordringarProviderVäljning | 1:n | Innehåller en lista över anspråksleverantörer som kan väljas.|

Elementet **ClaimsProviderSelections** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DisplayOption (SkärmOption)| Inga | Styr beteendet för ett fall där ett enda anspråksproviderval är tillgängligt. Möjliga värden: `DoNotShowSingleProvider` (standard) omdirigeras användaren omedelbart till den federerade identitetsprovidern. Eller `ShowSingleProvider` Azure AD B2C presenterar inloggningssidan med valet av en identitetsprovider. Om du vill använda det här `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` attributet måste [innehållsdefinitionsversionen](page-layout.md) vara och högre.|

Elementet **ClaimsProviderSelection** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Inga | Identifieraren för anspråksutbytet, som körs i nästa orchestration-steg i anspråksprovidervalet. Det här attributet eller attributet ValidationClaimsExchangeId måste anges, men inte båda. |
| ValideringClaimsExchangeId | Inga | Identifieraren för anspråksutbytet, som körs i det aktuella orchestration-steget för att validera valet av anspråksprovider. Det här attributet eller attributet TargetClaimsExchangeId måste anges, men inte båda. |

### <a name="claimsproviderselection-example"></a>Exempel på anspråksproviderMarkering

I följande orchestration-steg kan användaren välja att logga in med Facebook, LinkedIn, Twitter, Google eller ett lokalt konto. Om användaren väljer en av leverantörerna för social identitet körs det andra orchestration-steget med det valda anspråksutbytet som anges i `TargetClaimsExchangeId` attributet. Det andra orchestration-steget omdirigerar användaren till den sociala identitetsprovidern för att slutföra inloggningsprocessen. Om användaren väljer att logga in med det lokala kontot finns Azure AD B2C kvar på samma orchestration-steg (samma registreringssida eller inloggningssida) och hoppar över det andra orchestration-steget.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>Skadeväxlingar

Elementet **ClaimsExchanges** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Skadebyte | 1:n | Beroende på vilken teknisk profil som används omdirigerar antingen klienten enligt den ClaimsProviderSelection som har valts eller gör ett serveranrop för att utbyta anspråk. |

Elementet **ClaimsExchange** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för steget för anspråksutbyte. Identifieraren används för att referera till anspråksutbytet från ett urvalssteg för anspråksprovider i principen. |
| TechnicalProfileReferenceId | Ja | Identifieraren för den tekniska profil som ska utföras. |
