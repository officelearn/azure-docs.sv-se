---
title: 'Azure Active Directory Domain Services: Pausade domäner | Microsoft Docs'
description: Förskjutande och borttagning av hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 781a81589032c290cef7342e7210ee36f388b22a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233992"
---
# <a name="understand-the-suspended-states-for-an-azure-active-directory-domain-services-managed-domain"></a>Förstå pausade tillstånd för en Azure Active Directory Domain Services hanterad domän

När Azure Active Directory Domain Services (Azure AD DS) inte kan betjäna en hanterad domän under en lång tids period, placerar den hanterade domänen i ett pausat tillstånd. I den här artikeln förklaras varför hanterade domäner är pausade och hur du åtgärdar en pausad domän.


## <a name="states-your-managed-domain-can-be-in"></a>Anger att din hanterade domän kan finnas i

![Tids linje för pausad domän](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Föregående bild beskriver möjliga tillstånd som en Azure AD DS-hanterad domän kan finnas i.

### <a name="running-state"></a>Kör tillstånd
En hanterad domän som är korrekt konfigurerad och som körs regelbundet är  i körnings tillstånd.

**Vad som ska förväntas**
* Microsoft kan regelbundet övervaka hälso tillståndet för din hanterade domän.
* Domänkontrollanter för din hanterade domän korrigeras och uppdateras regelbundet.
* Ändringar från Azure Active Directory synkroniseras regelbundet till din hanterade domän.
* Vanliga säkerhets kopieringar görs för din hanterade domän.


### <a name="needs-attention-state"></a>"Kräver åtgärds tillstånd"
En hanterad domän är i **Åtgärds läget krav** om ett eller flera problem kräver att en administratör vidtar åtgärder. Sidan hälso tillstånd i den hanterade domänen visar en eller flera aviseringar i det här tillståndet.

Om du till exempel har konfigurerat en restriktiv NSG för ditt virtuella nätverk kanske Microsoft inte kan uppdatera och övervaka din hanterade domän. Den här ogiltiga konfigurationen utlöser en avisering som placerar din hanterade domän i status "behöver åtgärdas".

Varje avisering har en uppsättning lösnings steg. Vissa aviseringar är tillfälliga och kommer automatiskt att lösas av tjänsten. Du kan lösa vissa andra aviseringar genom att följa anvisningarna i motsvarande lösnings steg för aviseringen. För vissa kritiska aviseringar måste du kontakta Microsoft Support för att få en lösning.

Mer information finns i [fel sökning av aviseringar på en hanterad domän](troubleshoot-alerts.md).

**Vad som ska förväntas**

I vissa fall (till exempel om du har en ogiltig nätverks konfiguration) kan det hända att domän kontrol Lanterna för den hanterade domänen inte kan kontaktas. När din hanterade domän är i läget "kräver Attention" kan Microsoft inte garantera att den kommer att övervakas, korrigeras, uppdateras eller säkerhets kopie ras regelbundet.

* Den hanterade domänen är i ett ohälsosamt tillstånd och pågående hälso övervakning kan stoppas tills aviseringen har lösts.
* Domänkontrollanter för din hanterade domän kan inte korrigeras eller uppdateras.
* Ändringar från Azure Active Directory kanske inte synkroniseras med din hanterade domän.
* Säkerhets kopieringar för din hanterade domän kan eventuellt tas, om möjligt.
* Om du löser aviseringar som påverkar din hanterade domän kanske du kan återställa den till läget "körs".
* Kritiska aviseringar utlöses för konfigurations problem där Microsoft inte kan komma åt dina domänkontrollanter. Om sådana varningar inte löses inom 15 dagar, anges den hanterade domänen i tillståndet "Suspended".


### <a name="the-suspended-state"></a>Tillståndet "inaktive rad"
En hanterad domän försätts  i pausat läge av följande skäl:

* En eller flera kritiska aviseringar har inte lösts på 15 dagar. Kritiska aviseringar kan orsakas av en felaktig konfiguration som blockerar åtkomst till resurser som krävs av Azure AD DS.
    * Till exempel är aviseringen [AADDS104: Nätverks felet](alert-nsg.md) har olösts på över 15 dagar i den hanterade domänen.
* Det har uppstått ett fakturerings problem med din Azure-prenumeration eller så har din Azure-prenumeration gått ut.

Hanterade domäner pausas när Microsoft inte kan hantera, övervaka, korrigera eller säkerhetskopiera domänen kontinuerligt.

**Vad som ska förväntas**
* Domän kontrol Lanterna för den hanterade domänen är avetablerade och går inte att komma åt i det virtuella nätverket.
* Säkert LDAP åtkomst till den hanterade domänen via Internet (om den är aktive rad) slutar fungera.
* Du märker att det inte går att autentisera till den hanterade domänen, logga in på domänanslutna virtuella datorer eller att ansluta via LDAP/LDAPs.
* Säkerhets kopieringar för din hanterade domän tas inte längre.
* Synkronisering med Azure AD stoppas.

När du har löst aviseringen hamnar den hanterade domänen i tillståndet "pausad". Sedan måste du kontakta supporten.
Supporten kan återställa din hanterade domän, men bara om det finns en säkerhets kopia som är mindre än 30 dagar gammal.

Den hanterade domänen förblir i ett inaktiverat tillstånd i 15 dagar. För att återställa din hanterade domän rekommenderar Microsoft att du löser viktiga aviseringar direkt.


### <a name="deleted-state"></a>Status borttagen
En hanterad domän som stannar kvar i tillståndet "inaktive rad" i 15 dagar **tas bort**.

**Vad som ska förväntas**
* Alla resurser och säkerhets kopior för den hanterade domänen tas bort.
* Du kan inte återställa den hanterade domänen och du måste skapa en ny hanterad domän för att använda Azure AD DS.
* När den har tagits bort debiteras du inte för den hanterade domänen.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hur vet du om din hanterade domän har pausats?
Du ser en [avisering](troubleshoot-alerts.md) på sidan Azure AD DS Health i Azure Portal som deklarerar att domänen har pausats. Domänens tillstånd visar också "inaktive rad".


## <a name="restore-a-suspended-domain"></a>Återställa en pausad domän
Gör så här om du vill återställa en domän i tillståndet "Suspended":

1. Gå till [sidan Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure Portal.
2. Välj den hanterade domänen.
3. I den vänstra panelen väljer du **hälso tillstånd**.
4. Välj aviseringen. Aviserings-ID: t är antingen AADDS503 eller AADDS504, beroende på orsaken till SUS pensionen.
5. Välj den upplösnings länk som anges i aviseringen. Följ sedan stegen för att lösa aviseringen.

Din hanterade domän kan bara återställas till datumet för den senaste säkerhets kopieringen. Datumet för den senaste säkerhets kopieringen visas på sidan hälsa i den hanterade domänen. Eventuella ändringar som inträffat efter den senaste säkerhets kopieringen återställs inte. Säkerhets kopior för en hanterad domän lagras i upp till 30 dagar. Säkerhets kopieringar som är äldre än 30 dagar tas bort.


## <a name="next-steps"></a>Nästa steg
- [Lösa aviseringar för din hanterade domän](troubleshoot-alerts.md)
- [Läs mer om Azure Active Directory Domain Services](overview.md)
- [Kontakta produkt teamet](contact-us.md)

## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services produkt teamet för att [dela feedback eller för support](contact-us.md).
