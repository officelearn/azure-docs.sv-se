---
title: Hybrididentitetsdesign – dataskyddskrav Azure | Microsoft-dokument
description: När du planerar din hybrididentitetslösning ska du identifiera dataskyddskraven för ditt företag och vilka alternativ som är tillgängliga för att bäst uppfylla dessa krav.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918783"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planera för att förbättra datasäkerheten genom en stark identitetslösning
Det första steget för att skydda data är att identifiera vem som kan komma åt dessa data. Du måste också ha en identitetslösning som kan integreras med ditt system för att tillhandahålla autentiserings- och auktoriseringsfunktioner. Autentisering och auktorisering förväxlas ofta med varandra och deras roller missförstås. I verkligheten är de olika, vilket visas i figuren nedan:

![livscykel för mobila enheter](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Livscykelstadier för hantering av mobila enheter**

När du planerar din hybrididentitetslösning måste du förstå dataskyddskraven för ditt företag och vilka alternativ som är tillgängliga för att bäst uppfylla dessa krav.

> [!NOTE]
> När du är klar med planeringen för datasäkerhet läser du [Bestäm multifaktorautentiseringskrav](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) för att säkerställa att dina val om multifaktorautentiseringskrav inte påverkades av de beslut du fattade i det här avsnittet.
> 
> 

## <a name="determine-data-protection-requirements"></a>Bestäm dataskyddskrav
I mobilitetsåldern har de flesta företag ett gemensamt mål: att göra det möjligt för användarna att vara produktiva på sina mobila enheter, medan de är lokala eller på distans från var som helst för att öka produktiviteten. Företag som har sådana krav kommer också att vara oroade över antalet hot som måste mildras för att hålla företagets data säkra och upprätthålla användarens integritet. Varje företag kan ha olika krav i detta avseende; olika efterlevnadsregler som kommer att variera beroende på vilken bransch företaget agerar kommer att leda till olika designbeslut. 

Det finns dock vissa säkerhetsaspekter som bör undersökas och valideras, oavsett bransch.

## <a name="data-protection-paths"></a>Sökvägar för dataskydd
![dataskyddsvägar](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Sökvägar för dataskydd**

I diagrammet ovan blir identitetskomponenten den första som verifieras innan data används. Dessa data kan dock vara i olika lägen under den tid som de användes. Varje nummer i det här diagrammet representerar en sökväg där data kan placeras någon gång i tiden. Dessa siffror förklaras nedan:

1. Dataskydd på enhetsnivå.
2. Dataskydd under transporten.
3. Dataskydd i vila lokalt.
4. Dataskydd medan du vilar i molnet.

Det är nödvändigt att hybrididentitetslösningen kan utnyttja både lokala och molnidentitetshanteringsresurser för att identifiera användaren innan den ger åtkomst till data. När du planerar din hybrididentitetslösning ska du se till att följande frågor besvaras enligt organisationens krav:

## <a name="data-protection-at-rest"></a>Dataskydd i vila
Oavsett var data är i vila (enhet, moln eller lokalt) är det viktigt att göra en bedömning för att förstå organisationens behov i detta avseende. På detta område bör du se till att följande frågor ställs:

* Behöver ditt företag skydda data i vila?
  * Om ja, kan hybrididentitetslösningen integreras med din nuvarande lokala infrastruktur?
  * Om ja, kan hybrididentitetslösningen integreras med dina arbetsbelastningar i molnet?
* Kan molnidentitetshanteringen skydda användarens autentiseringsuppgifter och andra data som lagras i molnet?

## <a name="data-protection-in-transit"></a>Dataskydd under transitering
Data som överförs mellan enheten och datacentret eller mellan enheten och molnet måste skyddas. Att vara i transit innebär dock inte nödvändigtvis en kommunikationsprocess med en komponent utanför molntjänsten. Den rör sig internt, till exempel mellan två virtuella nätverk. På detta område bör du se till att följande frågor ställs:

* Behöver ditt företag skydda data under transport?
  * Om ja, kan hybrididentitetslösningen integreras med säkra kontroller som SSL/TLS?
* Håller molnidentitetshanteringen trafiken till och inom katalogarkivet (inom och mellan datacenter) signerad?

## <a name="compliance"></a>Efterlevnad
Regler, lagar och regelefterlevnadskrav varierar beroende på hur branschen ditt företag tillhör. Företag i högt reglerade branscher måste ta itu med identitetshanteringsfrågor i samband med efterlevnadsfrågor. Förordningar som Sarbanes-Oxley (SOX), Health Insurance Portability and Accountability Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) och Payment Card Industry Data Security Standard (PCI DSS) är strikta när det gäller identitet och åtkomst. Hybrid identitetslösning som ditt företag kommer att anta måste ha de grundläggande funktioner som kommer att uppfylla kraven i en eller flera av dessa förordningar. På detta område bör du se till att följande frågor ställs:

* Är hybrididentitetslösningen kompatibel med lagstadgade krav för ditt företag?
* Har hybrididentitetslösningen byggt 
* funktioner som gör det möjligt för ditt företag att uppfylla lagstadgade krav? 

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera dataskyddsstrategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att gå igenom de tillgängliga alternativen och fördelar / nackdelar med varje alternativ.  Genom att ha svarat på dessa frågor väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
 [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

