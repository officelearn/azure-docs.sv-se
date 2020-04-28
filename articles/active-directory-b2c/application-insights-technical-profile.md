---
title: Definiera en Application Insights teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en Application Insights teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80108580"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en Application Insights teknisk profil i en Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) stöder sändning av händelse data direkt till [Application Insights](../azure-monitor/app/app-insights-overview.md) med hjälp av Instrumentation-nyckeln som är avsedd att Azure AD B2C.  Med en Application Insights teknisk profil kan du hämta detaljerade och anpassade händelse loggar för dina användar resor till:

* Få insikter om användar beteende.
* Felsök dina egna principer i utvecklingen eller i produktionen.
* Mät prestanda.
* Skapa meddelanden från Application Insights.


## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterarens sammansättning som används av Azure AD B2C för Application Insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas den vanliga Application Insights tekniska profilen. Andra Application Insights tekniska profiler är AzureInsights-common för att utnyttja konfigurationen.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Application Insights. Du kan också mappa namnet på anspråket till ett namn som du vill ska visas i Application Insights. I följande exempel visas hur du skickar telemetrivärden till Application Insights. Egenskaperna för en händelse läggs till via syntaxen `{property:NAME}`där namn är egenskapen som läggs till i händelsen. DefaultValue kan vara antingen ett statiskt värde eller ett värde som har lösts av en av de [anspråk matchare](claim-resolver-overview.md)som stöds.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till Application Insights.

## <a name="persist-claims"></a>Kvarhåll anspråk

PersistedClaims-elementet används inte.

## <a name="output-claims"></a>Utgående anspråk

OutputClaims-och OutputClaimsTransformations-elementen används inte.

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

CryptographicKeys-elementet används inte.


## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| InstrumentationKey| Ja | Application Insights [Instrumentation-nyckeln](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)som används för att logga händelser. | 
| DeveloperMode| Inga | Ett booleskt värde som anger om utvecklarläge är aktiverat. Möjliga värden: `true` eller `false` (standard). Dessa metadata styr hur händelser buffras. I en utvecklings miljö med minimal händelse volym aktiverar du utvecklarläge om du vill att händelser skickas direkt till Application Insights.|  
|DisableTelemetry |Inga |Ett booleskt värde som anger om telemetri ska vara aktiverat eller inte. Möjliga värden: `true` eller `false` (standard).| 


## <a name="next-steps"></a>Nästa steg

- [Skapa en Application Insights resurs](../azure-monitor/app/create-new-resource.md)
- Lär dig hur du [spårar användar beteende i Azure Active Directory B2C att använda Application Insights](analytics-with-application-insights.md)
