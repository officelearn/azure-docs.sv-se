---
title: Identitetskrav för Azure azure för hybridmolnidentitetsdesign | Microsoft-dokument
description: Identifiera företagets affärsbehov som leder dig att definiera kraven för hybrididentitetsdesign.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ecc90e13f49c231d8d3ab0cff1de91443b80f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65950894"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Fastställa identitetskrav för din hybrididentitetslösning
Det första steget i utformningen av en hybrid identitetslösning är att fastställa kraven för den affärsorganisation som kommer att utnyttja den här lösningen.  Hybrididentitet startar som en stödjande roll (den stöder alla andra molnlösningar genom att tillhandahålla autentisering) och fortsätter med att tillhandahålla nya och intressanta funktioner som låser upp nya arbetsbelastningar för användare.  Dessa arbetsbelastningar eller tjänster som du vill använda för användarna kommer att diktera kraven för hybrididentitetsdesignen.  Dessa tjänster och arbetsbelastningar måste utnyttja hybrididentitet både lokalt och i molnet.  

Du måste gå igenom dessa viktiga aspekter av verksamheten för att förstå vad det är ett krav nu och vad företaget planerar för framtiden. Om du inte har synligheten för den långsiktiga strategin för hybrididentitetsdesign är chansen stor att din lösning inte kommer att vara skalbar när affärsbehoven växer och förändras. Diagrammet nedan visar ett exempel på en hybrididentitetsarkitektur och de arbetsbelastningar som låses upp för användare. Detta är bara ett exempel på alla nya möjligheter som kan låsas upp och levereras med en solid hybrid identitetsstrategi. 

Vissa komponenter som ingår i ![hybrididentitetshybridens hybrididentitetsarkitekturarkitektur](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Bestäm affärsbehov
Varje företag kommer att ha olika krav, även om dessa företag är en del av samma bransch, kan de verkliga affärskraven variera. Du kan fortfarande utnyttja bästa praxis från branschen, men i slutändan är det företagets affärsbehov som leder dig att definiera kraven för hybrididentitetsdesign. 

Se till att svara på följande frågor för att identifiera dina affärsbehov:

* Är ditt företag ute efter att minska IT driftskostnader?
* Vill ditt företag skydda molntillgångar (SaaS-appar, infrastruktur)?
* Är ditt företag ute efter att modernisera din IT?
  * Är dina användare mer mobila och krävande IT för att skapa undantag i din DMZ för att tillåta olika typer av trafik för att komma åt olika resurser?
  * Har ditt företag äldre appar som behövde publiceras för dessa moderna användare men är inte lätta att skriva om?
  * Behöver ditt företag utföra alla dessa uppgifter och få det under kontroll på samma gång?
* Vill ditt företag skydda användarnas identiteter och minska riskerna genom att ta med nya verktyg som utnyttjar expertisen från Microsofts Azure-säkerhetsexpertis lokalt?
* Är ditt företag försöker bli av med den fruktade "externa" konton på plats och flytta dem till molnet där de inte längre är en vilande hot i din lokala miljö?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analysera lokal identitetsinfrastruktur
Nu när du har en idé om företagets affärsbehov måste du utvärdera din lokala identitetsinfrastruktur. Den här utvärderingen är viktig för att definiera de tekniska kraven för att integrera din nuvarande identitetslösning i molnet identitetshanteringssystem. Se till att svara på följande frågor:

* Vilken autentiserings- och auktoriseringslösning använder ditt företag lokalt? 
* Har ditt företag för närvarande några lokala synkroniseringstjänster?
* Använder ditt företag någon tredjepartsidentitetsleverantörer (IdP)?

Du måste också vara medveten om de molntjänster som ditt företag kan ha. Att göra en bedömning för att förstå den aktuella integrationen med SaaS-, IaaS- eller PaaS-modeller i din miljö är mycket viktigt. Se till att svara på följande frågor under denna bedömning:

* Har ditt företag någon integration med en molntjänstleverantör?
* Om ja, vilka tjänster används?
* Är denna integration för närvarande i produktion eller är det en pilot?

> [!NOTE]
> Cloud Discovery analyserar dina trafikloggar mot Microsoft Cloud App Securitys molnappkatalog med över 16 000 molnappar som rankas och poängsätts baserat på mer än 70 riskfaktorer, för att ge dig kontinuerlig insyn i molnanvändning, Shadow IT och risken shadow IT utgör i din organisation. Kom igång finns [konfigurera Molnidentifiering](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Utvärdera krav på identitetsintegrering
Därefter måste du utvärdera kraven för identitetsintegrering. Den här utvärderingen är viktig för att definiera de tekniska kraven för hur användare ska autentisera, hur organisationens närvaro kommer att se ut i molnet, hur organisationen tillåter auktorisering och vad användarupplevelsen kommer att bli. Se till att svara på följande frågor:

* Kommer din organisation att använda federation, standardautentisering eller båda?
* Är federationen ett krav?  På grund av följande:
  * Kerberos-baserad SSO
  * Ditt företag har ett lokalt program (antingen inbyggda interna eller tredje part) som använder SAML eller liknande federationsfunktioner.
  * MFA via smartkort. RSA SecurID, etc.
  * Regler för klientåtkomst som behandlar frågorna nedan:
    1. Kan jag blockera all extern åtkomst till Office 365 baserat på klientens IP-adress?
    2. Kan jag blockera all extern åtkomst till Office 365, förutom Exchange ActiveSync?
    3. Kan jag blockera all extern åtkomst till Office 365, förutom webbläsarbaserade appar (OWA, SPO)
    4. Kan jag blockera all extern åtkomst till Office 365 för medlemmar i utsedda AD-grupper
* Oro för säkerhet/revision
* Redan befintliga investeringar i federerad autentisering
* Vilket namn kommer vår organisation att använda för vår domän i molnet?
* Har organisationen en anpassad domän?
  1. Är det domän offentligt och lätt kontrollerbart via DNS?
  2. Om så inte är fallet, då har du en allmän egendom som kan användas för att registrera en alternativ UPN i AD?
* Är användaridentifierarna konsekventa för molnrepresentation? 
* Har organisationen appar som kräver integrering med molntjänster?
* Har organisationen flera domäner och kommer de alla att använda standard eller federerad autentisering?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Utvärdera program som körs i din miljö
Nu när du har en uppfattning om din lokala infrastruktur och molninfrastruktur måste du utvärdera de program som körs i dessa miljöer. Den här utvärderingen är viktig för att definiera de tekniska kraven för att integrera dessa program i molnet identitetshanteringssystem. Se till att svara på följande frågor:

* Var kommer våra ansökningar att leva?
* Kommer användarna att komma åt lokala program?  I molnet? Eller både och?
* Finns det planer på att ta befintliga programarbetsbelastningar och flytta dem till molnet?
* Finns det planer på att utveckla nya program som kommer att finnas antingen lokalt eller i molnet som kommer att använda molnautentisering?

## <a name="evaluate-user-requirements"></a>Utvärdera användarkrav
Du måste också utvärdera användarkraven. Den här utvärderingen är viktig för att definiera de steg som kommer att behövas för ombordstigning och hjälpa användare när de övergår till molnet. Se till att svara på följande frågor:

* Kommer användarna att komma åt program lokalt?
* Kommer användarna att komma åt program i molnet?
* Hur loggar användarna vanligtvis in på sin lokala miljö?
* Hur loggar användarna in på molnet?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Bestäm incidentresponskrav](plan-hybrid-identity-design-considerations-incident-response-requirements.md) kommer att gå igenom de tillgängliga alternativen och för-/nackdelarna med varje alternativ.  Genom att ha svarat på dessa frågor väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställ krav på katalogsynkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

