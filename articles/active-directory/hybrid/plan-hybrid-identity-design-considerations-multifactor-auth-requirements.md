---
title: Hybrididentitetsdesign – multifaktorautentiseringskrav Azure | Microsoft-dokument
description: Med kontrollen Villkorlig åtkomst kontrollerar Azure Active Directory de specifika villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet. När dessa villkor är uppfyllda autentiseras användaren och får åtkomst till programmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
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
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109300"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Fastställa multifaktorautentiseringskrav för din hybrididentitetslösning
I denna värld av rörlighet, med användare som använder data och program i molnet och från alla enheter, har det blivit av största vikt att skydda den här informationen.  Varje dag finns det en ny rubrik om en säkerhetsöverträdelse.  Även om det inte finns någon garanti mot sådana överträdelser, multifaktorautentisering, ger ett extra lager av säkerhet för att förhindra dessa överträdelser.
Börja med att utvärdera organisationens krav för multifaktorautentisering. Det vill än, vad är organisationen försöker säkra.  Den här utvärderingen är viktig för att definiera de tekniska kraven för att ställa in och aktivera organisationer som användare för multifaktorautentisering.

Se till att svara på följande:

* Försöker ditt företag skydda Microsoft-appar? 
* Hur dessa appar publiceras?
* Ger ditt företag fjärråtkomst så att anställda kan komma åt lokala appar?

Om ja, vilken typ av fjärråtkomst? Du måste också utvärdera var de användare som har åtkomst till dessa program ska finnas. Den här utvärderingen är ytterligare ett viktigt steg för att definiera rätt multifaktorautentiseringsstrategi. Se till att svara på följande frågor:

* Var ska användarna finnas?
* Kan de lokaliseras var som helst?
* Vill ditt företag fastställa begränsningar beroende på användarens plats?

När du förstår dessa krav är det viktigt att också utvärdera användarens krav för multifaktorautentisering. Den här utvärderingen är viktig eftersom den definierar kraven för att distribuera multifaktorautentisering. Se till att svara på följande frågor:

* Är användarna bekanta med multifaktorautentisering?
* Kommer vissa användningsområden att krävas för att tillhandahålla ytterligare autentisering?  
  * Om ja, hela tiden, när de kommer från externa nätverk, eller tillgång till specifika program, eller på andra villkor?
* Kommer användarna att behöva utbildning om hur man ställer in och implementerar multifaktorautentisering?
* Vilka är de viktigaste scenarierna som ditt företag vill aktivera multifaktorautentisering för sina användare?

När du har svarat på de tidigare frågorna kan du förstå om det redan finns multifaktorautentisering implementerad lokalt. Den här utvärderingen är viktig för att definiera de tekniska kraven för att ställa in och aktivera organisationer som användare för multifaktorautentisering. Se till att svara på följande frågor:

* Behöver ditt företag skydda privilegierade konton hos MFA?
* Behöver ditt företag aktivera MFA för vissa ansökningar av efterlevnadsskäl?
* Behöver ditt företag aktivera MFA för alla berättigade användare av dessa program eller bara administratörer?
* Behöver du ha MFA alltid aktiverat eller bara när användarna är inloggade utanför företagets nätverk?

## <a name="next-steps"></a>Nästa steg
[Definiera en strategi för antagande av hybrididentitet](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

