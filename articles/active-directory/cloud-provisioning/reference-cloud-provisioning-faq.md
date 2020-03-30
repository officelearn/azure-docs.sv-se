---
title: Vanliga frågor och svar om Azure AD Connect-molnetablering
description: Det här dokumentet beskriver vanliga frågor och svar för molnetablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77916582"
---
# <a name="azure-active-directory-connect-faq"></a>Vanliga frågor och svar om Azure Active Directory Connect

Läs om vanliga frågor och svar för Azure Active Directory (Azure AD) Connect-molnetablering.

## <a name="general-installation"></a>Allmän installation

**F: Hur ofta körs molnetablering?**

Molnetablering är schemalagd att köras varannan minut. Varje 2 minuter, alla användare, grupper och lösenord hash ändringar kommer att etableras till Azure AD.

**F: Visa synkroniseringsfel för lösenord hash-synkronisering vid den första körningen. Varför?**

Detta är normalt. Felen beror på att användarobjektet inte finns i Azure AD. När användaren har etablerats till Azure AD bör lösenordshashar etableras i den efterföljande körningen. Vänta på ett par körningar och bekräfta att lösenord hash-synkronisering inte längre har felen.

**F: Vad händer om Active Directory-instansen har attribut som inte stöds av molnprovisonering (till exempel katalogtillägg)?**

Molnetablering körs och etablerar attribut som stöds. Attributen som inte stöds kommer inte att etableras till Azure AD. Granska katalogtilläggen i Active Directory och se till att du inte behöver dessa attribut för att flöda till Azure AD. Om ett eller flera attribut krävs kan du överväga att använda Azure AD Connect-synkronisering eller flytta den information som krävs till ett av de attribut som stöds (till exempel tilläggsattribut 1-15).

**F: Vad är skillnaden mellan Azure AD Connect-synkronisering och molnetablering?**

Med Azure AD Connect-synkronisering körs etablering på den lokala synkroniseringsservern. Konfigurationen lagras på den lokala synkroniseringsservern. Med Azure AD Connect-molnetablering lagras etableringskonfigurationen i molnet och körs i molnet som en del av Azure AD-etableringstjänsten. 

**F: Kan jag använda molnetablering för synkronisering från flera Active Directory-skogar?**

Ja. Molnetablering kan användas för synkronisering från flera Active Directory-skogar. I flerskogsmiljön måste alla referenser (exempel, chef) finnas inom domänen.  

**F: Hur uppdateras agenten?**

Agenterna uppgraderas automatiskt av Microsoft. För IT-teamet minskar detta bördan av att behöva testa och validera nya agentversioner. 

**F: Kan jag inaktivera automatisk uppgradering?**

Det finns inget sätt att inaktivera automatisk uppgradering.

**F: Kan jag ändra källankaret för molnetablering?**

Som standard använder molnetablering ms-ds-consistency-GUID med en återgång till ObjectGUID som källankare. Det finns inget sätt att ändra källankaret.

**F: Jag ser nya tjänsthuvudnamn med AD-domännamnen när du använder molnetablering. Är det väntat?**

Ja, molnetablering skapar ett tjänsthuvudnamn för etableringskonfigurationen med domännamnet som tjänstens huvudnamn. Gör inga ändringar i tjänstens huvudkonfiguration.

**F: Vad händer när en synkroniserad användare måste ändra lösenord vid nästa inloggning?**

Om synkronisering av lösenord hash är aktiverad i molnetablering och den synkroniserade användaren krävs för att ändra lösenord vid nästa inloggning i lokal AD, etablerar inte molnetablering som ska ändras lösenordshh till Azure AD. När användaren har ändrat lösenordet etableras användarens lösenordshh från AD till Azure AD.

**F: Stöder molnetablering tillbakaskrivning av ms-ds-consistencyGUID för alla objekt?**

Nej, molnetablering stöder inte tillbakaskrivning av ms-ds-consistencyGUID för alla objekt (inklusive användarobjekt). 

**F: Jag etablerar användare med hjälp av molnetablering. Jag har tagit bort konfigurationen. Varför ser jag fortfarande de gamla synkroniserade objekten i Azure AD?** 

När du tar bort konfigurationen rensas inte de synkroniserade objekten i Azure AD. Om du vill vara säkra på att du inte har de gamla objekten ändrar du konfigurationens omfattning till en tom grupp eller organisationsenheter. När etableringen körs och rensas objekten inaktiverar och tar du bort konfigurationen. 

**F: Vad betyder det att Exchange hybrid inte stöds?**

Funktionen Exchange-hybridinstallation gör att Exchange-postlådor kan samexistera lokalt och i Office 365. Azure AD Connect synkroniserar en specifik uppsättning attribut från Azure AD tillbaka till din lokala katalog.  Molnetableringsagenten synkroniserar för närvarande inte tillbaka dessa attribut till din lokala katalog och stöds därför inte som ersättning för Azure AD Connect.

**F: Kan jag installera molnetableringsagenten i Windows Server Core?**

Nej, det går inte att installera agenten på serverkärna.

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
