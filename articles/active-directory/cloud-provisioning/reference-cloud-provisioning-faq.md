---
title: Vanliga frågor och svar om Azure AD Connect-molnetablering
description: Det här dokumentet beskriver vanliga frågor om moln etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc3c4a943f24ba1f987aa1daf513b9e05ada65a7
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310007"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – vanliga frågor och svar

Läs om vanliga frågor och svar om Azure Active Directory (Azure AD) Anslut till moln etablering.

## <a name="general-installation"></a>Allmän installation

**F: hur ofta körs moln etablering?**

Moln etablering är schemalagt att köras var 2: e minut. Var 2: e minut, kommer alla användare, grupp-och lösen ords-hash-ändringar att tillhandahållas till Azure AD.

**F: se synkroniseringsfel vid synkronisering av lösen ord vid första körningen. Varför?**

Detta är normalt. Felen beror på att användarobjektet inte finns i Azure AD. När användaren har allokerats till Azure AD bör hash-värden för lösen ord tillhandahållas i den efterföljande körningen. Vänta på några körningar och bekräfta att det inte längre finns några fel i lösen ordets hash-synkronisering.

**F: Vad är skillnaden mellan Azure AD Connect synkronisering och moln etablering?**

Med Azure AD Connect Sync körs etableringen på den lokala Sync-servern. Konfigurationen lagras på den lokala Sync-servern. Med Azure AD Connect Cloud etableringen lagras etablerings konfigurationen i molnet och körs i molnet som en del av Azure AD Provisioning-tjänsten. 

**F: kan jag använda moln etablering för att synkronisera från flera Active Directory skogar?**

Ja. Moln etablering kan användas för att synkronisera från flera Active Directory skogar. I miljöer med flera skogar måste alla referenser (t. ex. hanteraren) finnas i domänen.  

**F: Hur uppdateras agenten?**

Agenterna uppgraderas automatiskt av Microsoft. För IT-teamet minskar detta belastningen på att testa och validera nya agent versioner. 

**F: kan jag inaktivera automatisk uppgradering?**

Det finns inget stöd för att inaktivera automatisk uppgradering.

**F: kan jag ändra käll ankare för moln etablering?**

Som standard använder moln etablering ms-DS-konsekvens-GUID med en återställning till ObjectGUID som käll ankare. Det finns inget stöd för att ändra käll ankare.

**F: Jag ser nya tjänst huvud namn med AD-domännamnen när de använder moln etablering. Förväntas det?**

Ja, Cloud-etablering skapar ett huvud namn för etablerings konfigurationen med domän namnet som tjänstens huvud namn. Gör inga ändringar i tjänstens huvud konfiguration.

**F: Vad händer när en synkroniserad användare krävs för att ändra lösen ord vid nästa inloggning?**

Om lösen ordets hash-synkronisering är aktiverat i moln etableringen och den synkroniserade användaren krävs för att ändra lösen ord vid nästa inloggning i den lokala AD-miljön, etablerar inte moln etableringen om att ändra lösen ordets hash-värde till Azure AD. När användaren har ändrat lösen ordet, tillhandahålls hashen för användarens lösen ord från AD till Azure AD.

**F: har moln etableringen stöd för tillbakaskrivning av ms-DS-consistencyGUID för alla objekt?**

Nej, moln etableringen stöder inte tillbakaskrivning av ms-DS-consistencyGUID för alla objekt (inklusive användar objekt). 

**F: Jag är etablerad användare som använder moln etablering. Jag har tagit bort konfigurationen. Varför ser jag fortfarande de gamla synkroniserade objekten i Azure AD?** 

När du tar bort konfigurationen rensar moln etableringen inte de synkroniserade objekten i Azure AD. Se till att du inte har de gamla objekten genom att ändra omfånget för konfigurationen till en tom grupp eller organisationsenheter. När etableringen körs och rensar objekten inaktiverar du och tar bort konfigurationen. 

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)
