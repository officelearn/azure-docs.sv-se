---
title: Hybrid identity design - multifaktorautentiseringskrav Azure | Microsoft Docs
description: Med villkorlig åtkomstkontroll kontrollerar de specifika villkor som du väljer när du autentiserar användaren och innan åtkomst ges till programmet i Azure Active Directory. När dessa villkor är uppfyllda, användaren autentiseras och får åtkomst till programmet.
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
ms.openlocfilehash: 3dabb381c16aa107e41c1d556e61e020b8c6a6c3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170250"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Fastställa krav för multifaktorautentisering för dina hybrididentitetslösning
I den här världen för mobilitet med användare som kommer åt data och program i molnet och på valfri enhet, har det blivit avgörande att skydda den här informationen.  Varje dag är det en ny rubrik om en säkerhetsöverträdelse.  Även om det finns ingen garanti mot överträdelser, ger multifaktorautentisering, ett extra lager av säkerhet för att förhindra dessa överträdelser.
Börja med att utvärdera organisationer kraven för multifaktorautentisering. Det vill säga vad organisationen försöker skydda.  Den här utvärderingsversionen är viktigt att definiera de tekniska kraven för att konfigurera och aktivera organisationer användare för multifaktorautentisering.

Se till att svara på följande:

* Är företaget vill du skydda Microsoft-appar 
* Hur de här apparna har publicerats?
* Företaget kan ge fjärråtkomst så att anställda åtkomst till lokala appar?

Om Ja, vilken typ av fjärråtkomst? Du måste också att utvärdera där användare som kommer åt dessa program kommer att finnas. Den här utvärderingsversionen är ett annat viktigt steg för att definiera en strategi för rätt multifaktorautentisering. Se till att besvara följande frågor:

* Där användarna ska finnas?
* Kan de finnas var som helst?
* Vill ditt företag att upprätta begränsningar enligt användarens plats?

När du förstår dessa krav, är det viktigt att utvärdera användarens krav för multifaktorautentisering. Den här utvärderingsversionen är viktig eftersom den definierar kraven för distribution av multifaktorautentisering. Se till att besvara följande frågor:

* Är användarna bekant med Multi-Factor authentication?
* Några användningsområden ombedd att ange ytterligare autentisering?  
  * Om Ja, hela tiden, när de kommer från externa nätverk eller få åtkomst till specifika program eller andra villkor?
* Användare kräver utbildning om hur du konfigurerar och implementera multifaktorautentisering?
* Vilka är de scenarier som företaget vill aktivera multifaktorautentisering för sina användare?

När du har svarat på tidigare frågor, kommer du att kunna se om det finns multifaktorautentisering redan implementerat den lokala. Den här utvärderingsversionen är viktigt att definiera de tekniska kraven för att konfigurera och aktivera organisationer användare för multifaktorautentisering. Se till att besvara följande frågor:

* Behöver företaget att skydda Privilegierade konton med MFA?
* Behöver företaget att aktivera MFA för vissa program av kompatibilitetsskäl?
* Behöver företaget att aktivera MFA för alla berättigade användare av dessa program eller bara administratörer?
* Du behöver har alltid aktiverat MFA eller bara när användare är inloggad utanför företagets nätverk?

## <a name="next-steps"></a>Nästa steg
[Definiera en hybrid identity införandestrategi](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

