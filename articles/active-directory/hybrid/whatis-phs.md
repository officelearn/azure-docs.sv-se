---
title: Vad är synkronisering av lösenordshash med Azure AD? | Microsoft Docs
description: Beskriver synkronisering av lösenordshash.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 568f284a22f170c2a6a0d60d9e2cfed0645937ba
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261383"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Vad är synkronisering av lösenordshash med Azure AD?
Synkronisering av lösenordshash är en av inloggningsmetoderna som används för att utföra hybrid-identitet. Azure AD Connect synkroniserar en hash-hash för en användares lösen ord från en lokal Active Directory instans till en molnbaserad Azure AD-instans.

Lösenordssynkronisering är ett tillägg till funktionen katalog synkronisering som implementeras av Azure AD Connect Sync. Du kan använda den här funktionen för att logga in på Azure AD-tjänster som Office 365. Du loggar in till tjänsten genom att använda samma lösenord som du använder för att logga in på din lokala Active Directory-instans.

![Vad är Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synkronisering av lösenordshash hjälper till att minska antalet lösenord. Dina användare behöver bara underhålla ett. Synkronisering av lösenordshash kan:

* Förbättra användarnas produktivitet.
* Minska supportkostnaderna.  

Om du bestämmer dig för att använda federation med [Federation with Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) kan du även konfigurera synkronisering av hashlösenord som en reservåtgärd.

Om du vill använda synkronisering av lösenordshash i din miljö måste du:

* Installera Azure AD Connect.  
* Konfigurera katalogsynkronisering mellan din lokala Active Directory-instans och din Azure Active Directory-instans.
* Aktivera hashsynkronisering för lösenord.



Mer information finns i [Vad är hybrididentitet?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Efterföljande moment

- [Vad är hybrididentitet?](whatis-hybrid-identity.md)
- [Vad är Azure AD Connect och Connect Health?](whatis-azure-ad-connect.md)
- [Vad är direktautentisering (PTA)?](how-to-connect-pta.md)
- [Vad är federation?](whatis-fed.md)
- [Vad är enkel inloggning?](how-to-connect-sso.md)
- [Så här fungerar hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)
