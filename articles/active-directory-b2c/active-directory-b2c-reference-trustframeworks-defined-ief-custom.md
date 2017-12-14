---
title: "Azure Active Directory B2C: Referera - förtroende ramverk | Microsoft Docs"
description: Ett avsnitt om Azure Active Directory B2C anpassade principer och identitet upplevelse Framework
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 6dd9039520ec9bfadb358262dad7ebcdb67f6344
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiera förtroende ramverk med Azure AD B2C identitet upplevelse Framework

Azure Active Directory B2C (Azure AD B2C) anpassade principer som använder identitet upplevelse Framework ge din organisation en centraliserad tjänst. Den här tjänsten minskar den identitetsfederation i en stor grupp av intresse. Komplexitet har minskats till ett enda förtroende och en enda metadatautväxling.

Azure AD B2C anpassade principer som använder identitet upplevelse Framework så att du kan besvara följande frågor:

- Vad är juridiska, säkerhet, sekretess och dataskyddsprinciper som måste iakttas?
- Som är kontakter och vad är processer för att bli en auktoriserad deltagare?
- Vilka är de auktoriserad information identitetsleverantörer (även kallat ”Anspråksproviders”) och vad de?
- Vilka är auktoriserad förlitande parter (och eventuellt vad de behöver)?
- Vilka är tekniska ”på kabeln” samverkanskrav för deltagare?
- Vilka är de operativa ”körning” regler som måste tillämpas för att utbyta information digitala identitet?

För att besvara följande frågor, skapa Azure AD B2C anpassade principer som använder identitet upplevelse Framework förtroende Framework (TF). Nu ska vi titta här konstruktionen och den innehåller.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Förstå förtroende Framework och federation management grunden

Förtroende-ramverket är en skriftlig specifikation av identitet, säkerhet, sekretess och dataskyddsprinciper som deltagare i en grupp av intresse måste överensstämma.

Federerade identiteter ger en grund för att uppnå slutanvändarens identitet säkerhet i Internet-skala. En enda digitala identitet för slutanvändaren kan återanvändas med flera förlitande parter genom att delegera Identitetshantering till tredje part.  

Garantier identiteten kräver att identitetsleverantörer (IdPs) och attributet providers (AtPs) följer specifika säkerhet, sekretess och operativa policy och praxis.  Om de inte kan utföra direkt kontroller, måste förlitande parter (RPs) Utveckla förtroenderelationer med IdPs och AtPs företaget väljer att arbeta med.  

Antalet användare och leverantörer av digitala identitetsinformation växer, är det svårt att fortsätta pairwise hantering av dessa förtroenderelationer eller även pairwise utbyte av teknisk metadata som krävs för nätverksanslutning.  Federation NAV har uppnått endast begränsad lyckades lösa dessa problem.

### <a name="what-a-trust-framework-specification-defines"></a>Definierar vad en specifikation av ett förtroende Framework
TFs är linchpins av öppna identitet Exchange (OIX) förtroende Framework modellen, där varje grupp av intresse styrs av en viss TF-specifikation. En sådan specifikation av TF definierar:

- **Säkerhet och sekretess mått för gruppen av intresse med definitionen av:**
    - De säkerhetsnivåer (Kompilera) som erbjuds/krävs av deltagare. till exempel en ordnad uppsättning förtroende klassificeringar för de digitala identitetsinformation.
    - De skyddsnivåer (LOP) som erbjuds/krävs av deltagare. till exempel en ordnad uppsättning förtroende klassificeringar för skydd av digitala identitets-information som hanteras av deltagare i gemenskapen av intresse.

- **Beskrivning av digitala identitets-information som har erbjuds/krävs av deltagare**.

- **Tekniskt principerna för produktion och användning av information om digitala identitet och därmed för att mäta Kompilera och LOP. Principerna skriftliga inkluderar vanligtvis följande typer av principer:**
    - Identitet språkverktyg principer, till exempel: *hur starkt är en persons identitetsinformation granskade?*
    - Säkerhetsprinciper, till exempel: *är hur starkt informationsintegritet och sekretess skyddas?*
    - Sekretesspolicy, till exempel: *vilka en användare är över personligt identifierbar information (PII)*?
    - Överlevnads principer, till exempel: *om en provider upphör operations, hur fungerar verksamhetskontinuitet och skydd av funktionen personligt identifierbar information?*

- **Tekniska profiler för produktion och användning av digital identitetsinformation. Dessa profiler innehåller:**
    - Scope-gränssnitt som digitala identitets-information är tillgänglig vid en angiven Kompilera.
    - Tekniska krav för samverkan under överföring.

- **Beskrivningar av de olika roller som deltagare i gruppen kan utföra och kvalifikationer som krävs för att uppfylla dessa roller.**

Därför en TF specifikation styr hur identitetsinformation utbyts mellan deltagarna i gruppen med intresse: förlitande parter, identitet och attributet providers och verifiering av attribut.

En TF-specifikation som ett eller flera dokument som fungerar som en referens för styrning av gemenskapen intressanta som reglerar kontrollen och förbrukningen av digitala identitetsinformation inom gruppen. Det är en dokumenterade uppsättning principer och procedurer som utformats för att upprätta ett förtroende för digitala identiteter som används för online transaktioner mellan medlemmarna i en grupp av intresse.  

Med andra ord definierar en TF specifikation reglerna för att skapa ett genomförbart federerad identitet ekosystem för en grupp.

Det finns för närvarande omfattande avtalet på fördelen av en sådan metod. Det är säkert att förtroende framework specifikationer underlätta utvecklingen av digitala identitets ekosystem med verifierbara egenskaper för säkerhet, säkerhet och sekretess, vilket innebär att de kan återanvändas i flera grupper av intresse.

För som använder orsak, Azure AD B2C anpassade principer som använder identitet upplevelse Framework specifikationen som bas för dess data representation för en TF för att underlätta samverkan.  

Azure AD B2C anpassade principer som utnyttjar identitet upplevelse Framework representerar en TF-specifikation som en blandning av data och människor. Vissa avsnitt i den här modellen (vanligtvis avsnitt som är mer riktade mot styrning) visas i form av referenser till publicerade säkerhet och sekretess princip dokumentation samt relaterade procedurer (eventuella). Andra avsnitt som beskrivs i detalj i metadata och runtime konfigurationsregler som underlättar operativa automation.

## <a name="understand-trust-framework-policies"></a>Förstå förtroende Framework principer

Vad gäller implementering består specifikationen TF av en uppsättning principer som tillåter fullständig kontroll över identitet beteende och upplevelser.  Azure AD B2C anpassade principer som utnyttjar identitet upplevelse Framework kan du redigera och skapa egna TF via sådana deklarativ principer som kan definiera och konfigurera:

- Dokumentreferens för eller referenser som definierar federerad identitet ekosystemet för gruppen som är kopplad till TF. De är länkar till TF-dokumentationen. (Fördefinierade) operativa ”körning” regler eller användaren-resor att automatisera och/eller kontrollera exchange och användning av anspråk. Dessa användare resor är associerade med en Kompilera (och en LOP). En princip kan därför ha användaren resor med olika LOAs (och LOPs).

- Identitets- och leverantörer eller Anspråksproviders i gemenskapen av intresse och tekniska profiler som de stöder tillsammans med (out-of-band) Kompilera/LOP godkännande som är kopplat till dem.

- Integrering med attributet verifiering eller Anspråksproviders.

- De förlitande parterna i community (genom härledning).

- Metadata för att fastställa nätverkskommunikation mellan deltagare. Dessa metadata tillsammans med teknisk profiler används under en transaktion för att ställa in ”på kabeln” samverkan mellan den förlitande parten och andra community-deltagare.

- Protokollet konverteringen eventuella (till exempel SAML, OAuth2, WS-Federation och OpenID Connect).

- Autentiseringskrav.

- Multifaktor orchestration eventuella.

- Ett delat schema för alla anspråk som är tillgängliga och att deltagarna i en grupp av intresse.

- Alla anspråksomvandlingar, tillsammans med möjliga data minimering i den här kontexten för att upprätthålla exchange och användning av anspråk.

- Bindningen och kryptering.

- Anspråk lagring.

### <a name="understand-claims"></a>Förstå anspråk

> [!NOTE]
> Vi gemensamt refererar till alla möjliga typer av identitetsinformation som kan utväxlas som ”anspråk”: anspråk om en slutanvändare autentisering autentiseringsuppgifter identitet de prövas kommunikation enheten fysisk placering, personligt identifierar attribut, och så vidare.  
>
> Vi använder termen ”anspråk”--i stället för ”attribut”--eftersom online transaktioner dessa data artefakter inte fakta som direkt kan verifieras av den förlitande parten. De är i stället intyg eller anspråk om fakta som den förlitande parten måste utveckla tillräcklig förtroende för att bevilja användaren begärda transaktionen.  
>
> Vi använder termen ”anspråk” också att Azure AD B2C anpassade principer som använder identitet upplevelse ramen är avsedda att förenkla utbyte av alla typer av digitala identitets-information på ett konsekvent sätt oavsett om det underliggande protokollet har definierats för autentisering av användare eller attributhämtningen.  På samma sätt kan vi använder termen ”Anspråksproviders” att gemensamt referera till identitetsleverantörer, attributet providers och verifiering av attributet när vi inte vill att skilja mellan funktionerna.   

Därför styr de hur identitetsinformation utbyts mellan en förlitande part, identitet och attributet providers och verifiering av attribut. De styr vilka identitet och attributet providers krävs för autentisering för en förlitande part. De bör betraktas som ett domänspecifika språk (DSL), som är ett datorspråk som har specialiserat för en viss programdomänen med arv, *om* instruktioner, polymorfism.

Dessa principer utgöra maskinläsbar delen av TF konstruktion i Azure AD B2C anpassade principer utnyttja ramverket identitet upplevelse. De omfattar alla Driftinformation, inklusive Anspråksproviders metadata och tekniska profiler, schemadefinitioner för anspråk, funktioner för omvandling av anspråk och användaren resor som fylls i för att underlätta operativa orchestration och automatisering.  

De antas vara *levande dokument* eftersom det finns en bra chans att innehållet kommer att ändras över tiden om aktiva deltagarna har deklarerats i principerna. Det finns risk att de allmänna villkoren för att vara en deltagare kan ändras.  

Federation-konfiguration och underhåll frågeprestanda avsevärt förenklas genom avskärmning förlitande parter från pågående förtroende och anslutningen reconfigurations som providers/verifiering av olika anspråk gå med i eller lämna (gemenskapen som representeras av) uppsättning principer.

Driftskompatibilitet är ett annat betydande utmaning. Vara måste integrerade providers/verifiering av ytterligare anspråk eftersom förlitande parter är inte troligt att stödja alla protokoll. Azure AD B2C anpassade principer lösa detta problem genom att stödja branschstandardprotokoll och genom att använda specifika användare resor att transponera begäranden när attributet providers och förlitande parter inte stöder samma protokoll.  

Användaren resor omfatta protokollet profiler och metadata som används för att ställa in ”på kabeln” samverkan mellan den förlitande parten och andra deltagare. Det finns också operativa runtime-regler som tillämpas på identitet information exchange begäran och svar-meddelanden för att tillämpa kompatibilitet med publicerade principer som en del av specifikationen TF. Uppfattning om användaren resor är nyckeln till anpassning i användarupplevelsen. Det kan också sheds ljus på hur systemets fungerar på protokollnivå.

På grundval kan, förlitande parters program och portaler beroende på deras kontexten anropa Azure AD B2C anpassade principer som utnyttjar identitet upplevelse ramen skicka namnet på en specifik princip och hämta exakt beteende och information om exchange de vill utan helst, ansträngning eller risk.
