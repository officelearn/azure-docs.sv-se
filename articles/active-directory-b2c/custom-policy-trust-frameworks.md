---
title: Referens - förtroenderamverk i Azure Active Directory B2C | Microsoft-dokument
description: Ett ämne om Azure Active Directory B2C-anpassade principer och Identity Experience Framework.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ffa25eec9c4b668f428d8e8b5a780a5fe4625a2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188893"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiera förtroenderamverk med Azure AD B2C Identity Experience Framework

Azure Active Directory B2C (Azure AD B2C) anpassade principer som använder Identity Experience Framework ger din organisation en centraliserad tjänst. Denna tjänst minskar komplexiteten i identitetsfederation i en stor gemenskap av intresse. Komplexiteten reduceras till en enda förtroenderelation och ett enda metadatautbyte.

Azure AD B2C-anpassade principer använder Identity Experience Framework för att du ska kunna svara på följande frågor:

- Vilka är de rättsliga, säkerhets-, sekretess- och dataskyddspolicyer som måste följas?
- Vilka är kontakterna och vilka är processerna för att bli en ackrediterad deltagare?
- Vilka är de ackrediterade identitetsinformationsleverantörerna (även kallade "skadeleverantörer") och vad erbjuder de?
- Vilka är de ackrediterade förlitande parterna (och eventuellt vad kräver de)?
- Vilka är de tekniska "on the wire"-interoperabilitetskraven för deltagarna?
- Vilka är de operativa "runtime"-reglerna som måste tillämpas för utbyte av digital identitetsinformation?

För att besvara alla dessa frågor använder Azure AD B2C anpassade principer som använder Identity Experience Framework konstruerat För att kunna svara på alla dessa frågor. Låt oss överväga denna konstruktion och vad den ger.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Förstå stiftelsen för förtroenderam och federationshantering

Trust Framework är en skriftlig specifikation av de principer för identitet, säkerhet, sekretess och dataskydd som deltagare i en intressegrupp måste följa.

Federerad identitet ger en grund för att uppnå slutanvändaridentitetsgaranti på Internetskala. Genom att delegera identitetshantering till tredje part kan en enda digital identitet för en slutanvändare återanvändas med flera förlitande parter.

Identitetssäkerhet kräver att identitetsleverantörer och attributleverantörer följer specifika säkerhets-, sekretess- och driftspolicyer och praxis.  Om de inte kan utföra direkta inspektioner måste förlitande parter utveckla förtroenderelationer med de IdPs och AtPs som de väljer att arbeta med.

När antalet konsumenter och leverantörer av digital identitetsinformation ökar är det svårt att fortsätta hantera dessa förtroenderelationer, eller till och med det parvisa utbytet av de tekniska metadata som krävs för nätverksanslutning.  Federationsnav har endast uppnått begränsad framgång när det gäller att lösa dessa problem.

### <a name="what-a-trust-framework-specification-defines"></a>Vad en trust framework-specifikation definierar
TFs är grundbultarna i OIX-modellen (Open Identity Exchange Framework", där varje intressegrupp styrs av en viss TF-specifikation. En sådan TF-specifikation definierar

- **Säkerhets- och sekretessstatistiken för intressegemenskapen med definitionen av:**
    - De försäkringsnivåer som erbjuds/krävs av deltagarna. Till exempel en ordnad uppsättning förtroendeklassificeringar för äktheten av digital identitetsinformation.
    - De skyddsnivåer som erbjuds/krävs av deltagarna. Till exempel en ordnad uppsättning förtroendeklassificeringar för skydd av digital identitetsinformation som hanteras av deltagare i intressegemenskapen.

- **Beskrivningen av den digitala identitetsinformation som erbjuds/krävs av deltagarna**.

- **De tekniska policyerna för produktion och konsumtion av digital identitetsinformation, och därmed för mätning av LOA och LOP. Dessa skriftliga principer innehåller vanligtvis följande kategorier av principer:**
    - Identitetssynsprinciper, till exempel: *Hur starkt granskas en persons identitetsinformation?*
    - Säkerhetsprinciper, till exempel: *Hur starkt skyddas informationsintegriteten och sekretessen?*
    - Sekretesspolicyer, till exempel: *Vilken kontroll har en användare över personlig identifierbar information (PII)*?
    - Överlevnadspolicyer, till exempel: *Om en leverantör upphör med sin verksamhet, hur fungerar kontinuitet och skydd av personuppgifter?*

- **De tekniska profilerna för produktion och konsumtion av digital identitetsinformation. Dessa profiler inkluderar:**
    - Scope-gränssnitt för vilka digital identitetsinformation finns tillgänglig på ett angivet LOA.
    - Tekniska krav för driftskompatibilitet på tråden.

- **Beskrivningarna av de olika roller som deltagarna i communityn kan utföra och de kvalifikationer som krävs för att uppfylla dessa roller.**

Således styr en TF-specifikation hur identitetsinformation utbyts mellan deltagarna i intressegemenskapen: förlitande parter, identitets- och attributleverantörer och attributkontrollanter.

En TF-specifikation är ett eller flera dokument som fungerar som referens för styrningen av intressegemenskapen som reglerar påståendet och konsumtionen av digital identitetsinformation inom samhället. Det är en dokumenterad uppsättning principer och procedurer som utformats för att skapa förtroende för de digitala identiteter som används för onlinetransaktioner mellan medlemmar i en intressegrupp.

Med andra ord definierar en TF-specifikation reglerna för att skapa ett livskraftigt federerat identitetsekosystem för en gemenskap.

För närvarande finns det en utbredd enighet om fördelarna med ett sådant tillvägagångssätt. Det råder ingen tvekan om att förtroende ram specifikationer underlätta utvecklingen av digital identitet ekosystem med kontrollerbara säkerhet, säkerhet och integritet egenskaper, vilket innebär att de kan återanvändas över flera samhällen av intresse.

Därför använder Azure AD B2C-anpassade principer som använder Identity Experience Framework specifikationen som grund för sin datarepresentation för en TF för att underlätta interoperabilitet.

Azure AD B2C Custom-principer som utnyttjar Identity Experience Framework representerar en TF-specifikation som en blandning av data som kan läsas av människor och datorer. Vissa delar av den här modellen (vanligtvis avsnitt som är mer inriktade på styrning) representeras som referenser till publicerad säkerhets- och sekretesspolicydokumentation tillsammans med eventuella relaterade procedurer (om sådana finns). Andra avsnitt beskriver i detalj konfigurationsmetadata och körningsregler som underlättar driftautomatisering.

## <a name="understand-trust-framework-policies"></a>Förstå principer för förtroenderamar

När det gäller implementering består TF-specifikationen av en uppsättning principer som tillåter fullständig kontroll över identitetsbeteenden och upplevelser.  Azure AD B2C anpassade principer som utnyttjar Identity Experience Framework kan du skapa och skapa din egen TF genom sådana deklarativa principer som kan definiera och konfigurera:

- Dokumentreferensen eller referenser som definierar det federerade identitetsekosystemet i samhället som relaterar till TF. De är länkar till TF dokumentation. De (fördefinierade) operativa "runtime"-reglerna eller användarens färder som automatiserar och/eller styr utbytet och användningen av anspråken. Dessa användarresor är associerade med ett LOA (och en LOP). En policy kan därför ha användarresor med varierande loas (och LOPs).

- Identitets- och attributleverantörerna, eller skadeleverantörerna, i intressegemenskapen och de tekniska profiler som de stöder tillsammans med den (out-of-band) LOA/LOP-ackreditering som relaterar till dem.

- Integreringen med attributkontrollanter eller anspråksleverantörer.

- De förlitande parterna i samhället (genom slutledning).

- Metadata för att upprätta nätverkskommunikation mellan deltagarna. Dessa metadata, tillsammans med de tekniska profilerna, används under en transaktion för att lod "på tråden" interoperabilitet mellan den förlitande parten och andra deltagare i samhället.

- Protokollkonverteringen om någon (till exempel SAML 2.0, OAuth2, WS-Federation och OpenID Connect).

- Autentiseringskraven.

- Multifaktoror orchestration om någon.

- Ett delat schema för alla anspråk som är tillgängliga och mappningar till deltagare i en intressegrupp.

- Alla anspråk omvandlingar, tillsammans med möjliga data minimering i detta sammanhang, för att upprätthålla utbyte och användning av fordringar.

- Bindningen och krypteringen.

- Anspråkslagringen.

### <a name="understand-claims"></a>Förstå påståenden

> [!NOTE]
> Vi hänvisar kollektivt till alla möjliga typer av identitetsinformation som kan utbytas som "anspråk": påståenden om en slutanvändares autentiseringsautentiseringsuppgifter, identitetskontroll, kommunikationsenhet, fysisk plats, personligt identifierande attribut, och så vidare.
>
> Vi använder termen "anspråk"- snarare än "attribut"- eftersom i online-transaktioner, dessa dataartefakter är inte fakta som kan verifieras direkt av den förlitande parten. Snarare är de påståenden, eller påståenden, om fakta som den förlitande parten måste utveckla tillräckligt förtroende för att bevilja slutanvändarens begärda transaktion.
>
> Vi använder också termen "anspråk" eftersom Azure AD B2C-anpassade principer som använder Identity Experience Framework är utformade för att förenkla utbytet av alla typer av digital identitetsinformation på ett konsekvent sätt oavsett om det underliggande protokollet är definierats för användarautentisering eller attributhämtning.  På samma sätt använder vi termen "anspråksleverantörer" för att kollektivt hänvisa till identitetsleverantörer, attributleverantörer och attributkontrollanter när vi inte vill skilja mellan deras specifika funktioner.

På så sätt styr de hur identitetsinformation utbyts mellan en förlitande part, identitets- och attributleverantörer och attributkontrollanter. De styr vilka identitets- och attributleverantörer som krävs för en förlitande parts autentisering. De bör betraktas som ett domänspecifikt språk (DSL), det vill säga ett datorspråk som är specialiserat för en viss programdomän med arv, *om* uttalanden, polymorfism.

Dessa principer utgör den maskinläsbara delen av TF-konstruktionen i Azure AD B2C Custom-principer som utnyttjar Identity Experience Framework. De omfattar alla operativa detaljer, inklusive anspråksleverantörers metadata och tekniska profiler, anspråksschemadefinitioner, anspråksomvandlingsfunktioner och användarresor som fylls i för att underlätta operativ orkestrering och automatisering.

De antas vara *levande dokument* eftersom det finns en god chans att deras innehåll kommer att förändras med tiden när det gäller de aktiva deltagare som deklareras i politiken. Det finns också potential att villkoren för att vara deltagare kan ändras.

Federationsinställningar och underhåll förenklas avsevärt genom att skydda förlitande parter från pågående förtroende- och konnektivitetsomkonfigurationer när olika anspråksleverantörer/kontrollanter ansluter sig till eller lämnar (den grupp som representeras av) uppsättningen principer.

Driftskompatibilitet är en annan viktig utmaning. Ytterligare skadereglerare/kontrollanter måste integreras, eftersom det är osannolikt att förlitande parter stöder alla nödvändiga protokoll. Azure AD B2C anpassade principer lösa detta problem genom att stödja branschstandard protokoll och genom att tillämpa specifika användare resor för att överföra begäranden när förlitande parter och attributleverantörer inte stöder samma protokoll.

Användarresor inkluderar protokollprofiler och metadata som används för att lod "på tråd" interoperabilitet mellan den förlitande parten och andra deltagare. Det finns också operativa körningsregler som tillämpas på begäran/svarsmeddelanden för identitetsinformation för att upprätthålla efterlevnad av publicerade principer som en del av TF-specifikationen. Idén om användarresor är nyckeln till anpassning av kundupplevelsen. Det belyser också hur systemet fungerar på protokollnivå.

På grundval av detta kan förlitande part program och portaler, beroende på deras sammanhang, åberopa Azure AD B2C anpassade principer som utnyttjar Identity Experience Framework passerar namnet på en viss princip och få exakt beteende och informationsutbyte de vill utan muss, krångel eller risk.
