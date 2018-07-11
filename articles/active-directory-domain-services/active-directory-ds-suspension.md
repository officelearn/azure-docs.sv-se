---
title: 'Azure Active Directory Domain Services: Pausats domäner | Microsoft Docs'
description: Hanterade domänen inaktivering och borttagning
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: ergreenl
ms.openlocfilehash: 37524fbdf3cd521414a507f6fb67421708343ccd
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934250"
---
# <a name="suspended-domains"></a>Avbrutna domäner
När Azure AD Domain Services är inte hantera en hanterad domän för en längre tidsperiod, placera den hanterade domänen i ett väntetillstånd. Den här artikeln förklarar varför hanterade domäner pausas, längden på inaktivering och hur du åtgärdar en pausad domän.


## <a name="overview-of-suspended-domains"></a>Översikt över pausat domäner

![Avbrutna domän tidslinje](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Föregående bild visar hur en domän har pausats, hur länge den inaktiveras och slutligen borttagningen av en hanterad domän. Följande avsnitt beskriver de orsaker till varför en domän kan stängas av och hur du med att aktivera en hanterad domän.


## <a name="why-are-managed-domains-suspended"></a>Varför pausas hanterade domäner?

Hanterade domäner gör uppehåll när de är i ett tillstånd där Azure AD Domain Services är inte hantera domänen. Detta kan ha orsakats av en felkonfiguration som blockerar åtkomsten till resurser som krävs av Azure AD Domain Services eller en inaktiv Azure-prenumeration. Efter 15 dagar från det att det inte går att underhålla en hanterad domän att Azure AD Domain Services pausas domänen.

De exakta orsaker varför det gick att pausa din domän visas nedan:
* Med en eller flera av följande aviseringar finns på din domän för 15 dagar i rad:
   * [AADDS104: Nätverksfel](active-directory-ds-troubleshoot-nsg.md).
* Din Azure-prenumeration har upphört att gälla eller är inaktiv


## <a name="what-happens-when-a-domain-is-suspended"></a>Vad händer när en domän är inaktiverad?

När en domän pausas, slutar de virtuella datorerna som tjänsten din hanterade domän Azure AD Domain Services. Det innebär att säkerhetskopior tas inte längre, användare kan inte logga in på din domän och synkronisering med Azure AD utförs inte längre.

Domänen bara kvar i ett tillfälligt tillstånd för högst 15 dagar. För att säkerställa en tid för återställning, bör du lösa inaktiveringen så snart som möjligt.

## <a name="how-do-i-know-if-my-domain-is-suspended"></a>Hur vet jag om min domän pausas?
Den hanterade domänen får en [avisering](active-directory-ds-troubleshoot-alerts.md) på sidan Azure AD Domain Services Health i Azure-portalen som deklarerar domänen har pausats. Dessutom kommer tillståndet för domänen märkas ”pausad”.


## <a name="unsuspending-and-restoring-domains"></a>Uppehåll och återställa domäner

Om du vill aktivera en domän, krävs följande:

1. Navigera till den [Azure AD Domain Services-sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) på Azure portal
2. Klicka på den domän som du vill aktivera
3. I det vänstra navigeringsfönstret klickar du på **hälsotillstånd**
4. Klicka på aviseringen inaktivering (aviserings-ID är antingen AADDS503 eller AADDS504, beroende på orsaken till inaktivering).
5. Klicka på länken lösning i aviseringen och följ stegen för att lösa dina inaktivering.

Din domän kan bara återställas från datumet för senaste säkerhetskopieringen. Datumet då den senaste säkerhetskopieringen visas på hälsosidan för den hanterade domänen. Ändringar sedan den senaste säkerhetskopieringen återställs inte vid unsuspension. Azure AD Domain Services kan dessutom endast lagra säkerhetskopior för upp till 30 dagar. Om den senaste säkerhetskopian är äldre än 30 dagar, säkerhetskopieringen måste tas bort och Azure AD Domain Services kommer inte att återställa från en säkerhetskopia.

## <a name="deleting-domains"></a>Ta bort domäner

Om domänen har inaktiverats i mer än 15 dagar, Azure AD Domain Services tar du bort den hanterade domänen på grund av inaktivitet och oförmåga att fullgöra domänen. Du kommer inte längre att debiteras för Azure AD Domain Services. Om du vill återställa din hanterade domän, behöver du återskapa den.


## <a name="next-steps"></a>Nästa steg
- [Lös aviseringar på din hanterade domän](active-directory-ds-troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontakta produktteamet](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontakta oss

Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
