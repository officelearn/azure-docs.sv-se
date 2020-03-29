---
title: Hybrididentitetsdesign – incidentsvarskrav Azure | Microsoft-dokument
description: Fastställ övervaknings- och rapporteringsfunktioner för hybrididentitetslösningen som kan utnyttjas av IT för att vidta åtgärder för att identifiera och minska potentiella hot
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109279"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Fastställ incidentsvarskrav för din hybrididentitetslösning
Stora eller medelstora organisationer kommer sannolikt att ha en [säkerhetsincidentsvar](https://technet.microsoft.com/library/cc700825.aspx) på plats för att hjälpa IT att vidta åtgärder i enlighet med incidentnivån. Identitetshanteringssystemet är en viktig komponent i incidentsvarsprocessen eftersom det kan användas för att identifiera vem som utförde en specifik åtgärd mot målet. Hybrididentitetslösningen måste kunna tillhandahålla övervaknings- och rapporteringsfunktioner som kan utnyttjas av IT för att vidta åtgärder för att identifiera och minska ett potentiellt hot. I en typisk incidenthanteringsplan har du följande faser som en del av planen:

1. Första bedömningen.
2. Incidentkommunikation.
3. Skadekontroll och riskreducering.
4. Identifiering av vad det var kompromiss och svårighetsgrad.
5. Bevis bevarande.
6. Anmälan till lämpliga parter.
7. Systemåterställning.
8. Dokumentation.
9. Skador och kostnadsbedömning.
10. Bearbeta och planera revidering.

Under identifieringen av vad det var kompromiss och allvarlighetsgrad, kommer det att bli nödvändigt att identifiera de system som har komprometterats, filer som har använts och bestämma känsligheten för dessa filer. Ditt hybrididentitetssystem bör kunna uppfylla dessa krav för att hjälpa dig att identifiera användaren som har gjort ändringarna. 

## <a name="monitoring-and-reporting"></a>Övervakning och rapportering
Många gånger identitetssystemet kan också hjälpa till i inledande bedömningsfasen främst om systemet har byggt in gransknings- och rapporteringsfunktioner. Under den första utvärderingen måste IT-administratör kunna identifiera en misstänkt aktivitet, eller så ska systemet kunna utlösa den automatiskt baserat på en förkonfigurerad uppgift. Många aktiviteter kan tyda på en möjlig attack, men i andra fall kan ett dåligt konfigurerat system leda till ett antal falska positiva identifieringssystem i ett intrångsdetekteringssystem. 

Identitetshanteringssystemet bör hjälpa IT-administratörer att identifiera och rapportera dessa misstänkta aktiviteter. Vanligtvis kan dessa tekniska krav uppfyllas genom att övervaka alla system och ha en rapporteringskapacitet som kan belysa potentiella hot. Använd frågorna nedan för att designa din hybrididentitetslösning samtidigt som du tar hänsyn till kraven för incidentsvar:

* Har ditt företag en säkerhetsincident på plats?
  * Om ja, används det nuvarande identitetshanteringssystemet som en del av processen?
* Behöver ditt företag identifiera misstänkta inloggningsförsök från användare på olika enheter?
* Behöver ditt företag identifiera potentiella komprometterade användarens autentiseringsuppgifter?
* Behöver ditt företag granska användarens åtkomst och åtgärd?
* Behöver ditt företag veta när en användare återställer sitt lösenord?

## <a name="policy-enforcement"></a>Policyframtvingande
Under skadekontroll och riskreduceringsfas är det viktigt att snabbt minska de faktiska och potentiella effekterna av en attack. Denna åtgärd som du kommer att vidta på denna punkt kan göra skillnaden mellan en minderårig och en större en. Det exakta svaret beror på din organisation och vilken typ av attack du står inför. Om den första bedömningen kom fram till att ett konto har komprometterats måste du tillämpa principen för att blockera kontot. Det är bara ett exempel där identitetshanteringssystemet kommer att utnyttjas. Använd frågorna nedan för att designa din hybrididentitetslösning samtidigt som du tar hänsyn till hur principer kommer att tillämpas för att reagera på en pågående incident:

* Har ditt företag principer för att blockera användare från åtkomst till nätverket om det behövs?
  * Om ja, integrerar den nuvarande lösningen med hybrid identitetshanteringssystem som du ska anta?
* Behöver ditt företag tillämpa villkorlig åtkomst för användare som är i karantän? 

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera dataskyddsstrategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att gå igenom de tillgängliga alternativen och fördelar / nackdelar med varje alternativ.  Genom att ha svarat på dessa frågor väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Definiera dataskyddsstrategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

