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
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 99896b0f618151cd368a21c4a212b7d8e28b7a6f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126101"
---
# <a name="suspended-domains"></a>Avbrutna domäner
När Azure AD Domain Services är inte hantera en hanterad domän för en längre tidsperiod, placera den hanterade domänen i ett väntetillstånd. Den här artikeln förklarar varför hanterade domäner pausas och hur du åtgärdar en pausad domän.


## <a name="states-your-managed-domain-can-be-in"></a>Tillstånd den hanterade domänen kan vara i

![Avbrutna domän tidslinje](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Föregående bild beskrivs möjliga tillstånd kan ha en Azure AD Domain Services-hanterad domän.

### <a name="running-state"></a>”Kör” tillstånd
En hanterad domän som är korrekt konfigurerad och körs regelbundet finns i den **kör** tillstånd.

**Vad du kan förvänta dig:**
* Microsoft kan regelbundet övervaka hälsotillståndet för din hanterade domän.
* Domänkontrollanter för din hanterade domän är korrigeras och uppdateras regelbundet.
* Ändringar från Azure Active Directory synkroniseras regelbundet till din hanterade domän.
* Regelbundna säkerhetskopieringar görs för din hanterade domän.


### <a name="needs-attention-state"></a>'Behöver åtgärdas ”tillstånd
En hanterad domän finns i den **Värdstatus** tillstånd, om ett eller flera problem kräver en administratör kan vidta åtgärder. Hälsotillståndssidan för den hanterade domänen visas en lista över en eller flera aviseringar i det här tillståndet. Om du har konfigurerat en begränsande NSG för det virtuella nätverket, till exempel vara Microsoft det går inte att uppdatera och övervaka din hanterade domän. Den här ogiltig konfiguration resulterar i en avisering som genererats och att den hanterade domänen behöver kontrolleras tillståndet.

Varje avisering har en uppsättning Lösningssteg. Vissa aviseringar är tillfälligt och kommer att få automatiskt löst av tjänsten. Du kan lösa vissa andra aviseringar genom att följa anvisningarna i motsvarande åtgärderna i den här aviseringen. Du måste kontakta Microsoft-supporten för att lösa några viktiga aviseringar.

Mer information finns i [felsökning av aviseringar på en hanterad domän](active-directory-ds-troubleshoot-alerts.md).

**Vad du kan förvänta dig:**

I vissa fall (t.ex, om du har en ogiltig konfiguration) kanske domänkontrollanterna för den hanterade domänen inte kan nås. Microsoft kan inte garantera din hanterade domän är övervakas, korrigeringar, uppdateras eller säkerhetskopieras regelbundet i det här tillståndet.

* Den hanterade domänen är i ett feltillstånd och pågående hälsoövervakning slutar förrän aviseringen har lösts.
* Domänkontrollanterna för den hanterade domänen kan inte korrigeras eller uppdateras.
* Ändringar från Azure Active Directory kan inte synkroniseras till din hanterade domän.
* Säkerhetskopieringar för den hanterade domänen kan tas om möjligt.
* Om du har löst aviseringarna som påverkar din hanterade domän kan det vara möjligt att återställa din hanterade domän till tillståndet ”körs”.
* Kritiska aviseringar initieras för konfigurationsproblem där Microsoft kan inte nå domänkontrollanterna. Om dessa aviseringar inte är åtgärdats inom 15 dagar, placeras din hanterade domän statusen ”Pausad”.


### <a name="suspended-state"></a>' Väntetillstånd '
En hanterad domän placeras i den **pausad** tillstånd av följande skäl:
* En eller flera kritiska varningar inte har lösts i 15 dagar. Kritiska aviseringar kan ha orsakats av en felkonfiguration som blockerar åtkomsten till resurser som krävs av Azure AD Domain Services.
    * Exempel: om den hanterade domänen har avisering [AADDS104: nätverksfel](active-directory-ds-troubleshoot-nsg.md) olöst mer än 15 dagar.
* Det finns ett fakturering problem med din Azure-prenumeration eller din Azure-prenumeration har upphört att gälla.

Hanterade domäner gör uppehåll när Microsoft inte kan hantera, övervaka, uppdatera eller säkerhetskopiera domänen med jämna mellanrum.

**Vad du kan förvänta dig:**
* Domänkontrollanterna för den hanterade domänen tas bort och är inte kan nås i det virtuella nätverket.
* Åtkomst med säkert LDAP till den hanterade domänen via internet (om aktiverat) slutar fungera.
* Du ser fel i autentisera till den hanterade domänen, logga in på domänanslutna virtuella datorer och ansluter via LDAP/LDAPS.
* Säkerhetskopieringar för din hanterade domän kommer inte längre.
* Stoppar synkronisering med Azure AD.
* Lös aviseringen orsakar din hanterade domän statusen ”Pausad” och sedan kontakta supporten.
* Stöd för kan återställa den hanterade domänen, bara om det finns en säkerhetskopia som är mindre än 30 dagar.

Den hanterade domänen bara kvar i ett väntetillstånd i 15 dagar. Om du vill återställa din hanterade domän rekommenderar Microsoft att du har löst kritiska aviseringar omedelbart.


### <a name="deleted-state"></a>Tillståndet 'Deleted'
En hanterad domän som är i tillståndet ”Pausad” i 15 dagar är **borttagna**.

**Vad du kan förvänta dig:**
* Alla resurser och säkerhetskopior för den hanterade domänen tas bort.
* Du kan inte återställa den hanterade domänen och måste du skapa en ny hanterad domän om du vill använda Azure AD Domain Services.
* När tagits bort, kan du inte debiteras för den hanterade domänen.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hur vet du om din hanterade domän är inaktiverad?
Du ser en [avisering](active-directory-ds-troubleshoot-alerts.md) på sidan Azure AD Domain Services Health i Azure-portalen som deklarerar domänen har pausats. Tillståndet för domänen visas också ”pausad”.


## <a name="restore-a-suspended-domain"></a>Återställa en pausad domän
Om du vill återställa en domän i tillståndet ”Pausad” gör du följande:

1. Navigera till den [Azure AD Domain Services-sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) på Azure portal
2. Klicka på den hanterade domänen.
3. I det vänstra navigeringsfönstret klickar du på **hälsotillstånd**.
4. Klicka på aviseringen. Aviserings-ID är AADDS503 eller AADDS504, beroende på orsaken till inaktivering.
5. Klicka på länken upplösning som anges i aviseringen och följ stegen för att lösa aviseringen.

Den hanterade domänen kan bara återställas till datumet för senaste säkerhetskopieringen. Datumet då den senaste säkerhetskopieringen visas på hälsosidan för den hanterade domänen. Alla ändringar som gjorts efter den senaste säkerhetskopieringen inte återställas. Säkerhetskopior för en hanterad domän lagras i upp till 30 dagar. Säkerhetskopieringar som är äldre än 30 dagar tas bort.


## <a name="next-steps"></a>Nästa steg
- [Lös aviseringar på din hanterade domän](active-directory-ds-troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontakta produktteamet](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
