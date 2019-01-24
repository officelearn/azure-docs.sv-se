---
title: Azure AD Domain Services - kontrollerar hälsotillståndet för din hanterade domän | Microsoft Docs
description: Kontrollera hälsotillståndet för din hanterade domän via sidan health i Azure-portalen.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: fe5776dad4c4b9ae96484ac266a23e87658cf578
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852839"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Kontrollera hälsotillståndet för en Azure AD Domain Services-hanterad domän

## <a name="overview-of-the-health-page"></a>Översikt över sidan hälsa
Med hjälp av sidan health på Azure portal, går det att hålla dig uppdaterad på vad som händer på din hanterade domän. Den här artikeln beskriver elementen i hälsosidan.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Så här visar hälsotillståndet för din hanterade domän
1. Navigera till den [Azure AD Domain Services-sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure-portalen.
2. Klicka på den domän du vill visa hälsotillståndet för.
3. I det vänstra navigeringsfönstret klickar du på **hälsotillstånd**.

Följande bild illustrerar en exempelsida för hälsotillstånd: ![Exempelsida för hälsotillstånd](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Den hanterade domänen hälsotillstånd utvärderas för varje timme. Vänta tills nästa utvärderingscykel för att visa uppdaterade hälsotillståndet för din hanterade domän när du har gjort ändringar till din hanterade domän. ”Senast utvärderad” tidsstämpeln i övre högra hörnet visar när hälsotillståndet för din hanterade domän senast utvärderades.
>

### <a name="status-of-your-managed-domain"></a>Status för din hanterade domän
Statusen uppe till höger på hälsan för sidan anger den övergripande hälsan för din hanterade domän. Statusen omfattar alla befintliga aviseringar på din domän. Du kan även visa statusen för din domän på översiktssidan för Azure AD Domain Services.

| Status | Ikon | Förklaring |
| --- | :----: | --- |
| Körs | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15"> | Din hanterade domän fungerar smidigt och har inte några aviseringar som kritiskt eller varning. Den här domänen kan ha informativa aviseringar. |
| Behöver kontrolleras (varning) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15"> | Det finns inga kritiska aviseringar på din hanterade domän, men det finns en eller flera varningar som behöver åtgärdas. |
| Behöver kontrolleras (kritisk) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15"> | Det finns en eller flera kritiska aviseringar på din hanterade domän. Du kan också ha aviseringar för varningar och/eller endast i informationssyfte. |
| Distribuerar | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15"> | Din domän håller på att distribueras. |

## <a name="monitors"></a>Övervakare
Övervakare är aspekter av din hanterade domän som Azure AD Domain Services övervakar regelbundet. Det bästa sättet att hålla din Övervakare i felfritt tillstånd är att lösa alla aktiva varningar för din hanterade domän.

Azure AD Domain Services övervakar för närvarande följande:
 - Backup
 - Synkronisering med Azure AD

### <a name="the-backup-monitor"></a>”Säkerhetskopiering” Övervakaren
Det här övervakar om utförs regelbundna säkerhetskopieringar av din hanterade domän. I följande tabell beskrivs vad som händer i kolumnen information för säkerhetskopiering övervakaren:

| Detalj värde | Förklaring |
| --- | --- |
|”Aldrig har säkerhetskopierat” | Det här tillståndet är normalt en nyligen skapade hanterad domän. Den första säkerhetskopian skapas vanligtvis 24 timmar efter din hanterade domän har etablerats. Om din hanterade domän inte har skapats nyligen eller om du ser det här tillståndet för en onormalt lång tid, [supporten](active-directory-ds-contact-us.md). |
| Senaste säkerhetskopian skapades 1 till 14 dagar sedan | I allmänhet är kan det här värdet förväntas för säkerhetskopiering övervakaren. |
| Senaste säkerhetskopian skapades mer än 14 dagar sedan. | Som helst som är längre än två veckor är ovanligt lång tid sedan den senaste säkerhetskopieringen. Aktiva kritiska varningar kan förhindra att din hanterade domän som säkerhetskopieras regelbundet. Och Lös alla aktiva aviseringar för din hanterade domän och sedan om problemet fortfarande kvarstår [supporten](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>”Synkronisering med Azure AD-Övervakaren
Microsoft övervakar hur ofta din hanterade domän är synkroniserad med Azure Active Directory. Antal objekt (användare och grupper) och antalet ändringar som gjorts i Azure AD-katalogen, eftersom den senaste synkroniseringen kan både påverkar hur lång tid en synkroniseringsperiod kan ta. Om din hanterade domän senast synkroniserades än tre dagar sedan, [supporten](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Aviseringar
Aviseringar genereras för problem med din hanterade domän som behöver åtgärdas för att Azure AD Domain Services ska köras. Varje avisering beskriver problemet och tillhandahåller en lösning-URL som beskriver specifika steg för att lösa problemet. Du kan visa alla aviseringar och sina lösningar genom att gå till den [Felsök aviseringar](active-directory-ds-troubleshoot-alerts.md) artikeln.

### <a name="alert-severity"></a>Allvarlighetsgrad för avisering
Aviseringar är indelade i tre olika nivåer av allvarlighetsgrad: kritisk, varning och information.

 * **Kritiska aviseringar** är problem som kraftigt försämras din hanterade domän. Dessa aviseringar bör åtgärdas omedelbart, som Microsoft inte kan övervaka, hantera, korrigera och synkronisera din hanterade domän. 
 * **Varningsaviseringar** meddela dig om problem som kan påverka din hanterade domän i framtiden. Dessa aviseringar ger rekommendationer för att skydda din hanterade domän.
 * **Informationsaviseringar** aviseringar som inte negativt påverkar din domän. Informationsaviseringar är utformade för att hålla dig kunskap om vad som händer i din domän och Azure AD Domain Services.

## <a name="next-steps"></a>Nästa steg
- [Lös aviseringar på din hanterade domän](active-directory-ds-troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontakta produktteamet](active-directory-ds-contact-us.md)
