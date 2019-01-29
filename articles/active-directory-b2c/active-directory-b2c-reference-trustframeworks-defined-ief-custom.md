---
title: Referens – förtroende ramverk i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt om Azure Active Directory B2C anpassade principer och den Identitetsramverk.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8debab073d65cd64303d2f395b73a3a04e8105b6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166874"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiera förtroende ramverk med Azure AD B2C för Identitetsupplevelse

Azure Active Directory B2C (Azure AD B2C) anpassade principer som använder den Identitetsramverk ge organisationen en centraliserad tjänst. Den här tjänsten minskar komplexiteten med identitetsfederation i många av intresse. Komplexiteten minskar till en enda förtroenderelation och en enkel metadata-exchange.

Azure AD B2C anpassade principer som använder den Identitetsramverk att besvara följande frågor:

- Vad är juridisk information, säkerhet, sekretess och principerna för dataskydd som ska efterföljas?
- Vem är kontakter och vilka är processerna för att bli en auktoriserad deltagare?
- Vilka är de ackrediterade information identitetsleverantörer (även kallat ”Anspråksproviders”) och vad de?
- Vilka är de ackrediterade förlitande parterna (och du kan också vad de behöver)?
- Vilka är tekniska ”på kabeln” samverkanskrav för deltagare?
- Vilka är de operativa ”runtime”-regler som måste tillämpas för utbyte av digitala ID-information?

För att besvara dessa frågor, skapa anpassade Azure AD B2C-principer som använder Identitetsramverk förtroende Framework (TF). Anta att du har den här konstruktion och vad det gör.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Förstå åtkomsthanteringen förtroende Framework och federation

Förtroende-ramverket är en skriftliga specifikation av identitet, säkerhet, sekretess och principerna för dataskydd som deltagare i en grupp av intresse måste överensstämma.

Federerad identitet ger en grund för att uppnå slutanvändarens identitet assurance vid internetskalning. Genom att delegera Identitetshantering till tredje part kan kan en enda digitala identitet för en slutanvändare återanvändas med flera förlitande parter.  

Identitet assurance kräver att Identitetsproviders (IDP: er) och attributet providers (AtPs) följer specifika säkerhet, sekretess, och operativa riktlinjer och metoder.  Om de inte kan utföra direkt inspektioner, måste förlitande part (RPs) Utveckla förtroenderelationer med IDP: er och AtPs de väljer att arbeta med.  

När antalet konsumenter och leverantörer av digitala ID-information växer, är det svårt att fortsätta pairwise hanteringen av dessa förtroenderelationer eller även pairwise utbyte av teknisk metadata som krävs för nätverksanslutning.  Federation hubs har uppnått endast begränsad lyckades lösa dessa problem.

### <a name="what-a-trust-framework-specification-defines"></a>Definierar vad en förtroende Framework-specifikation
TFs är linchpins av den öppna identitet Exchange (OIX) lita Framework-modellen, där varje SNMP-grupp intressanta styrs av en viss TF-specifikation. En sådan TF-specifikation definierar:

- **Säkerhet och sekretess mått för community i närheten med definitionen av:**
    - De försäkringsnivåer (Kompilera) som erbjuds/krävs av deltagare. till exempel en ordnad uppsättning förtroende klassificeringar för de digitala ID-information.
    - De skyddsnivåer (LOP) som erbjuds/krävs av deltagare. till exempel en ordnad uppsättning förtroende klassificeringar för skydd av digitala ID-information som hanteras av deltagare i diskussionsgruppen av intresse.

- **Beskrivning av digitala ID-information som har erbjuds/krävs av deltagare**.

- **De tekniska principerna för produktion och förbrukning av digitala ID-information och därmed för att mäta Kompilera och LOP. Dessa skriftliga principer omfattar vanligtvis följande typer av principer:**
    - Identitet språkverktyg principer, till exempel: *Hur starkt är en persons identitetsinformation som du vet att dess?*
    - Säkerhetsprinciper, till exempel: *Hur starkt skyddas information och sekretess?*
    - Sekretessprinciper, till exempel: *Vilka kontroll en användare har över personligt identifierbar information (PII)*?
    - Överlevnads principer, till exempel: *Om en provider upphör åtgärder, hur fungerar och skydd av personligt identifierbar information funktion?*

- **Tekniska profiler för produktion och förbrukning av digitala ID-information. Dessa profiler innehåller:**
    - Scope-gränssnitt som digitala ID-information är tillgänglig på en angiven Kompilera.
    - Tekniska krav för samverkan ledare.

- **Beskrivningar av de olika roller som deltagare i gruppen kan utföra och kvalifikationerna som krävs för att uppfylla dessa roller.**

Därför en TF-specifikation reglerar hur identitetsinformation utbyts mellan deltagarna i diskussionsgruppen intressanta: förlitande parter, identitet och attributet leverantörer och verifiering av attribut.

En TF-specifikation som ett eller flera dokument som fungerar som en referens för styrning av communityn av intresse som reglerar kontrollen och förbrukning av digitala ID-information inom gruppen. Det är en dokumenterad uppsättning principer och procedurer som utformats för att upprätta ett förtroende för de digitala identiteter som används för onlinetransaktioner mellan medlemmarna i en grupp av intresse.  

Med andra ord definierar en TF-specifikation reglerna för att skapa ett genomförbart federerad identitet ekosystem för en grupp.

Det finns för närvarande utökas avtalet om fördelen av en sådan metod. Det är säkert att förtroende framework specifikationer underlätta utvecklingen av digitala identitets-ekosystem med verifierbart egenskaper för säkerhet, säkerhet och sekretess, vilket innebär att de kan återanvändas i flera grupper av intresse.

För som använder orsak, Azure AD B2C anpassade principer som använder den Identitetsramverk specifikationen som bas för dess datarepresentation för en TF för att underlätta samverkan.  

Azure AD B2C anpassade principer som utnyttjar den Identitetsramverk representerar en TF-specifikation som en blandning av data och människor. Vissa delar av den här modellen (vanligtvis avsnitten som är mer riktade mot styrning) representeras som referenser till publicerade säkerhet och sekretess dokumentation om policy tillsammans med följande relaterade (om sådan finns). Andra avsnitt som beskrivs i detalj metadata och runtime konfigurationsregler som underlättar operativa automation.

## <a name="understand-trust-framework-policies"></a>Förstå förtroende Framework principer

Vad gäller implementering består TF-specifikationen av en uppsättning principer som tillåter fullständig kontroll över identitet beteende och upplevelser.  Azure AD B2C anpassade principer som utnyttjar den Identitetsramverk kan du redigera och skapa dina egna TF via deklarativa principer som kan definiera och konfigurera:

- Dokumentreferens för eller referenser som definierar federerad identitet ekosystem med communityn som relaterar till TF. De är länkar till TF-dokumentationen. (Fördefinierade) operativa ”runtime”-regler eller de körningar som användare att automatisera och/eller kontrollera exchange och användningen av anspråk. Dessa användare transporter är associerade med en Kompilera (och en LOP). En princip kan därför ha användare resor med olika LOAs (och LOPs).

- Identitets- och attributet-leverantörer eller Anspråksproviders i communityn Orienteringspunkter och de tekniska profiler som de stöder tillsammans med (out-of-band) Kompilera/LOP ackreditering som relaterar till dem.

- Integrering med verifiering av attributet eller Anspråksproviders.

- Förlitande parter i gemenskapen (av inferens).

- Metadata för att upprätta kommunikation mellan deltagare. Dessa metadata tillsammans med tekniska profiler används under en transaktion för att ställa in ”på kabeln” samverkan mellan den förlitande parten och andra community-deltagare.

- Protokollet konverteringen eventuellt (till exempel SAML 2.0, OAuth2, WS-Federation och OpenID Connect).

- Autentiseringskrav.

- Multifaktor orchestration eventuellt.

- Ett delat schema för alla anspråk som är tillgängliga och mappningar till deltagare i en grupp av intresse.

- Alla anspråksomvandlingar, tillsammans med möjliga data minimering i den här kontexten för hantering av exchange och användning av anspråk.

- Bindnings- och kryptering.

- Anspråk-lagring.

### <a name="understand-claims"></a>Förstå anspråk

> [!NOTE]
> Vi gemensamt refererar till alla möjliga typer av identitetsinformation som kan utväxlas som ”anspråk”: anspråk om en slutanvändare autentiseringsbehörighet, identity veta, kommunikationsenhet, fysisk placering, personligt identifieringsattribut, och så vidare.  
>
> Vi använder termen ”anspråk”--i stället för ”attribut”--eftersom online transaktioner dessa data artefakter inte fakta som kan verifieras direkt av den förlitande parten. De är i stället intyg eller anspråk om fakta som måste den förlitande parten utveckla tillräcklig trygghet för att bevilja slutanvändarens transaktions.  
>
> Vi använder också termen ”anspråk” eftersom Azure AD B2C anpassade principer som använder den Identitetsramverk är utformade för att förenkla utbyte av alla typer av digitala ID-information på ett konsekvent sätt oavsett om det underliggande protokollet är definierats för användaren autentisering eller attributet hämtning.  På samma sätt kan vi använder termen ”Anspråksproviders” att sammantaget referera till identitetsleverantörer, attribut-providers och verifiering av attribut när vi inte vill skilja mellan funktionerna.   

Därför styr de hur identitetsinformation utbyts mellan en förlitande part, identitet och attributet leverantörer och verifiering av attribut. De kontrollerar vilken identitet och attributet providers krävs för en förlitande part autentisering. De bör betraktas som ett domänspecifika språk (DSL), det vill säga ett datorspråk som har specialiserats för en specifik programdomän med arv, *om* instruktioner, polymorfism.

Dessa principer utgör maskinläsbara delen av TF-konstruktion i Azure AD B2C anpassade principer för att utnyttja den Identitetsramverk. De omfattar alla operativa information, inklusive Anspråksproviders metadata och tekniska profiler, anspråk schemadefinitioner, funktioner för omvandling av anspråk och användaren utbildning som fylls i för att underlätta operativa orkestrering och automatisering.  

Antas vara *levande dokument* eftersom det finns en bra möjlighet att deras innehåll kommer att ändras över tid om aktiva deltagare som har deklarerats i principerna. Det finns också risk att de allmänna villkoren för att du är en deltagare som kan ändras.  

Federation-konfiguration och underhåll avsevärt förenklad av avskärmning förlitande parter från pågående förtroende och anslutning reconfigurations som olika anspråk providers/verifiering gå med i eller lämna (community som representeras av) uppsättning principer.

Samverkan är en annan stor utmaning. Vara måste integrerade ytterligare anspråk providers/verifiering eftersom förlitande parter inte är troligt att stödja alla nödvändiga protokoll. Azure AD B2C anpassade principer lösa detta problem genom att stödja branschstandardprotokollen och genom att använda specifika användare utbildning för att transponera begäranden när förlitande parter och leverantörer av attributet inte stöder samma protokoll.  

Användaren resor är protokoll-profiler och metadata som används för att ställa in ”på kabeln” samverkan mellan den förlitande parten och andra deltagare. Det finns också operativa runtime-regler som tillämpas på identitet information exchange begäranden/svarsmeddelanden för att genomdriva efterlevnad av de publicerade principer som en del av TF-specifikationen. Uppfattning om användaren resor är nyckeln till anpassning av kundupplevelsen. Den också belyses hur systemet fungerar på protokollnivån.

På grundval kan, förlitande partsprogram och portaler beroende på deras kontext anropa Azure AD B2C anpassade principer som utnyttjar Identitetsramverk skicka namnet på en specifik princip och få exakt beteende och informationsutbyte de vill utan helst, krångel eller risk.
