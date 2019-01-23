---
title: Identitetskrav för hybrid identity design Azure i molnet | Microsoft Docs
description: Identifiera företagets affärsbehov som leder dig att fastställa krav för hybrid identity design.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/27/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 9be8ddb6869a4872ff3eae9b589584f850c7dc96
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463628"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Fastställa identitetskrav för för din hybrididentitetslösning
Det första steget i utforma en hybrididentitetslösning är att avgöra kraven för organisationen som kommer att utnyttja den här lösningen.  Hybrididentitet startas som en stödjande roll (stöds alla andra molnlösningar genom att tillhandahålla autentisering) och på att tillhandahålla nya och intressanta funktioner att låsa upp nya arbetsbelastningar för användare.  Dessa arbetsbelastningar eller tjänster som du vill använda för dina användare styr kraven för hybrid identity utformningen.  Dessa tjänster och arbetsbelastningar behöver använda hybrididentitet både lokalt och i molnet.  

Du behöver gå igenom dessa viktiga aspekter av företag att förstå vad det är ett krav nu och vad företaget planerar för framtiden. Om du inte har synligheten för långsiktig strategi för hybrid identity design, är risken att lösningen inte är skalbar när verksamheten behöver växa och ändra.   T han diagrammet nedan visar ett exempel på en hybrid identity-arkitektur och de arbetsbelastningar som är att låsas upp för användare. Det här är bara ett exempel på alla nya möjligheter som kan låsas upp och levereras med en solid hybrid identity-strategi. 

Vissa komponenter som ingår i hybrid identity-arkitektur ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Fastställa affärsbehov
Alla företag har olika krav, även om dessa företag finns i samma bransch, den professionella kraven kan variera. Du kan fortfarande utnyttja bästa praxis från branschen, men slutänden är det företagets affärsbehov som leder dig att fastställa krav för hybrid identity design. 

Se till att besvara följande frågor för att identifiera dina affärsbehov:

* Företaget vill klippa ut IT driftskostnaderna?
* Företaget vill skydda molntillgångar (SaaS-appar, infrastruktur)?
* Företaget vill modernisera IT?
  * Är användarna mer mobila och krävande IT att skapa undantag till Perimeternätverket att tillåta annan typ av trafik till olika resurser?
  * Har företaget äldre appar som krävs för att publiceras till dessa moderna användare men är inte enkelt att skriva om?
  * Behöver företaget att utföra dessa uppgifter och placera den under kontrollen på samma gång?
* Företaget vill skydda användarnas identiteter och minska risken genom att föra nya verktyg som utnyttjar expertis för Microsofts Azure-säkerhet expertis på plats?
* Ditt företag försöker bli av med dreaded ”external” konton lokalt och flytta dem till molnet där de inte längre ett vilande hot i din lokala miljö?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analysera den lokala identitetsinfrastrukturen
Nu när du har en idé om företagets företagets krav behöver du utvärdera din lokala identitetsinfrastruktur. Den här utvärderingsversionen är viktigt för att definiera de tekniska kraven för att integrera din aktuella identitetslösning till hanteringssystem för cloud-identitet. Se till att besvara följande frågor:

* Vilken autentisering och auktorisering lösningen gör ditt företag använder lokalt? 
* Har företaget för närvarande alla lokala synkroniseringstjänster?
* Använder företaget någon tredje parts identitetsleverantörer (IdP)?

Du måste också vara medvetna om molntjänster som företaget kan ha. Det är mycket viktigt att utföra en utvärdering för att förstå den aktuella integrationen med SaaS, IaaS och PaaS-modeller i din miljö. Se till att besvara följande frågor under den här utvärderingen:

* Har företaget någon integrering med en molntjänstleverantör?
* Om Ja, vilka tjänster som används?
* Är den här integreringen för närvarande i produktion eller är det en pilot?

> [!NOTE]
> Cloud Discovery analyserar dina trafikloggar mot Microsoft Cloud App Securitys cloud app-katalogen för över 16 000 moln-appar som rangordnas och poängsätts baserat på fler än 70 riskfaktorer använder för att ge dig med pågående insyn i molnet, Shadow IT och risken Skuggkopia IT utgör i din organisation. Att hämta börja med att läsa [konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Utvärdera identitetskrav för integrering
Därefter måste du utvärdera identitetskrav för integrering. Den här utvärderingsversionen är viktigt att definiera de tekniska kraven för hur användarna ska autentiseras, hur organisationens närvaro ser ut i molnet, hur organisationen kan auktorisering och användarupplevelsen datorn kommer att vara. Se till att besvara följande frågor:

* Kommer din organisation använder federation, standardautentisering eller båda?
* Är ett krav för federation?  På grund av följande:
  * Kerberos-baserad enkel inloggning
  * Företaget har ett lokalt program (antingen inbyggd interna eller 3 part) som använder SAML eller liknande federation-funktioner.
  * MFA via smartkort. RSA SecurID, etc.
  * Åtkomstregler för klienten som löser frågorna nedan:
    1. Kan jag blockera all extern åtkomst till Office 365 baserat på IP-adressen för klienten?
    2. Kan jag blockera all extern åtkomst till Office 365, utom Exchange ActiveSync?
    3. Jag kan blockera all extern åtkomst till Office 365, förutom webbläsarbaserade appar (OWA, SPO)
    4. Jag kan blockera all extern åtkomst till Office 365 för medlemmar av avsedda AD-grupperna
* Säkerhetsgranskning/frågor
* Redan befintlig investering i federerad autentisering
* Vilket namn kommer företaget att använda vår domän i molnet?
* Har organisationen en anpassad domän?
  1. Är den domänen offentliga och enkelt verifierbart via DNS?
  2. Sedan har du en offentlig domän som kan användas för att registrera ett alternativt UPN i AD om den inte?
* Är användaridentifierare konsekventa för molnet representation? 
* Har organisationen appar som kräver integrering med molntjänster?
* Organisationen har flera domäner och använder de alla standard- eller federerad autentisering?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Utvärdera program som körs i din miljö
Nu när du har en idé om dina lokala och molnbaserade infrastruktur behöver du utvärdera program som körs i dessa miljöer. Den här utvärderingsversionen är viktigt att definiera de tekniska kraven för att integrera dessa program till hanteringssystem för cloud-identitet. Se till att besvara följande frågor:

* Där kommer våra program live?
* Kommer användarna att ha åtkomst till lokala program?  I molnet? Eller båda?
* Finns det några planer på att ta de befintliga arbetsbelastningarna för program och flytta dem till molnet?
* Det finns planer på att utveckla nya program som ska finnas antingen lokalt eller i molnet som kommer att använda molnbaserad autentisering?

## <a name="evaluate-user-requirements"></a>Utvärdera kraven användare
Du måste utvärdera kraven för användare. Den här utvärderingsversionen är viktigt att definiera de steg som krävs för publicering och hjälpa användare när de flyttar till molnet. Se till att besvara följande frågor:

* Ska användarna använda lokal?
* Kommer användarna att ha åtkomst till program i molnet?
* Hur kan användare vanligtvis logga in på sina lokala miljö?
* Hur kommer användarna loggar in till molnet?

> [!NOTE]
> Se till att ner varje svar och försök förstå anledningen till svaret. [Fastställa kraven på incidentsvar](plan-hybrid-identity-design-considerations-incident-response-requirements.md) tillgängliga alternativ och-tekniker/nackdelar med varje alternativ.  Har besvarat frågor väljer du vilket alternativ som bäst passar din verksamhet behöver du.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa katalogsynkroniseringskrav](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

