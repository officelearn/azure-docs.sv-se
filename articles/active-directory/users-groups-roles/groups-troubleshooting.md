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
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: aaded644635ab93cef9323ad38d1d150b15fe743
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450303"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Felsöka dynamiska medlemskap för grupper
**Jag har konfigurerat en regel för en grupp, men inget medlemskap uppdateras i gruppen**<br/>Kontrollera värdena för användarattribut på regeln: det finns användare som uppfyller regeln? Om allt ser bra ut, det kan ta lite tid att fylla i gruppen. Beroende på klientens storlek så kan det ta upp till 24 timmar att fylla gruppen första gången eller efter en regeländring.

**Jag har konfigurerat en regel, men nu de befintliga medlemmarna i regeln tas bort**<br/>Detta är förväntat. Befintliga medlemmar i gruppen tas bort när en regel är aktiverat eller ändrats. De användare som har returnerats från utvärdering av regeln läggs till som medlemmar i gruppen.     

**Jag ser inte medlemskap ändras direkt när jag lägger till eller ändra en regel, varför inte?**<br/>Dedikerad medlemskapsutvärdering sker regelbundet i en asynkron bakgrundsprocess. Hur lång tid processen tar bestäms av antalet användare i din katalog och storleken på gruppen skapas till följd av regeln. Normalt ser kataloger med ett litet antal användare ändringarna av gruppmedlemskap i mindre än ett par minuter. Kataloger med ett stort antal användare kan ta 30 minuter eller längre tid att fylla i.

### <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../connect/active-directory-aadconnect.md)
