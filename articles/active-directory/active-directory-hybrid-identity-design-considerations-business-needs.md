---
title: Identitetskrav för hybrid cloud identitet design Azure | Microsoft Docs
description: Identifiera företagets affärsbehov som leder dig att fastställa krav för hybrid identity-design.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5741a5024b5f5105a71d9404191601b951a301e4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Ange identitetskrav för för din hybrididentitetslösning
Det första steget i utforma en hybrididentitetslösning är att avgöra kraven för organisationen som kommer att använda den här lösningen.  Hybrididentitet startas som en stödjande roll (stöds alla andra molnlösningar genom att tillhandahålla autentisering) och för att lägga till nya och intressanta funktioner som att låsa upp nya arbetsbelastningar för användare.  Dessa arbetsbelastningar eller tjänster som du vill använda för dina användare styr kraven för hybrid identity-design.  Dessa tjänster och arbetsbelastningar behöver utnyttja hybrididentitet både lokalt och i molnet.  

Du behöver gå igenom dessa viktiga aspekter av företag att förstå vad det är ett krav nu och företaget planerar för framtiden. Om du inte har synligheten för långsiktig strategi för identitet hybridutformning, är risken att lösningen inte är skalbar när verksamheten behöver växa och ändra.   T han diagrammet nedan visar ett exempel på en hybrid identity-arkitektur och arbetsbelastningar som är att låsas upp för användare. Detta är bara ett exempel på alla nya möjligheter som kan låsas upp och levereras med en fast hybrid identity-strategi. 

Vissa komponenter som ingår i hybrid identity-arkitektur ![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Fastställa affärsbehov
Alla företag har olika krav, även om dessa företag finns i samma bransch, verkliga företag kraven kan variera. Du kan fortfarande utnyttja bästa praxis från branschen, men slutligen är det företagets affärsbehov som leder dig att fastställa krav för hybrid identity-design. 

Se till att besvara följande frågor för att identifiera dina affärsbehov:

* Ditt företag söker efter att klippa ut IT driftskostnaderna?
* Ditt företag söker efter för att skydda molnet tillgångar (infrastruktur, SaaS-appar)?
* Ditt företag söker efter för att modernisera IT?
  * Är användarna mer mobila och krävande IT att skapa undantag till din DMZ så att en annan typ av trafik till olika resurser?
  * Har företaget äldre appar som krävs för att publiceras till dessa moderna användare, men är inte enkelt att skriva om?
  * Behöver företaget att utföra dessa uppgifter och ta det under kontroll på samma gång?
* Ditt företag söker efter att skydda användarnas identitet och minska riskerna genom att nya verktyg som utnyttjar expertis för Microsoft Azure-säkerhet expertis lokalt?
* Ditt företag som försöker att ta bort dreaded ”externa” konton lokalt och flytta dem till det moln där de inte längre ett vilande hot i din lokala miljö?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analysera lokala identitetsinfrastruktur
Nu när du har en uppfattning om företagets företagets krav som du behöver utvärdera din lokala identitetsinfrastruktur. Denna utvärdering är viktigt för att definiera de tekniska kraven för att integrera din aktuella identitetslösning till molnet identitet hanteringssystemet. Se till att besvara följande frågor:

* Vilka autentisering och auktorisering lösning har ditt företag använder lokalt? 
* Har ditt företag för närvarande några lokala synkroniseringstjänster?
* Använder företaget en tredje parts identitetsleverantörer (IdP)?

Du måste också vara medveten om molntjänster som ditt företag kan ha. Det är mycket viktigt att utföra en utvärdering för att förstå den aktuella integreringen med SaaS, IaaS och PaaS-modeller i din miljö. Se till att besvara följande frågor under denna bedömning:

* Har företaget någon integrering med en molntjänstleverantör?
* Om Ja, vilka tjänster som används?
* För närvarande är den här integreringen i produktion eller är en pilot?

> [!NOTE]
> Om du inte har en korrekt mappning för alla dina appar och molntjänster, kan du använda verktyget Cloud App Discovery. Det här verktyget kan ge IT-avdelningen insyn i alla organisationens affärsbehov och konsumenten molnappar. Den gör det enklare att upptäcka skugg-it, information om användarmönster och eventuell användaråtkomst till dina molnprogram. Få igång finns [Cloud app discovery](manage-apps/cloud-app-discovery.md).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Utvärdera identity integration krav
Därefter måste du utvärdera integreringskraven identitet. Det är viktigt att definiera de tekniska kraven för hur användare autentiseras, hur organisationens förekomst ser ut i molnet, hur organisationen kan auktorisering och användarupplevelsen kommer att utvärderingen. Se till att besvara följande frågor:

* Kommer din organisation använder federation, standardautentisering eller båda?
* Är ett krav för federationen?  På grund av följande:
  * Kerberos-baserad SSO
  * Företaget har ett lokalt program (antingen inbyggd interna eller 3 part) som använder SAML eller liknande funktioner för federation.
  * MFA via smartkort. RSA SecurID osv.
  * Klienten åtkomstregler som löser frågorna nedan:
    1. Kan jag blockera all extern åtkomst till Office 365 baserat på IP-adressen för klienten?
    2. Kan jag blockera all extern åtkomst till Office 365, utom Exchange ActiveSync?
    3. Jag kan blockera all extern åtkomst till Office 365, utom webbläsarbaserade appar (OWA, SPO)
    4. Jag kan blockera all extern åtkomst till Office 365 för medlemmar i avsedda AD-grupper
* Säkerhetsgranskning/frågor
* Befintlig investering i federerad autentisering
* Vilket namn vår organisation använder för våra domänen i molnet?
* Har organisationen en anpassad domän?
  1. Är domänen offentliga och enkelt kan verifieras via DNS?
  2. Om det inte sedan har du en offentlig domän som kan användas för att registrera ett alternativt UPN i AD?
* Stämmer de ID: n för molnet representation? 
* Har organisationen appar som kräver integrering med molntjänster?
* Organisationen har flera domäner och alla använder standard- eller federerad autentisering?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Utvärdera program som körs i din miljö
Nu när du har en uppfattning om dina lokala och molnet infrastruktur behöver du utvärdera program som körs i dessa miljöer. Det är viktigt att definiera de tekniska kraven för att integrera dessa program till molnet identitet hanteringssystemet utvärderingen. Se till att besvara följande frågor:

* Var kommer våra program bor?
* Kommer användarna åt lokala program?  I molnet? Eller båda?
* Finns det planer för att ta de befintliga programbelastningar och flytta dem till molnet?
* Finns det planer för att utveckla nya program som ska finnas antingen lokalt eller i molnet som använder molnet autentisering?

## <a name="evaluate-user-requirements"></a>Utvärdera användarkrav
Du måste utvärdera användarkraven. Denna utvärdering är viktigt att definiera de steg som krävs för-boarding och hjälper användare som de övergång till molnet. Se till att besvara följande frågor:

* Kommer användarna åt program lokala?
* Kommer användarna åt program i molnet?
* Hur ofta kan användare logga in på sina lokala miljö?
* Hur kommer användare logga in på molnet?

> [!NOTE]
> Se till att varje svar och försök förstå anledningen till svaret. [Fastställa kraven på incidentsvar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) kommer att överskrida de tillgängliga alternativ och -tekniker/nackdelar med varje alternativ.  Har besvarat frågorna väljer du vilket alternativ som bäst passar dina behöver.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Ange krav för directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

