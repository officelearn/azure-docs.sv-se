---
title: Identitet hybridutformning - krav för multifaktorautentisering Azure | Microsoft Docs
description: Med villkorlig åtkomstkontroll kontrollerar de särskilda villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet i Azure Active Directory. När dessa villkor är uppfyllda, autentiserade användaren och få tillgång till programmet.
documentationcenter: ''
services: active-directory
author: femila
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: fe5ac8386394bf659de8d9905827359900f08d93
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801042"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Ange krav för multifaktorautentisering för dina hybrididentitetslösning
I den här värld mobilitet, med användare som ansluter till data och program i molnet och från valfri enhet, blivit skydda den här informationen ytterst viktigt.  Varje dag är det en ny rubrik om ett intrång.  Även om det är inte säkert mot sådana överträdelser, ger multifaktorautentisering, ett ytterligare lager av säkerhet för att förhindra att dessa överträdelser.
Börja med att utvärdera organisationer kraven för multifaktorautentisering. Det vill säga vad organisationen försöker skydda.  Denna utvärdering är viktigt att definiera de tekniska kraven för att installera och aktivera organisationer användare för multifaktorautentisering.

> [!NOTE]
> Om du inte är bekant med MFA och syfte, rekommenderas du läsa artikeln [vad är Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) före fortsätta läsa det här avsnittet.
> 
> 

Se till att svara på följande:

* Ditt företag försöker skydda Microsoft-appar? 
* Hur dessa appar är publicerade?
* Företaget kan ge fjärråtkomst så att anställda åtkomst till lokala appar?

Om Ja, vilken typ av fjärråtkomst? Du måste utvärdera där användare som kommer åt dessa program kommer att finnas. Det här är ett annat viktigt steg för att definiera en strategi för rätt multifaktorautentisering. Se till att besvara följande frågor:

* Om användarna ska finnas?
* Kan de finnas var som helst?
* Vill företaget upprätta begränsningar enligt användarens plats?

När du förstår dessa krav är det viktigt att du även utvärdera användarens krav för multifaktorautentisering. Denna utvärdering är viktig eftersom den definierar kraven för lansera multifaktorautentisering. Se till att besvara följande frågor:

* Är användare som är bekanta med Multi-Factor authentication?
* Vissa använder ombedd att ange ytterligare autentisering?  
  * Om Ja, tid när kommer från externa nätverk eller vid åtkomst till specifika program eller andra villkor?
* Kommer användarna utbildning att konfigurera och implementera multifaktorautentisering?
* Vilka är de scenarier som företaget vill aktivera Multi-Factor authentication för användarna?

Efter föregående frågor, kommer du att kunna förstå om multifaktorautentisering redan implementerats lokala. Denna utvärdering är viktigt att definiera de tekniska kraven för att installera och aktivera organisationer användare för multifaktorautentisering. Se till att besvara följande frågor:

* Behöver företaget skydda Privilegierade konton med MFA?
* Behöver ditt företag att aktivera MFA för vissa program på grund av kompatibilitetsskäl?
* Behöver ditt företag att aktivera MFA för alla berättigade användare av dessa program eller endast administratörer?
* Du behöver har alltid aktiverat MFA eller bara när användare är inloggade utanför företagets nätverk?

## <a name="next-steps"></a>Nästa steg
[Definiera en strategi för införandet en hybrid identity](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

