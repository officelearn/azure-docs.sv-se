---
title: Felsöka dynamiskt medlemskap för grupper | Microsoft Docs
description: Felsökningstips för dynamiskt medlemskap för grupper i Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e189fb8b2bc5079d1560d3b7a54fea2db7366fe7
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46293987"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Felsöka dynamiska medlemskap för grupper

**Jag har konfigurerat en regel för en grupp, men inget medlemskap uppdateras i gruppen**<br/>Kontrollera värdena för användarattribut på regeln: det finns användare som uppfyller regeln? Om allt ser bra ut, det kan ta lite tid att fylla i gruppen. Beroende på klientens storlek så kan det ta upp till 24 timmar att fylla gruppen första gången eller efter en regeländring.

**Jag har konfigurerat en regel, men nu de befintliga medlemmarna i regeln tas bort**<br/>Detta är förväntat. Befintliga medlemmar i gruppen tas bort när en regel är aktiverat eller ändrats. De användare som har returnerats från utvärdering av regeln läggs till som medlemmar i gruppen.

**Jag ser inte medlemskap ändras direkt när jag lägger till eller ändra en regel, varför inte?**<br/>Dedikerad medlemskapsutvärdering sker regelbundet i en asynkron bakgrundsprocess. Hur lång tid processen tar bestäms av antalet användare i din katalog och storleken på gruppen skapas till följd av regeln. Normalt ser kataloger med ett litet antal användare ändringarna av gruppmedlemskap i mindre än ett par minuter. Kataloger med ett stort antal användare kan ta 30 minuter eller längre tid att fylla i.

**Jag påträffade ett fel vid bearbetning av regel**<br/>I följande tabell visas vanliga dynamiskt medlemskap regeln fel och hur du åtgärdar dem.

| Regeln parsningsfel | Fel vid användning | Korrigerad användning |
| --- | --- | --- |
| Fel: Attributet stöds inte. |(user.invalidProperty - eq ”Value”) |(user.department - eq ”value”)<br/><br/>Kontrollera att attributet är på den [stöds egenskapslistan](groups-dynamic-membership.md#supported-properties). |
| Fel: Operatorn stöds inte på attribut. |(user.accountEnabled-innehåller SANT) |(user.accountEnabled - eq SANT)<br/><br/>Den operator som används stöds inte för egenskapstypen (i det här exemplet-innehåller kan inte användas på Skriv booleskt värde). Använd rätt operatorer för egenskapstypen. |
| Fel: Frågekompileringsfel. | 1. (user.department - eq ”försäljning”) (user.department - eq ”marknadsföring”)<br>2. (user.userPrincipalName-matchar ”*@domain.ext”) | 1. Operator saknas. Använd - och eller - eller två gå med i predikat<br>(user.department - eq ”försäljning”)- eller (user.department - eq ”marknadsföring”)<br>2. Fel i reguljärt uttryck som används med - matcha<br>(user.userPrincipalName-matchar ”. *@domain.ext”)<br>eller alternativt: (user.userPrincipalName-matchar ”@domain.ext$”) |

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
