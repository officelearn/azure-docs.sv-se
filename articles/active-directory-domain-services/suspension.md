---
title: Avstängda domäner i Azure AD Domain Services | Microsoft-dokument
description: Lär dig mer om de olika hälsotillstånden för en Azure AD DS-hanterad domän och hur du återställer en avstängd domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 8a82d2ad3e79633bb930348c6162996e961c4306
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612962"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Förstå hälsotillstånden och lösa avstängda domäner i Azure Active Directory Domain Services

När Azure Active Directory Domain Services (Azure AD DS) inte kan serva en hanterad domän under en längre tid, försätter den hanterade domänen i ett pausat tillstånd. Om en hanterad domän sedan förblir i ett pausat tillstånd tas den bort automatiskt. Lös eventuella aviseringar så fort du kan för att hålla din Azure AD DS-hanterade domän felfri och undvika avstängning.

I den här artikeln beskrivs varför hanterade domäner pausas och hur du återställer en avstängd domän.

## <a name="overview-of-managed-domain-states"></a>Översikt över hanterade domäntillstånd

Genom livscykeln för en Azure AD DS-hanterad domän finns det olika tillstånd som anger dess hälsa. Om den hanterade domänen rapporterar ett problem löser du snabbt den underliggande orsaken för att stoppa tillståndet från att fortsätta att försämras.

![Förloppet av tillstånd som en Azure AD DS-hanterad domän tar mot avstängning](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

En Azure AD DS-hanterad domän kan vara i något av följande tillstånd:

* [Körs](#running-state)
* [Behöver uppmärksamhet](#needs-attention-state)
* [Inaktiverad](#suspended-state)
* [Borttaget](#deleted-state)

## <a name="running-state"></a>Kör tillstånd

En Azure AD DS-hanterad domän som är korrekt konfigurerad och körs utan problem är i *Running* körtillståndet. Det här är det önskade tillståndet för en hanterad domän.

### <a name="what-to-expect"></a>Vad du kan förvänta dig

* Azure-plattformen kan regelbundet övervaka hälsotillståndet för den hanterade domänen.
* Domänkontrollanter för den hanterade domänen korrigeras och uppdateras regelbundet.
* Ändringar från Azure Active Directory synkroniseras regelbundet till den hanterade domänen.
* Regelbundna säkerhetskopior görs för den hanterade domänen.

## <a name="needs-attention-state"></a>Behov Uppmärksamhet tillstånd

En Azure AD DS-hanterad domän med ett eller flera problem som måste åtgärdas finns i tillståndet *Behöver åtgärdas.* Hälsosidan för den hanterade domänen visar aviseringarna och anger var det finns ett problem. Vissa aviseringar är tillfälliga och löses automatiskt av Azure-plattformen. För andra aviseringar kan du åtgärda problemet genom att följa de angivna lösningsstegen. Det finns en kritisk avisering, [öppna en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

Ett exempel på en avisering är när det finns en restriktiv nätverkssäkerhetsgrupp. I den här konfigurationen kanske Azure-plattformen inte kan uppdatera och övervaka den hanterade domänen. En avisering genereras och tillståndet ändras till *Behöver åtgärdas*.

Mer information finns i [Felsöka aviseringar för en Azure AD DS-hanterad domän][resolve-alerts].

### <a name="what-to-expect"></a>Vad du kan förvänta dig

När en Azure AD DS-hanterad domän är i tillståndet *Behöver åtgärdas* kanske Azure-plattformen inte kan övervaka, korrigera, uppdatera eller säkerhetskopiera data regelbundet. I vissa fall, till exempel med en ogiltig nätverkskonfiguration, kan domänkontrollanterna för den hanterade domänen inte nås.

* Den hanterade domänen är i feltillstånd och pågående hälsoövervakning kan stoppas tills aviseringen har åtgärdats.
* Domänkontrollanter för den hanterade domänen kan inte korrigeras eller uppdateras.
* Ändringar från Azure Active Directory kanske inte synkroniseras med den hanterade domänen.
* Säkerhetskopieringar för den hanterade domänen kanske inte tas.
* Om du löser icke-kritiska aviseringar som påverkar den hanterade domänen bör hälsotillståndet återgå till tillståndet *Kör.*
* Kritiska aviseringar utlöses för konfigurationsproblem där Azure-plattformen inte kan nå domänkontrollanterna. Om dessa kritiska aviseringar inte har lösts inom 15 dagar går den hanterade domänen in i *vänteläge.*

## <a name="suspended-state"></a>Tillfälligt tillstånd

En Azure AD DS-hanterad domän går in i tillståndet **Pausad** av något av följande skäl:

* En eller flera kritiska aviseringar har inte lösts på 15 dagar.
    * Kritiska aviseringar kan orsakas av en felkonfiguration som blockerar åtkomst till resurser som behövs av Azure AD DS. Aviseringen [AADDS104: Nätverksfel][alert-nsg] har till exempel varit olöst i mer än 15 dagar i den hanterade domänen.
* Det finns ett faktureringsproblem med Azure-prenumerationen eller så har Azure-prenumerationen upphört att gälla.

Hanterade domäner pausas när Azure-plattformen inte kan hantera, övervaka, korrigera eller säkerhetskopiera domänen. En hanterad domän förblir i ett *pausat* tillstånd i 15 dagar. Lös kritiska aviseringar omedelbart om du vill behålla åtkomsten till den hanterade domänen.

### <a name="what-to-expect"></a>Vad du kan förvänta dig

Följande beteende uppstår när en Azure AD DS-hanterad domän är i *pausat* tillstånd:

* Domänkontrollanter för den hanterade domänen avetablerars och kan inte nås i det virtuella nätverket.
* Säker LDAP-åtkomst till den hanterade domänen via Internet, om den är aktiverad, slutar fungera.
* Det finns fel i autentiseringen till den hanterade domänen, logga in på domänanslutna virtuella datorer eller ansluta via LDAP/LDAPS.
* Säkerhetskopieringar för den hanterade domänen tas inte längre.
* Synkronisering med Azure AD stoppas.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hur vet du om den hanterade domänen är avstängd?

Du ser en [avisering][resolve-alerts] på sidan Azure AD DS Health i Azure-portalen som noterar att domänen är avstängd. Tillståndet för domänen visar också *Pausad*.

### <a name="restore-a-suspended-domain"></a>Återställa en avstängd domän

Så här återställer du hälsotillståndet för en Azure AD DS-hanterad domän som är i *pausat* tillstånd:

1. Sök efter och välj **Domäntjänster**i Azure-portalen .
1. Välj din Azure AD DS-hanterade domän i listan, till exempel *aaddscontoso.com*och välj sedan **Hälsa**.
1. Välj aviseringen, till exempel *AADDS503* eller *AADDS504*, beroende på orsaken till avstängningen.
1. Välj den lösningslänk som finns i aviseringen och följ stegen för att lösa den.

En hanterad domän kan bara återställas till datumet för den senaste säkerhetskopian. Datumet för den senaste säkerhetskopian visas på **sidan Hälsa** i den hanterade domänen. Alla ändringar som inträffade efter den senaste säkerhetskopieringen återställs inte. Säkerhetskopior för en hanterad domän lagras i upp till 30 dagar. Säkerhetskopior som är äldre än 30 dagar tas bort.

När du har löst aviseringar när den hanterade domänen är i *pausat* tillstånd [öppnar du en Azure-supportbegäran för][azure-support] att återgå till ett felfritt tillstånd. Om det finns en säkerhetskopia mindre än 30 dagar kan Azure-stöd återställa den hanterade domänen.

## <a name="deleted-state"></a>Borttaget tillstånd

Om en Azure AD DS-hanterad domän förblir i vänt tillstånd i 15 dagar tas den bort. *Suspended* Den här processen kan inte återuppknås.

### <a name="what-to-expect"></a>Vad du kan förvänta dig

När en Azure AD DS-hanterad domän går in i tillståndet *Borttaget* visas följande beteende:

* Alla resurser och säkerhetskopior för den hanterade domänen tas bort.
* Du kan inte återställa den hanterade domänen och behöver skapa en ersättningshanterad domän för att återanvända Azure AD DS.
* När den har tagits bort faktureras du inte för den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Om du vill att din Azure AD DS-hanterade domän ska vara felfri och minimera risken för att den pausas kan du läsa om hur [du löser aviseringar för din hanterade domän][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
