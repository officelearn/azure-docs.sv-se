---
title: Felsöka villkorlig åtkomst med verktyget Vad händer - Azure Active Directory
description: Var du hittar vilka principer för villkorlig åtkomst som tillämpades och varför
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175798"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Felsöka villkorlig åtkomst med verktyget Vad händer

[Verktyget Vad händer om](what-if-tool.md) i villkorlig åtkomst är kraftfullt när du försöker förstå varför en princip har tillämpats eller inte tillämpades på en användare under en viss omständighet eller om en princip skulle gälla i ett känt tillstånd.

Verktyget Vad händer om finns i **Azure Portal** > **Azure Active Directory** > **Villkorlig åtkomst** > **Vad händer om**.

![Villkorad åtkomst vad händer om-verktyget i standardtillstånd](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Verktyget Vad händer utvärderar för närvarande inte principer i rapportläge.

## <a name="gathering-information"></a>Samla in information

Verktyget Vad händer om kräver bara en **användare** för att komma igång. 

Följande ytterligare information är valfri, men kommer att bidra till att begränsa utrymmet för specifika fall.

* Molnappar eller åtgärder
* IP-adress 
* Land/region
* Enhetsplattform
* Klientappar (förhandsgranskning)
* Enhetstillstånd (förhandsgranskning) 
* Inanmälningsrisk

Den här informationen kan samlas in från användaren, deras enhet eller Azure AD-inloggningsloggen.

## <a name="generating-results"></a>Generera resultat

Ange de kriterier som samlats in i föregående avsnitt och välj **Vad händer om** du vill generera en resultatlista. 

När som helst kan du välja **Återställ** för att rensa alla villkor indata och återgå till standardtillståndet.

## <a name="evaluating-results"></a>Utvärdera resultat

### <a name="policies-that-will-apply"></a>Policyer som kommer att gälla

Den här listan visar vilka principer för villkorlig åtkomst som skulle gälla med tanke på villkoren. Listan innehåller både de bidrags- och sessionskontroller som gäller. Exempel på detta är att kräva multifaktorautentisering för att komma åt ett visst program.

### <a name="policies-that-will-not-apply"></a>Policyer som inte gäller

Den här listan visar principer för villkorlig åtkomst som inte skulle gälla om villkoren tillämpas. Listan innehåller alla principer och anledningen till att de inte gäller. Exempel är användare och grupper som kan uteslutas från en princip.

## <a name="use-case"></a>Användningsfall

Många organisationer skapar principer baserat på nätverksplatser, tillåter betrodda platser och blockerar platser där åtkomst inte ska ske.

För att verifiera att en konfiguration har gjorts på rätt sätt kan en administratör använda verktyget Vad händer om för att efterlikna åtkomst, från en plats som ska tillåtas och från en plats som ska nekas.

![Vad händer om-verktyget som visar resultat med Blockera åtkomst](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

I det här fallet skulle användaren blockeras från att komma åt någon molnapp på sin resa till Nordkorea eftersom Contoso har blockerat åtkomst från den platsen.

Det här testet kan utökas till att omfatta andra datapunkter för att begränsa omfattningen.

## <a name="next-steps"></a>Nästa steg

* [Vad är villkorlig åtkomst?](overview.md)
* [Vad är Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Vad är en enhetsidentitet?](../devices/overview.md)
* [Så här fungerar Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
