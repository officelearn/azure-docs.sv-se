---
title: Vad är identitetsetablering med Azure AD? | Microsoft Docs
description: Beskriver översikt över identitetsetablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28513c57101af67695d10056b3dc8e6537dcddb2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76712549"
---
# <a name="what-is-identity-provisioning"></a>Vad är identitetsetablering?

Företag och organisationer blir i dag allt mer en blandning av lokala och molnbaserade program.  Användare kräver åtkomst till program både lokalt och i molnet. Det finns behov av att ha en enda identitet i dessa olika program (lokalt och i molnet).

Etablering är processen att skapa ett objekt baserat på vissa villkor, hålla objektet uppdaterat och ta bort objektet när villkoren inte längre uppfylls. När en ny användare till exempel ansluter till din organisation anges den användaren i HR-systemet.  Då kan etablering skapa ett motsvarande användarkonto i molnet, i Active Directory och olika program som användaren behöver åtkomst till.  Detta gör det möjligt för användaren att börja arbeta och har tillgång till de program och system de behöver på dag ett. 

![molnetablering](media/what-is-provisioning/cloud1.png)

När det gäller Azure Active Directory kan etablering delas upp i följande nyckelscenarier.  

- **[HR-driven etablering](#hr-driven-provisioning)**  
- **[Etablering av appar](#app-provisioning)**  
- **[Katalogtablering](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-driven etablering

![molnetablering](media/what-is-provisioning/cloud2.png)

Etablering från HR till molnet innebär att skapa objekt (användare, roller, grupper osv.) baserat på den information som finns i ditt HR-system.  

Det vanligaste scenariot är att när en ny medarbetare ansluter sig till ditt företag, de anges i HR-systemet.  När det inträffar etableras de till molnet.  I det här fallet Azure AD.  Etablering från HR kan omfatta följande scenarier. 

- **Anställa nya medarbetare** – När en ny medarbetare läggs till i moln-HR skapas ett användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD, med tillbakaskrivning av e-postadressen till Cloud HR.
- **Medarbetarattribut och profiluppdateringar** – När en medarbetarpost uppdateras i moln-HR (till exempel namn, titel eller chef) uppdateras deras användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD.
- **Uppsägning av medarbetare** - När en medarbetare sägs upp i moln-HR inaktiveras deras användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD.
- **Medarbetare återanställer** - När en medarbetare återanställs i moln-HR kan deras gamla konto automatiskt återaktiveras eller återupprättas (beroende på dina önskemål) till Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD.


## <a name="app-provisioning"></a>Etablering av appar

![molnetablering](media/what-is-provisioning/cloud3.png)

I Azure Active Directory (Azure AD) avser termen **[appetablering](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** att automatiskt skapa användaridentiteter och roller i molnprogrammen som användarna behöver åtkomst till. Förutom att skapa användaridentiteter, automatisk etablering inkluderar underhåll och borttagning av användaridentiteter som status eller roller förändras. Vanliga scenarier är att etablera en Azure AD-användare i program som [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)med mera.

## <a name="directory-provisioning"></a>Katalogtablering

![molnetablering](media/what-is-provisioning/cloud4.png)

Lokal etablering innebär etablering från lokala källor (som Active Directory) till Azure AD.  

Det vanligaste scenariot skulle vara när en användare i Active Directory (AD) etableras i Azure AD.

Detta har utförts av Azure AD Connect sync, Azure AD Connect molnetablering och Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
- [Installera molnetablering](how-to-install.md)
