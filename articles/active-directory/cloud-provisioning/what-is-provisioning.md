---
title: Vad är identitets etablering med Azure AD? | Microsoft Docs
description: Beskriver översikt över identitets etablering.
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
ms.openlocfilehash: acde7f30649e03d44fd891c959b53113b0e46f51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793516"
---
# <a name="what-is-identity-provisioning"></a>Vad är identitets etablering?

Företag och organisationer blir i dag allt mer en blandning av lokala och molnbaserade program.  Användare behöver åtkomst till program både lokalt och i molnet. Det måste finnas en enda identitet i dessa olika program (både lokalt och i molnet).

Etablering är en process för att skapa ett objekt baserat på vissa villkor, så att objektet blir inaktuellt och ta bort objektet när villkoren inte längre uppfylls. Till exempel när en ny användare ansluter till din organisation, anges användaren i HR-systemet.  Vid detta tillfälle kan etableringen skapa ett motsvarande användar konto i molnet, i Active Directory och olika program som användaren behöver åtkomst till.  Detta gör att användaren kan starta arbetet och ha åtkomst till de program och system som de behöver på dag ett. 

![moln etablering](media/what-is-provisioning/cloud1.png)

Med avseende på Azure Active Directory kan etableringen delas upp i följande viktiga scenarier.  

- **[HR-driven etablering](#hr-driven-provisioning)**  
- **[App-etablering](#app-provisioning)**  
- **[Katalog etablering](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-driven etablering

![moln etablering](media/what-is-provisioning/cloud2.png)

Etableringen från HR till molnet innebär att du kan skapa objekt (användare, roller, grupper osv.) baserat på den information som finns i ditt HR-system.  

Det vanligaste scenariot är att när en ny medarbetare ansluts till företaget registreras de i HR-systemet.  När detta sker är de etablerade i molnet.  I det här fallet Azure AD.  Etableringen från HR kan avse följande scenarier. 

- **Anställning av nya anställda** – när en ny medarbetare läggs till i Cloud HR skapas ett användar konto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD, med Skriv-tillbaka till molnets e-postadress.
- **Uppdateringar av anställda och profiler** – när en medarbetar post uppdateras i moln HR (till exempel namn, titel eller chef) uppdateras deras användar konto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD.
- **Anställdas uppsägningar** – när en medarbetare avslutas i molnet, inaktive ras användar kontot automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD.
- **Anställdas återställningar** – när en medarbetare återställs i molnet kan deras gamla konto automatiskt återaktiveras eller etableras på nytt (beroende på dina önskemål) till Active Directory, Azure Active Directory och eventuellt Office 365 och andra SaaS-program som stöds av Azure AD.


## <a name="app-provisioning"></a>App-etablering

![moln etablering](media/what-is-provisioning/cloud3.png)

Med appens etablering ingår etableringen av användare och roller i de program som användaren behöver åtkomst till.  

Det vanligaste scenariot är att när en användare i Azure AD tillhandahålls till O365 eller Salesforce.

## <a name="directory-provisioning"></a>Katalog etablering

![moln etablering](media/what-is-provisioning/cloud4.png)

Lokal etablering innebär etablering från lokala källor (till exempel Active Directory) till Azure AD.  

Det vanligaste scenariot är att när en användare i Active Directory (AD) är etablerad i Azure AD.

Detta har gjorts genom Azure AD Connect Sync, Azure AD Connect moln etablering och Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)
