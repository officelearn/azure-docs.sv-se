---
title: Referens – förtroende ramverk i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt om Azure Active Directory B2C anpassade principer och ramverket för identitets upplevelse.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d29bd05f67d00047499dc256e5e1a82f98693a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990991"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiera förtroende ramverk med Azure AD B2C Identity Experience Framework

Azure Active Directory B2C (Azure AD B2C) anpassade principer som använder identitets miljö ramverket ger din organisation en centraliserad tjänst. Den här tjänsten minskar komplexiteten i identitets federationen i en stor grupp av intresse. Komplexiteten minskas till en enda förtroende relation och ett utbyte av en enda metadata.

I Azure AD B2C anpassade principer används identitets miljö ramverket för att ge dig svar på följande frågor:

- Vilka är de juridiska, säkerhets-, sekretess-och data skydds principer som måste följas?
- Vilka är kontakterna och vilka är processerna för att bli en ackrediterad deltagare?
- Vilka är ackrediterade identitets informations leverantörer (kallas även "anspråks leverantörer") och vad erbjuder de?
- Vilka är de ackrediterade förlitande parterna (och eventuellt vad krävs)?
- Vilka är de tekniska kraven på samverkans krav för deltagare?
- Vilka är drift körnings regler som måste framtvingas för utbyte av digital identitets information?

Om du vill besvara alla dessa frågor, Azure AD B2C anpassade principer som använder sig av identitets miljö ramverket, använder du konstruktionen Trust Framework (TF). Nu ska vi ta en titt på den här konstruktionen och vad den erbjuder.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Förstå Trust Framework och Federation Management Foundation

Trust Framework är en skriftlig specifikation av identitet, säkerhet, sekretess och data skydds principer som deltagarna i en gemenskap av intresse måste uppfylla.

Federerade identiteter ger en grund för att uppnå identitets garanti för slutanvändare i Internet. Genom att delegera identitets hantering till tredje part kan en enda digital identitet för en slutanvändare återanvändas med flera förlitande parter.

Identitets garantier kräver att identitets leverantörer (IDP: er) och AtPs (Attribute providers) följer vissa principer och metoder för säkerhet, sekretess och drift.  Om de inte kan utföra direkta kontroller måste förlitande parter (RPs) utveckla förtroende relationer med IDP: er och AtPs de väljer att arbeta med.

Eftersom antalet konsumenter och leverantörer av Digital Identity-information växer, är det svårt att fortsätta använda den för att hantera dessa förtroende relationer, eller till och med det bislagna utbytet av de tekniska metadata som krävs för nätverks anslutningen.  Federations nav har uppnått begränsad framgång för att lösa dessa problem.

### <a name="what-a-trust-framework-specification-defines"></a>Vad en Trust Framework-specifikation definierar
TFs är linchpins för ramverket med öppen Identity Exchange (OIX) för förtroende Framework, där varje gemenskap av intresse regleras av en viss TF-specifikation. En sådan TF-specifikation definierar:

- **Mått för säkerhet och sekretess för gemenskapen av intresse med definitionen av:**
    - Nivåerna av säkerhet (LOA) som erbjuds/krävs av deltagarna, till exempel en ordnad uppsättning förtroende klassificering för äktheten av digital identitets information.
    - De skydds nivåer (LOP) som erbjuds/krävs av deltagarna. till exempel en ordnad uppsättning förtroende klassificeringar för skydd av digital identitets information som hanteras av deltagare i intresse samhället.

- **Beskrivningen av den digitala identitets information som erbjuds/krävs av deltagare**.

- **De tekniska principerna för produktion och konsumtion av digital identitets information och därmed för att mäta LOA och LOP. De här skrivna principerna omfattar vanligt vis följande typer av principer:**
    - Principer för identitets bevisering, till exempel: *hur starkt är en persons identitets information testats?*
    - Säkerhets principer, till exempel: *hur starkt är informations integritet och konfidentialitet skyddad?*
    - Sekretess principer, till exempel: *vilken kontroll har en användare via personligt identifierbar information (PII)*?
    - Policyer för stöldskydd, till exempel: *om en provider upphör att fungera, hur fungerar kontinuitet och skydd av PII-funktionen?*

- **De tekniska profilerna för produktion och konsumtion av digital identitets information. De här profilerna är:**
    - Omfattnings gränssnitt för vilka Digital identitets information är tillgänglig vid en angiven LOA.
    - Tekniska krav för samverkan med högsta möjliga samverkan.

- **Beskrivningarna av de olika rollerna som deltagarna i communityn kan utföra och de kvalifikationer som krävs för att uppfylla dessa roller.**

En TF-specifikation styr alltså hur identitets information utbyts mellan deltagarna i intresset: förlitande parter, identitets-och attribut-providers och attribut verifierare.

En TF-specifikation är ett eller flera dokument som fungerar som en referens för styrning av den intresse grupp som reglerar ansvars kontrollen och konsumtionen av digital identitets information i communityn. Det är en dokumenterad uppsättning principer och procedurer som utformats för att upprätta förtroende i de digitala identiteter som används för onlinetransaktioner mellan medlemmar i en community av intresse.

Med andra ord definierar en TF-specifikation reglerna för att skapa ett livskraftigt eko system med federerade identiteter för en community.

För närvarande finns det omfattande avtal om fördelarna med en sådan metod. Det finns inget tvivel om att Trust Framework-specifikationer fören klar utvecklingen av digitala identitets eko system med verifierbar säkerhet, säkerhet och sekretess, vilket innebär att de kan återanvändas i flera intresse grupper.

Av den anledningen använder Azure AD B2C anpassade principer som använder sig av identitets Miljös ramverket specifikationen som grund för data representationen för att göra det lättare att samverka.

Azure AD B2C anpassade principer som utnyttjar identitets miljö ramverket representerar en TF-specifikation som en blandning av mänsklig och maskinläsbar data. Vissa avsnitt i den här modellen (vanligt vis avsnitt som är mer orienterade mot styrning) representeras som referenser till publicerade säkerhets-och sekretess principer, tillsammans med relaterade procedurer (om sådana finns). Andra avsnitt beskriver i detalj vilka konfigurations-och körnings regler som underlättar drift automatisering.

## <a name="understand-trust-framework-policies"></a>Förstå principer för förtroende ramverk

Vid implementering består specifikationen av en uppsättning principer som ger fullständig kontroll över identitets beteenden och upplevelser.  Azure AD B2C anpassade principer som utnyttjar identitets miljö ramverket kan du skapa och skapa egna TF genom sådana deklarativ principer som kan definiera och konfigurera:

- Dokument referensen eller referenserna som definierar eko systemet med federerade identiteter för den community som relaterar till TF. De är länkar till TF-dokumentationen. De (fördefinierade) operativa "runtime"-reglerna eller användaren befärdar som automatiserar och/eller styr Exchange och användning av anspråken. Dessa användar resor är associerade med en LOA (och en LOP). En princip kan därför ha användar transporter med varierande LOAs (och LOPs).

- Providern för identitet och attribut, eller anspråks leverantörer, i gemenskapen av intresse och de tekniska profiler som de stöder tillsammans med den (out-of-band) LOA/LOP-ackreditering som är relaterad till dem.

- Integrering med attribut som verifierare eller anspråks leverantörer.

- De förlitande parterna i communityn (efter härledning).

- Metadata för att upprätta nätverkskommunikation mellan deltagare. Dessa metadata, tillsammans med de tekniska profilerna, används under en transaktion för att ansluta till "på kabel"-samverkan mellan den förlitande parten och andra Community-deltagare.

- Protokoll konverteringen om några (till exempel SAML 2,0, OAuth2, WS-Federation och OpenID Connect).

- Autentiseringskrav.

- Multifakta-Orchestration om det finns någon.

- Ett delat schema för alla anspråk som är tillgängliga och mappar till deltagare i en community av intresse.

- Alla anspråks omvandlingar, tillsammans med möjliga data minimering i detta sammanhang, för att bibehålla utbytet och användningen av anspråken.

- Bindning och kryptering.

- Anspråks lagringen.

### <a name="understand-claims"></a>Förstå anspråk

> [!NOTE]
> Vi refererar gemensamt till alla möjliga typer av identitets information som kan bytas ut mot "anspråk": anspråk på en slutanvändares autentiseringsuppgifter för autentisering, identitet först konsumentsajter, kommunikations enhet, fysisk plats, personligt identifierande attribut och så vidare.
>
> Vi använder termen "anspråk", i stället för "attribut", eftersom dessa data artefakter inte är fakta som kan verifieras direkt av den förlitande parten i onlinetransaktioner. De är hellre, eller anspråk, om fakta som den förlitande parten måste utveckla tillräckligt trygg för att ge slutanvändarens begärda transaktion.
>
> Vi använder också termen "anspråk" eftersom Azure AD B2C anpassade principer som använder sig av identitets miljö ramverket är utformade för att förenkla utbytet av alla typer av digital identitets information på ett konsekvent sätt oavsett om det underliggande protokollet har definierats för användarautentisering eller hämtning av attribut.  På samma sätt använder vi termen "anspråks leverantörer" för att se identitets leverantörer, attribut leverantörer och attribut verifierare när vi inte vill skilja mellan sina olika funktioner.

De styr därför hur identitets information utbyts mellan en förlitande part, identitet och attribut-providers och attribut verifierare. De styr vilka identitets-och attribut leverantörer som krävs för en förlitande parts autentisering. De bör betraktas som ett domänbaserat språk (DSL), det vill säga ett dator språk som är specialiserat för en viss program domän med arv, *om* -instruktioner, polymorfism.

Dessa principer utgör den maskinläsbara delen av TF-konstruktionen i Azure AD B2C anpassade principer som använder sig av identitets miljö ramverket. De innehåller all drift information, inklusive anspråks leverantörers metadata och tekniska profiler, anspråk schema definitioner, anspråks omvandlings funktioner och användar resor som är fyllda för att under lätta drifts dirigering och automatisering.

De antas vara *levande dokument* eftersom det är en chans att deras innehåll ändras med tiden för de aktiva deltagare som har deklarerats i principerna. Det är också möjligt att villkoren för att bli deltagare kan ändras.

Federations installation och underhåll är avsevärt förenklat av skärmade förlitande parter från pågående förtroende och anslutnings omkonfigurationer som olika anspråks leverantörer/verifierare, JOIN eller tjänstledighet (communityn representeras av) uppsättningen med principer.

Samverkan är en annan viktig utmaning. Ytterligare anspråks leverantörer/verifierare måste integreras eftersom förlitande parter inte stöder alla nödvändiga protokoll. Azure AD B2C anpassade principer löser problemet genom att stödja bransch standard protokoll och genom att använda vissa användar resor för att transponera begär anden när förlitande parter och attribut för attribut inte stöder samma protokoll.

Användar resan inkluderar protokoll profiler och metadata som används för att ansluta "på kabel" mellan den förlitande parten och andra deltagare. Det finns också operativa körnings regler som tillämpas på identitets information Exchange-begäran/svarsmeddelanden för tvingande av efterlevnad med publicerade principer som en del av TF-specifikationen. Idén med användar resan är en nyckel till anpassningen av kund upplevelsen. Den försätter också ljuset av hur systemet fungerar på protokoll nivå.

På detta sätt kan förlitande part-program och portaler, beroende på deras kontext, anropa Azure AD B2C anpassade principer som använder sig av identitets miljö ramverket som skickar namnet på en princip och ger exakt det beteende och det informations utbyte som de vill ha utan någon Muss, en eller en risk.
