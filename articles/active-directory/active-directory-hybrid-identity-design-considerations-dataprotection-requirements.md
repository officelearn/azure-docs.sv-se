---
title: "Identitet hybridutformning - kraven på dataskydd Azure | Microsoft Docs"
description: "När du planerar din hybrididentitetslösning identifiera dataskyddskrav för ditt företag och vilka alternativ som är tillgängliga för bäst uppfyller kraven."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 1a2feca8d24fabda4c191b5a181ab6606c912657
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Planera för att förbättra datasäkerhet via starkt identitetslösning
Det första steget att skydda data är identifiera vem som kan komma åt dessa data och som en del av den här processen måste du ha en identitet lösning som kan integreras med systemet innehåller funktioner för autentisering och auktorisering. Autentisering och auktorisering ihop ofta med varandra och deras roller tror många. I verkligheten är de helt annorlunda, som visas i bilden nedan:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**Livscykelstadier för hantering av mobila enheter**

När du planerar din hybrididentitetslösning måste du förstå kraven på dataskydd för ditt företag och vilka alternativ som är tillgängliga för bäst uppfyller kraven.

> [!NOTE]
> När du är klar med att planera för datasäkerhet läsa [ange krav för multifaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) så att dina val av multifaktorautentisering kraven inte påverkas av de beslut du fattar i det här avsnittet.
> 
> 

## <a name="determine-data-protection-requirements"></a>Fastställa kraven på dataskydd
I mobility ålder, de flesta företag har ett gemensamt mål: aktivera sina användare att vara produktiva på sina mobila enheter lokalt eller fjärranslutet från var som helst för att öka produktiviteten. Detta kan bero på ett gemensamt mål, men företag som har sådana krav blir också problem om mängden hot som måste uppvägas för att skydda företagets data och skyddar användarens sekretess. Alla företag kan ha olika krav i detta avseende; olika kompatibilitetsregler som varierar enligt vilken bransch företaget fungerar kommer att leda till olika designbeslut. 

Det finns vissa säkerhetsaspekter som ska vara utforskade och verifierats, oavsett bransch, som beskrivs i nästa avsnitt.

## <a name="data-protection-paths"></a>Sökvägar för data protection
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**Sökvägar för data protection**

I ovanstående diagram identity-komponenten kommer att vara den första som verifieras innan data används. Dessa data kan vara i olika tillstånd under den tid som den användes. Varje tal i det här diagrammet representerar en sökväg där data kan finnas på någon punkt i tiden. Dessa värden beskrivs nedan:

1. Dataskydd på enhetsnivå.
2. Dataskydd under överföring.
3. Dataskydd när resten lokala.
4. Dataskydd när resten finns i molnet.

Även om tekniska kontroller som kommer aktivera IT-avdelningen att skydda data på var och en av dessa faser direkt erbjuds inte av hybrididentitetslösning, är det nödvändigt att hybrididentitetslösning kan utnyttja både lokalt och i molnet identity management-resurser för att identifiera användaren innan du beviljar åtkomst till data. När planera din hybrididentitetslösning för att säkerställa att följande frågor besvaras enligt organisationens behov:

## <a name="data-protection-at-rest"></a>Skydd av data i vila
Oavsett var finns data i vila (enhet, molnet eller lokalt), är det viktigt att utföra en utvärdering för att förstå organisationen behöver i detta sammanhang. Se till att följande frågor uppmanas för det här området:

* Behöver företaget att skydda data i vila?
  * Om Ja, kan hybrididentitetslösning integreras med den befintliga lokala infrastrukturen?
  * Om Ja, kan hybrididentitetslösning att integrera med dina arbetsbelastningar som finns i molnet?
* Kan Identitetshantering molnet skydda användarens autentiseringsuppgifter och andra data som lagras i molnet?

## <a name="data-protection-in-transit"></a>Dataskydd under överföring
Data under överföring mellan enheten och datacenter eller mellan enheten och i molnet måste skyddas. Dock innebär som transit inte nödvändigtvis en process för kommunikation med en komponent utanför Molntjänsten; flyttas internt, även, till exempel mellan två virtuella nätverk. Se till att följande frågor uppmanas för det här området:

* Behöver företaget att skydda data under överföring?
  * Om Ja, kan hybrididentitetslösning att integrera med säkra kontroller, till exempel SSL/TLS?
* Kan du behålla trafiken till och inom katalogdatabas (inom och mellan datacenter) signerade Identitetshantering molnet?

## <a name="compliance"></a>Efterlevnad
Förordningar, lagar och regelefterlevnad krav varierar beroende på bransch ditt företag tillhör. Företag i hög reglerade branscher måste uppfylla Identitetshantering frågor som rör efterlevnadsproblem. Förordningar som Sarbanes-Oxley (SOX), Health Insurance Portability and Accountability Act (HIPAA), Gramm Leach Bliley Act (GLBA) och den Payment Card Industry Data Security Standard (PCI DSS) är mycket strikt om identitets- och. Hybrididentitetslösning som företaget antar måste ha grundfunktionerna som uppfyller kraven för en eller flera av dessa regler. Se till att följande frågor uppmanas för det här området:

* Är hybrididentitetslösning som är kompatibel med regelkrav för ditt företag?
* Hybrididentitetslösning har inbyggda funktioner som gör att företaget ska vara kompatibla regelkrav? 

> [!NOTE]
> Se till att varje svar och försök förstå anledningen till svaret. [Definiera en strategi för Data Protection](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att överskrida det tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Har besvarat frågorna väljer du vilket alternativ som bäst passar dina behöver.
> 
> 

## <a name="next-steps"></a>Nästa steg
 [Ange krav för innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

