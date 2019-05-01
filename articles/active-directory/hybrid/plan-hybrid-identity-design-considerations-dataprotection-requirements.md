---
title: Hybrid identity design - kraven på dataskydd Azure | Microsoft Docs
description: När du planerar din hybrididentitetslösning, identifiera dataskyddskrav för din verksamhet och vilka alternativ som är tillgängliga för att bäst uppfylla dessa.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
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
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918783"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planering för att utöka datasäkerhet via en stark identitetslösning
Det första steget i att skydda data är att identifiera vem som kan komma åt dessa data. Du måste dessutom ha en identitetslösning som kan integreras med ditt system genom att tillhandahålla funktioner för autentisering och auktorisering. Autentisering och auktorisering förväxlas ofta med varandra och deras roller tror många. I verkligheten kan skiljer de sig, enligt bilden nedan:

![livscykeln för mobila enheter](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Livscykelstadier för hantering av mobila enheter**

När du planerar din hybrididentitetslösning, måste du förstå dataskyddskrav för din verksamhet och vilka alternativ som är tillgängliga för bäst uppfyller dessa krav.

> [!NOTE]
> När du är klar med att planera för datasäkerhet granska [fastställa krav för multifaktorautentisering](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) så att dina val om multifaktorautentiseringskrav inte påverkas av beslut du gjort i det här avsnittet.
> 
> 

## <a name="determine-data-protection-requirements"></a>Fastställa kraven på dataskydd
I mobility ålder, de flesta företag har ett gemensamt mål: användare få möjlighet att vara produktiva på sina mobila enheter, lokalt eller via en fjärranslutning från var som helst för att öka produktiviteten. Företag som har sådana krav kommer också att berörda om antalet hot som måste undvikas för att skydda företagets data och skyddar användarens integritet. Varje företag kan ha olika krav i detta avseende; olika regler för efterlevnad som varierar enligt vilken bransch företaget fungerar leder till olika designbeslut. 

Det finns dock vissa säkerhetsaspekter som bör utforskas och verifierats, oavsett bransch.

## <a name="data-protection-paths"></a>Datasökvägar för skydd
![Datasökvägar för skydd](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Datasökvägar för skydd**

I diagrammet ovan identity-komponenten kommer att vara den första som verifieras innan data behöver nås. Dessa data kan vara i olika tillstånd under den tid som den användes. Varje tal i det här diagrammet representerar en sökväg där data kan finnas någon gång i tid. Dessa siffror beskrivs nedan:

1. Dataskydd på enhetsnivå.
2. Dataskydd under överföringen.
3. Dataskydd på rest på plats.
4. Dataskydd när resten finns i molnet.

Det är nödvändigt att hybrididentitetslösning är kompatibla med att använda både lokalt och identity management-resurser för att identifiera användaren innan den ger åtkomst till data i molnet. När du planerar din hybrididentitetslösning bör du se till att följande frågor besvaras enligt organisationens krav:

## <a name="data-protection-at-rest"></a>Skydda data i vila
Oavsett var data är i vila (enheter, molnet eller lokalt), är det viktigt att utföra en utvärdering för att förstå företagets behov i detta avseende. Se till att följande frågor uppmanas för det här området:

* Behöver företaget att skydda data i vila?
  * Om Ja, kan hybrididentitetslösning integreras med den befintliga lokala infrastrukturen?
  * Om Ja, kan hybrididentitetslösning att integrera med dina arbetsbelastningar som finns i molnet?
* Är Identitetshantering i molnet kan skydda användarens autentiseringsuppgifter och andra data som lagras i molnet?

## <a name="data-protection-in-transit"></a>Dataskydd under överföring
Information som överförs mellan enheten och i datacenter eller mellan enheten och molnet måste skyddas. Dock innebär är under överföring inte nödvändigtvis en process för kommunikation med en komponent utanför din molntjänst; flyttas internt också, till exempel mellan två virtuella nätverk. Se till att följande frågor uppmanas för det här området:

* Behöver företaget att skydda data under överföring?
  * Om Ja, kan hybrididentitetslösning integrera med säker kontroller, till exempel SSL/TLS?
* Kan du behålla trafik till och inom katalogdatabasen (inom och mellan datacenter) registrerat Identitetshantering i molnet?

## <a name="compliance"></a>Efterlevnad
Bestämmelser, lagar och efterlevnadskrav varierar beroende på bransch som tillhör företaget. Företag i hög reglerade branscher måste uppfylla Identitetshantering frågor som rör efterlevnadsproblem. Regler, t.ex Sarbanes-Oxley (SOX), Health Insurance Portability och Accountability Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) och den Payment Card Industry Data Security Standard (PCI DSS) är strikt om identitets- och åtkomsthantering. Hybrididentitetslösning som företaget antar måste ha kärnfunktioner som uppfyller kraven för en eller flera av dessa regler. Se till att följande frågor uppmanas för det här området:

* Är hybrididentitetslösning kompatibel med regelkraven för ditt företag?
* Hybrididentitetslösning har skapats 
* i funktioner som gör att ditt företag för att vara kompatibel efterlevnadskrav? 

> [!NOTE]
> Se till att ner varje svar och försök förstå anledningen till svaret. [Definiera en strategi för Data Protection](plan-hybrid-identity-design-considerations-data-protection-strategy.md) tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Har besvarat frågor väljer du vilket alternativ som bäst passar din verksamhet behöver du.
> 
> 

## <a name="next-steps"></a>Nästa steg
 [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

