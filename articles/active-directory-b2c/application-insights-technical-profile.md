---
title: Definiera en teknisk profil för Application Insights i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för Application Insights i en anpassad princip i Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108580"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en teknisk profil för Application Insights i en anpassad Azure AD B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) stöder att skicka händelsedata direkt till [Application Insights](../azure-monitor/app/app-insights-overview.md) med hjälp av instrumenteringsnyckeln som tillhandahålls till Azure AD B2C.  Med en teknisk profil för Application Insights kan du få detaljerade och anpassade händelseloggar för dina användarresor till:

* Få insikter om användarbeteende.
* Felsöka dina egna principer i utveckling eller i produktion.
* Mät prestanda.
* Skapa meddelanden från Application Insights.


## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantarattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen som används av Azure AD B2C for Application Insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas den gemensamma tekniska profilen Application Insights. Andra tekniska profiler för Application Insights inkluderar AzureInsights-Common för att utnyttja konfigurationen.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Ingående anspråk

Elementet **InputClaims** innehåller en lista över anspråk som ska skickas till Application Insights. Du kan också mappa namnet på ditt anspråk till ett namn som du föredrar att visas i Application Insights. I följande exempel visas hur du skickar telemetri till Application Insights. Egenskaper för en händelse läggs `{property:NAME}`till i syntaxen , där NAME läggs till i händelsen. DefaultValue kan vara antingen ett statiskt värde eller ett värde som matchas av en av de [anspråkslösare](claim-resolver-overview.md)som stöds .

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Elementet **InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra indataanspråken eller generera nya innan de skickas till Application Insights.

## <a name="persist-claims"></a>Kvarstår anspråk

Elementet PersistedClaims används inte.

## <a name="output-claims"></a>Utgående fordringar

Elementen OutputClaims och OutputClaimsTransformations används inte.

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet CryptographicKeys används inte.


## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Instrumenteringsnyckel| Ja | [Instrumenteringsnyckeln](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights , som ska användas för att logga händelserna. | 
| UtvecklareMode| Inga | En boolesk som anger om utvecklarläget är aktiverat. Möjliga `true` värden: `false` eller (standard). Dessa metadata styr hur händelser buffras. I en utvecklingsmiljö med minimal händelsevolym resulterar det händelser som skickas direkt till Application Insights när utvecklarläget aktiveras.|  
|InaktiveraLtlemmetri |Inga |En boolesk som anger om telemetri ska aktiveras eller inte. Möjliga `true` värden: `false` eller (standard).| 


## <a name="next-steps"></a>Nästa steg

- [Skapa en application insights-resurs](../azure-monitor/app/create-new-resource.md)
- Lär dig hur du [spårar användarbeteende i Azure Active Directory B2C med application insights](analytics-with-application-insights.md)
