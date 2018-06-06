---
title: Identitet hybridutformning - kraven på incidentsvar Azure | Microsoft Docs
description: Fastställa funktioner för övervakning och rapportering för hybrididentitetslösning som kan utnyttjas av IT-avdelningen att vidta åtgärder för att identifiera och minska potentiella hot
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
ms.openlocfilehash: 09dfa0bd01a7de95ba6ce3093ddf589595116aa6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801066"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Fastställa kraven på incidentsvar för din hybrididentitetslösning
Stora eller medelstora organisationer troligen har en [säkerhet incidenter](https://technet.microsoft.com/library/cc700825.aspx) för att hjälpa IT vidta åtgärder i enlighet med detta till nivån för incident. Identity management-systemet är en viktig komponent i incidenter processen eftersom den kan användas för att identifiera vem som utförde en specifik åtgärd mot målet. Hybrididentitetslösning måste kunna tillhandahålla övervakning och rapportering funktioner som kan utnyttjas av IT-avdelningen att vidta åtgärder för att identifiera och åtgärda ett potentiellt hot. I en typisk incidentsvarsplanen har du följande faser som en del av planen:

1. Inledande kontroll.
2. Incident kommunikation.
3. Skada och minska kreditkortsrisker.
4. Identifiering av vad det var kompromettering och allvarlighetsgrad.
5. Bevis konservering.
6. Meddelande till berörda parter.
7. Systemåterställning.
8. Dokumentation.
9. Bedömning av skada och kostnader.
10. Processen och planera revision.

Under identifieringen av IT-avdelningen och olika allvarlighetsgrad fas, blir det nödvändigt att identifiera datorer som har komprometterats filer som har öppnats och ta reda på dessa filer. Hybrid identity systemet ska kunna uppfylla kraven för att hjälpa dig att identifiera användaren som utförde ändringarna. 

## <a name="monitoring-and-reporting"></a>Övervakning och rapportering
Många gånger identitetssystem kan också i första kompatibilitetsutvärderingsfasen huvudsakligen om systemet har skapats i gransknings- och rapporteringsfunktioner. Under den inledande bedömningen IT-administratören måste kunna identifiera en misstänkt aktivitet eller systemet ska kunna utlösaren den automatiskt baserat på en förkonfigurerad uppgift. Många aktiviteter kan tyda på ett möjligt angrepp, men i andra fall ett felaktigt konfigurerat system kan leda till ett antal falska positiva identifieringar i ett system för identifiering av adressintrång. 

Identity management-system bör hjälpa IT-administratörer att identifiera och rapportera de misstänkta aktiviteterna. Vanligtvis kan dessa tekniska krav uppfyllas genom att övervaka alla system och har en rapporteringsfunktion som kan fokusera på potentiella hot. Använd frågorna nedan för att hjälpa dig att utforma din hybrididentitetslösning när med hänsyn till kraven på incidentsvar tänka på:

* Har företaget en incidenter för säkerhet på plats?
  * Om Ja, det aktuella hanteringssystemet identitet används som en del av processen?
* Behöver företaget identifiera misstänkta inloggningsförsök från användare för olika enheter?
* Behöver företaget att identifiera potentiellt komprometterade användarens autentiseringsuppgifter?
* Behöver företaget granska användarens åtkomst och åtgärden?
* Behöver företaget att veta när en användare återställa sina lösenord?

## <a name="policy-enforcement"></a>Tvingande principer
Vid skada och risker minskning-fas är det viktigt att snabbt minska faktiska och potentiella effekterna av ett angrepp. Den åtgärd som ska tas kan nu göra skillnaden mellan en mindre och en större. Det exakta svaret beror på din organisation och typen av angrepp som kan uppstå. Om den inledande kontrollen slutsatsen att ett konto komprometteras, behöver du tillämpa princip för att blockera det här kontot. Det är bara ett exempel där hanteringssystemet identitet kommer utnyttjas. Använd frågorna nedan för att hjälpa dig att utforma en hybrididentitetslösning när ta hänsyn till hur principer tillämpas för att reagera på en pågående incident:

* Har företaget principer för att blockera användare från åtkomst nätverket vid behov?
  * Om Ja, kan den aktuella lösningen integreras med hybrid identity management system som du kommer att ta i bruk?
* Behöver företaget att tillämpa villkorlig åtkomst för användare som är i karantän? 

> [!NOTE]
> Se till att varje svar och försök förstå anledningen till svaret. [Definiera en strategi för data protection](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att överskrida det tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Har besvarat frågorna väljer du vilket alternativ som bäst passar dina behöver.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Definiera en strategi för skydd av data](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

