---
title: 'Azure AD Connect-synkronisering: aktivera AD-Papperskorgen | Microsoft Docs'
description: "Det här avsnittet rekommenderar användning av AD-Papperskorgen funktionen med Azure AD Connect."
services: active-directory
keywords: "AD-Papperskorgen, oavsiktlig borttagning, källfästpunkten"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: eb455477547f3db8245cf3601576eba9c6fdc56f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect-synkronisering: aktivera AD-Papperskorgen
Det rekommenderas att du aktiverar AD Papperskorgen för din lokala Active kataloger, som synkroniseras till Azure AD. 

Om du av misstag tar bort en lokal AD användarobjektet och återställning med hjälp av funktionen, Azure AD återställs motsvarande Azure AD-användarobjektet.  Information om funktionen för AD-Papperskorgen finns i artikeln [översikt över scenarier för att återställa borttagna Active Directory-objekt](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Fördelarna med att aktivera AD-Papperskorgen
Den här funktionen hjälper med att återställa användarobjekt i Azure AD genom att göra följande:

* Om du av misstag tar bort en lokal AD-användare objekt tas bort i nästa synkronisering cykel motsvarande Azure AD-användarobjektet. Som standard sparas Azure AD den borttagna Azure AD-användarobjektet i ej permanent borttagna tillstånd för 30 dagar.

* Om du har lokala AD Återanvänd Bin-funktionen aktiverad, kan du återställa den borttagna lokala AD-användarobjektet utan att ändra värdet Källfästpunkt. När den återställda lokalt AD användarobjektet synkroniseras till Azure AD Azure AD kommer återställning motsvarande ej permanent borttagna Azure AD-användarobjektet. Information om Källfästpunkten attribut finns i artikel [Azure AD Connect: Designbegreppen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Om du inte har lokala AD-Papperskorgen funktionen är aktiverad kan du bli ombedd att skapa en AD-användarobjektet för att ersätta det borttagna objektet. Om Azure AD Connect-synkroniseringstjänsten konfigureras för att använda AD systemgenererat attribut (till exempel ObjectGuid) för attributet Källfästpunkten nyligen skapade AD användarobjektet inte Källfästpunkten samma värde som AD-objekt för borttagna användare. När AD-objekt för nyligen skapade användaren synkroniseras till Azure AD Azure AD skapar en ny Azure AD-användarobjektet i stället för att återställa det ej permanent borttagna objektet för Azure AD-användare.

> [!NOTE]
> Som standard bort behåller Azure AD Azure AD-användarobjekt i ej permanent borttagna tillstånd i 30 dagar innan de tas bort permanent. Administratörer kan dock accelerera borttagningen av sådana objekt. När objekt tas bort permanent de inte längre kan återställas-, även om lokala AD-Papperskorgen är aktiverad.



## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
