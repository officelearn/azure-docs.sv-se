---
title: UserJourneys | Microsoft Docs
description: Ange det UserJourneys elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c49ebcf31df950920574af05a9411e463b908bad
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381655"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Resor användaren ange explicita sökvägar som en princip som tillåter ett förlitande partsprogram att hämta önskade anspråk för en användare. Användaren tas via dessa sökvägar för att hämta de anspråk som ska ges till den förlitande parten. Med andra ord definiera användaren resor affärslogiken i vilka en användare går igenom som Azure AD B2C Identitetsramverk processer begäran.

Dessa användare transporter kan ses som en mall som är tillgängliga för att uppfylla core behovet av olika svarande parter i vår community av intresse. Användaren resor underlätta definitionen för den förlitande part-delen av en princip. En princip kan definiera flera användare resor. Varje användarresan är en sekvens med orchestration-steg.

Definiera de användare körningar som stöds av principen, en **UserJourneys** elementet läggs till under det översta elementet i principfilen. 

Den **UserJourneys** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | En användarresa som definierar alla konstruktionerna som krävs för en fullständig användarflödet. | 

Den **UserJourney** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| ID | Ja | En identifierare för en användarresa som kan användas för att referera till den från andra element i principen. Den **DefaultUserJourney** elementet i den [förlitande part princip](relyingparty.md) pekar på det här attributet. |

Den **UserJourney** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | En orchestration-sekvens som måste följas för en lyckad transaktion. Varje användarresa består av en sorterad lista över orchestration-steg som utförs i följd. Om ett steg misslyckas, misslyckas transaktionen. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

En användarresa representeras som en orchestration-sekvens som måste följas för en lyckad transaktion. Om ett steg misslyckas, misslyckas transaktionen. Dessa orchestration-steg referera till båda byggstenarna och Anspråksproviders som tillåts i policyfilen. Orchestration-steg som ansvarar för att visa eller göra en användarupplevelsen har också en referens till motsvarande innehållsdefinition identifierare.

Orchestration-steg kan vara conditionaly ecxetuted, baserat på villkor som definierats i orchestration-steg-elementet. Du kan kontrollera om du vill utföra en orkestreringssteget endast om specifika anspråksproviderförtroendet finns, eller om ett anspråk är lika med eller inte med det angivna värdet för examle. 


Att ange en sorterad lista orchestration-steg, en **OrchestrationSteps** element har lagts till som en del av principen. Det här elementet krävs.

Den **OrchestrationSteps** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | En ordnad orchestration-steg. | 

Den **OrchestrationStep** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| Ordna | Ja | Sorteringen för orchestration-steg. | 
| Typ | Ja | Typ av orchestration-steg. Möjliga värden: <ul><li>**ClaimsProviderSelection** -anger att orkestreringssteget visas olika anspråksleverantörer för användaren att välja en.</li><li>**CombinedSignInAndSignUp** -anger att orkestreringssteget utgör en kombinerad social provider registreringssida för inloggning och lokala konto.</li><li>**ClaimsExchange** -anger att orkestreringssteget utbyter anspråk med en anspråksprovider.</li><li>**SendClaims** -anger att orkestreringssteget skickar anspråk till den förlitande parten med en token utfärdad av en utfärdare av anspråk.</li></ul> | 
| ContentDefinitionReferenceId | Nej | Identifierare för den [innehåll definition](contentdefinitions.md) som är associerade med den här orchestration-steg. Vanligtvis har innehållsdefinition referens-ID definierats i självkontrollerad tekniska profilen. Men finns det tillfällen när Azure AD B2C behöver visa något utan tekniska profil. Det finns två exempel, om typ av orkestreringssteget är en av följande: `ClaimsProviderSelection` eller `CombinedSignInAndSignUp`. Azure AD B2C måste visa identitets-provider-markeringen utan en tekniska profilen. | 
| CpimIssuerTechnicalProfileReferenceId | Nej | Orkestreringssteget är `SendClaims`. Den här egenskapen definierar den tekniska profilen identifieraren för anspråksleverantören som utfärdar en token för den förlitande parten.  Om ej finns, skapas ingen förlitande part-token. |


Den **OrchestrationStep** element kan innehålla följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- | 
| Villkoren | 0: n | En lista över villkor som måste uppfyllas för orchestration-steg att köra. | 
| ClaimsProviderSelections | 0: n | En lista över anspråk providern val för orchestration-steg. | 
| ClaimsExchanges | 0: n | En lista över utbyten av anspråk för orchestration-steg. | 

#### <a name="preconditions"></a>Villkoren

Den **villkoren** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- | 
| Villkor | 0: n | Beroende på den tekniska profilen som används, antingen dirigerar om klienten enligt anspråk providern markeringen eller gör ett server-anrop till exchange-anspråk. | 


##### <a name="precondition"></a>Villkor

Den **Förhandsvillkoret** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| Typ | Ja | Typ av kontroll eller fråga för den här villkor. Värdet kan vara **ClaimsExist**, som anger åtgärderna som ska utföras om de angivna anspråk finns i användarens aktuella anspråksuppsättningen, eller **ClaimEquals**, som anger att åtgärderna ska utföras om det angivna anspråket finns och dess värde är lika med det angivna värdet. |
| ExecuteActionsIf | Ja | Använd ett sant eller falskt-test för att avgöra om åtgärder i villkor som ska utföras. | 

Den **Förhandsvillkoret** element innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Värde | 1: n | En ClaimTypeReferenceId ska efterfrågas för. Ett annat värde element innehåller värdet som ska kontrolleras.</li></ul>|
| Åtgärd | 1:1 | Den åtgärd som ska utföras om villkor som kontrollerar inom en orkestreringssteget är true. Om värdet för den `Action` är inställd på `SkipThisOrchestrationStep`, den associerade `OrchestrationStep` inte ska köras. | 

### <a name="preconditions-examples"></a>Exempel på villkor

Följande villkor kontrollerar om användarens objectId finns. I användarresan, har som användaren valt för att logga in med lokalt konto. Hoppa över det här steget orchestration om objectId finns.

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

Följande villkor kontrollerar om användaren loggat in med ett socialt konto. Ett försök görs att hitta användarkontot i katalogen. Om användaren loggar in eller loggar med ett lokalt konto hoppa över, orchestration-steg.

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

Villkoren kan kontrollera flera villkor. I följande exempel kontrollerar om ”objectId” eller ”e-post finns. Om det första villkoret är sant, hoppar över vägen till nästa orchestration-steg.

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

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Orchestration-steg av typen `ClaimsProviderSelection` eller `CombinedSignInAndSignUp` kan innehålla en lista över Anspråksproviders som en användare kan logga in med. Ordningen på objekten i den `ClaimsProviderSelections` element styr ordningen på de identitetsprovidrar som visas för användaren.

Den **ClaimsProviderSelection** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0: n | Innehåller en lista över Anspråksproviders som kan väljas.|

Den **ClaimsProviderSelection** elementet innehåller följande attribut: 

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nej | Identifierare för anspråksutbytet som körs i nästa steg för dirigering av anspråk providern valet. Det här attributet för attributet ValidationClaimsExchangeId måste anges. | 
| ValidationClaimsExchangeId | Nej | Identifierare för anspråksutbytet som körs i aktuella orchestration-steg för att validera anspråk providern valet. Det här attributet för attributet TargetClaimsExchangeId måste anges. |

### <a name="claimsproviderselection-example"></a>Exempel för ClaimsProviderSelection

I följande orchestration-steg kan användaren välja att logga in med Facebook, LinkIn, Twitter, Google eller ett lokalt konto. Om användaren väljer en av sociala identitetsleverantörer, andra orkestreringssteget körs med den valda anspråk exchange som anges i den `TargetClaimsExchangeId` attribut. Det andra steget i orchestration omdirigeras användaren till den sociala identitetsprovidern att slutföra inloggningen. Om användaren väljer att logga in med det lokala kontot, Azure AD B2C kvar i samma orkestreringssteget (samma registreringssidan eller på inloggningssidan) och hoppar över det andra orchestration-steget.

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

## <a name="claimsexchanges"></a>ClaimsExchanges

Den **ClaimsExchanges** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0: n | Beroende på den tekniska profilen som används, antingen dirigerar om klienten enligt ClaimsProviderSelection som valdes eller gör ett server-anrop till exchange-anspråk. | 

Den **ClaimsExchange** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| ID | Ja | En identifierare för exchange-steg anspråk. Identifieraren används för att referens anspråksutbytet från anspråk providern urval steg i principen. | 
| TechnicalProfileReferenceId | Ja | Identifierare för den tekniska profilen som ska köras. |
 
















