---
title: 'Azure AD Connect-synkronisering: Aktivera AD-Papperskorgen | Microsoft Docs'
description: Det här avsnittet rekommenderar användning av Papperskorgen i Active Directory-funktionen med Azure AD Connect.
services: active-directory
keywords: Papperskorgen i Active Directory, oavsiktlig borttagning, källfästpunkt
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4836ffc8c6ab013ef5ad7a661db0df3254b6d4e1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468809"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect-synkronisering: Aktivera AD-papperskorgen
Vi rekommenderar att du aktiverar Papperskorgen i Active Directory-funktionen för din lokala Active-kataloger som synkroniseras till Azure AD. 

Om du av misstag tar bort en lokal AD user-objektet och återställning med hjälp av funktionen, Azure AD återställs motsvarande användarobjekt i Azure AD.  Information om funktionen Papperskorgen i Active Directory finns i artikeln [Scenarioöversikt för återställning av borttagna Active Directory objekt](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Fördelarna med att aktivera AD-Papperskorgen
Den här funktionen hjälper med att återställa objekt för Azure AD genom att göra följande:

* Om du av misstag tar bort en lokal AD-användare objekt för motsvarande Azure AD user-objektet kommer att tas bort i nästa synkroniseringscykel. Som standard Azure AD sparas den borttagna Azure AD-användarobjektet i ej permanent borttagna tillstånd i 30 dagar.

* Om du har en lokal AD återvinning Bin funktionen är aktiverad kan du återställa den borttagna lokala AD user-objektet utan att ändra dess Källfästpunkt-värde. När de återställda lokalerna AD-användarobjektet synkroniseras till Azure AD, Azure AD kommer återställningen motsvarande ej permanent borttagna Azure AD user-objektet. Information om källfästpunktsattributet finns i artikeln [Azure AD Connect: Designbegrepp](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Om du inte har en lokal AD-Papperskorgen funktionen är aktiverad kan du bli ombedd att skapa ett användarobjekt i AD för att ersätta det borttagna objektet. Om Azure AD Connect-synkroniseringstjänsten har konfigurerats för att använda en systemgenererad AD-attribut (till exempel ObjectGuid) för källfästpunktsattributet för nyligen skapade AD user-objektet inte samma Källankare värde som det borttagna objektet för AD-användare. När AD nyligen skapade objektet synkroniseras till Azure AD, Azure AD skapar en ny Azure AD user-objektet i stället för att återställa det ej permanent borttagna objektet för Azure AD-användare.

> [!NOTE]
> Som standard bort Azure AD-användarobjekt i ej permanent borttagna tillstånd i Azure AD är fortfarande i 30 dagar innan de tas bort permanent. Administratörer kan dock snabbare borttagningen av sådana objekt. När objekt tas bort permanent, de inte längre kan återställas, även om lokala Papperskorgen i Active Directory-funktionen är aktiverad.

>[!NOTE]
>När en användare har tagits bort från den lokala synkronisering omfång och tas bort i molnet, har tidigare DirSyncEnabled status för konton som felaktigt angetts till ”False”. Om du därefter som användaren har återställts manuellt från Azure AD-Papperskorgen, visade ett felaktigt tillstånd för ”endast molnet”-kontot. Detta har nu åtgärdats och värdet för DirSyncEnabled status är alltid hålls ”True” när en användare tas bort från sync omfattning, sedan ej permanent borttagna och manuellt kan återställas från Azure AD-Papperskorgen.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
