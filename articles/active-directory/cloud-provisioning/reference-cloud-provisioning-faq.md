---
title: Vanliga frågor och svar om Azure AD Connect-molnetablering
description: Det här dokumentet beskriver vanliga frågor om moln etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcf2df4e472d022fcdec0c9b7c69c73192c503f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518478"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Vanliga frågor och svar om Azure Active Directory Connect Cloud-etablering

Läs om vanliga frågor och svar om Azure Active Directory (Azure AD) Anslut till moln etablering.

## <a name="general-installation"></a>Allmän installation

**F: hur ofta körs moln etablering?**

Moln etablering är schemalagt att köras var 2: e minut. Var 2: e minut, kommer alla användare, grupp-och lösen ords-hash-ändringar att tillhandahållas till Azure AD.

**F: se synkroniseringsfel vid synkronisering av lösen ord vid första körningen. Varför?**

Detta är förväntat. Felen beror på att användarobjektet inte finns i Azure AD. När användaren har allokerats till Azure AD bör hash-värden för lösen ord tillhandahållas i den efterföljande körningen. Vänta på några körningar och bekräfta att det inte längre finns några fel i lösen ordets hash-synkronisering.

**F: Vad händer om Active Directory-instansen har attribut som inte stöds av moln etablering (till exempel katalog tillägg)?**

Moln etablering kommer att köras och etablera de attribut som stöds. Attribut som inte stöds är inte etablerade i Azure AD. Granska katalog tilläggen i Active Directory och se till att du inte behöver de attributen för att flöda till Azure AD. Om det krävs ett eller flera attribut kan du överväga att använda Azure AD Connect synkronisera eller flytta nödvändig information till ett av de attribut som stöds (t. ex. tilläggets attribut 1-15).

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

Om lösen ordets hash-synkronisering är aktive rad i moln etableringen och den synkroniserade användaren krävs för att ändra lösen ord vid nästa inloggning i den lokala AD-miljön, etablerar inte moln etableringen "to-changed"-lösen ordet hash till Azure AD. När användaren har ändrat lösen ordet, tillhandahålls hashen för användarens lösen ord från AD till Azure AD.

**F: har moln etableringen stöd för tillbakaskrivning av ms-DS-consistencyGUID för alla objekt?**

Nej, moln etableringen stöder inte tillbakaskrivning av ms-DS-consistencyGUID för alla objekt (inklusive användar objekt). 

**F: Jag är etablerad användare som använder moln etablering. Jag har tagit bort konfigurationen. Varför ser jag fortfarande de gamla synkroniserade objekten i Azure AD?** 

När du tar bort konfigurationen tar moln etableringen inte bort de synkroniserade objekten automatiskt i Azure AD. Se till att du inte har de gamla objekten genom att ändra omfånget för konfigurationen till en tom grupp eller organisationsenheter. När etableringen körs och rensar objekten inaktiverar du och tar bort konfigurationen. 

**F: Vad betyder det att Exchange hybrid inte stöds?**

Funktionen Exchange-hybridinstallation gör att Exchange-postlådor kan samexistera lokalt och i Office 365. Azure AD Connect synkroniserar en specifik uppsättning attribut från Azure AD tillbaka till din lokala katalog.  Moln Provisioning-agenten synkroniserar för närvarande inte de här attributen i din lokala katalog och stöds därför inte som en ersättning för Azure AD Connect.

**F: kan jag installera moln etablerings agenten på Windows Server Core?**

Nej, det finns inte stöd för att installera agenten på Server Core.

**F: kan jag använda en uppsamlings server med moln etablerings agenten?**

Nej, det finns inte stöd för mellanlagrings servrar.

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
