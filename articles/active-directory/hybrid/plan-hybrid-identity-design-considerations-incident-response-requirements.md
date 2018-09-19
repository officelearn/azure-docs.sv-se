---
title: Hybrid identity design - kraven på incidentsvar Azure | Microsoft Docs
description: Fastställa funktioner för övervakning och rapportering för hybrididentitetslösning som kan utnyttjas av IT-avdelningen att vidta åtgärder för att identifiera och undanröja eventuella hot
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: baa1d1fec6438b937ba783adcc86c4f74fa1a6db
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312062"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Fastställa kraven på incidentsvar för din hybrididentitetslösning
Stora eller medelstora organisationer troligen har en [svar på säkerhetsincidenter](https://technet.microsoft.com/library/cc700825.aspx) hjälp IT vidta åtgärder i enlighet med detta till nivå för incident. Hanteringssystem för identitet är en viktig komponent i processen för incidenthantering eftersom den kan användas för att identifiera vem som utförde en specifik åtgärd mot målet. Hybrididentitetslösning måste kunna tillhandahålla funktioner för övervakning och rapportering som kan utnyttjas av IT-avdelningen att vidta åtgärder för att identifiera och minimera potentiella hot. I en typisk incidentåtgärdsplan har du följande faser som en del av planen:

1. Inledande utvärderingen.
2. Incident kommunikation.
3. Skada kontroll och minska kreditkortsrisker.
4. Identifiering av vad det var kompromettering och allvarlighetsgrad.
5. Bevis konservering.
6. Meddelande till lämplig parter.
7. Systemåterställning.
8. Dokumentation.
9. Utvärdering av skador och kostnader.
10. Processen och planera revision.

Under identifieringen av it-avdelningen var kompromettering och allvarlighetsgrad-fasen, det ska vara nödvändigt att identifiera vilka system som har komprometterats, filer som har använts och ta reda på dessa filer. Hybrid identitetssystemet bör kunna uppfylla följande krav för att hjälpa dig att identifiera den användare som gjort dessa ändringar. 

## <a name="monitoring-and-reporting"></a>Övervakning och rapportering
Många gånger identitetssystem kan också i fas första utvärdering främst om systemet har skapats i gransknings- och rapporteringsfunktioner. Under den inledande utvärderingen IT-administratören måste kunna identifiera en misstänkt aktivitet eller systemet bör kunna utlösaren som det automatiskt baserat på en förkonfigurerad uppgift. Många aktiviteter kan tyda på ett möjligt angrepp, men i andra fall kan en felaktigt konfigurerade systemet kan leda till ett antal falska positiva identifieringar i ett system för identifiering av intrång. 

Hanteringssystem för identitet ska hjälpa IT-administratörer att identifiera och rapportera de misstänkta aktiviteterna. Vanligtvis kan dessa tekniska krav uppfyllas genom att övervaka alla system och att ha en rapporteringsfunktionen i Markera potentiella hot. Använd frågorna nedan för att hjälpa dig att utforma din hybrididentitetslösning och med kraven på incidentsvar beräkningen:

* Har företaget ett svar på säkerhetsincidenter på plats?
  * Om Ja, den aktuella identitetshanteringssystem används som en del av processen?
* Behöver företaget att identifiera misstänkta inloggningsförsök från användare på olika enheter?
* Behöver företaget att identifiera potentiellt komprometterade användarens autentiseringsuppgifter?
* Behöver företaget att granska användarens åtkomst och åtgärden?
* Behöver företaget att veta när en användare återställa sina lösenord?

## <a name="policy-enforcement"></a>Principtillämpning
Under skada och minskad risk-fas är det viktigt att snabbt minska verkliga och potentiella effekterna av en attack. Åtgärden som du kommer att utföras kan nu göra skillnaden mellan en mindre och en större. Det exakta svaret beror på din organisation och typen av angrepp som kan uppstå. Om den inledande utvärderingen avslutat att ett konto har komprometterats, måste du framtvinga för att blockera det här kontot. Det är bara ett exempel där hanteringssystem för identitet ska användas. Använd frågorna nedan för att hjälpa dig att utforma din hybrididentitetslösning samtidigt som man tänker på hur principer tillämpas för att reagera på en pågående incident:

* Har företaget principer på plats att blockera användare från åtkomst till nätverket om det behövs?
  * Om Ja, den aktuella lösningen integreras med hybrid identity management system som du ska använda?
* Behöver företaget att tillämpa villkorlig åtkomst för användare som finns i karantän? 

> [!NOTE]
> Se till att ner varje svar och försök förstå anledningen till svaret. [Definiera en strategi för data protection](plan-hybrid-identity-design-considerations-data-protection-strategy.md) tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Har besvarat frågor väljer du vilket alternativ som bäst passar din verksamhet behöver du.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Definiera en strategi för skydd av data](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

