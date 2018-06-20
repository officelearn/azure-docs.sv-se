---
title: Azure AD Domain Services - Kontrollera hälsotillståndet för din hanterade domän | Microsoft Docs
description: Kontrollera hälsotillståndet för din hanterade domän med hjälp av sidan hälsotillstånd i Azure-portalen.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: ffb00ce85a06812dfc42dfbbba74f712c1877615
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212400"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Kontrollera hälsotillståndet för en Azure AD Domain Services-hanterad domän

## <a name="overview-of-the-health-page"></a>Översikt över sidan hälsa
På sidan hälsa på Azure-portalen, kan du hålla dig uppdaterad på vad som händer på din hanterade domän. Den här artikeln beskriver hur elementen på sidan hälsa.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Hur du visar hälsotillståndet för din hanterade domän
1. Navigera till den [Azure AD Domain Services-sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure-portalen.
2. Klicka på den domän som du vill visa hälsotillstånd.
3. I det vänstra navigeringsfönstret klickar du på **hälsa**.

Följande bild illustrerar en exempelsida för hälsotillstånd: ![exempelsida hälsa](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> Hälsa för din hanterade domän utvärderas för varje timme. Vänta tills nästa utvärderingscykel för att visa din hanterade domän uppdaterade hälsotillstånd när ändringar din hanterade domän. Tidsstämpeln ”senast utvärderades” i övre högra hörnet visar när hälsotillståndet för din hanterade domän senast utvärderades.
>

### <a name="status-of-your-managed-domain"></a>Status för din hanterade domän
Status i övre höger i din hälsa sidan anges den övergripande hälsan för din hanterade domän. Status faktorer i alla befintliga aviseringar på din domän. Du kan också visa status för din domän på översiktssidan av Azure AD Domain Services.

| Status | Ikon | Förklaring |
| --- | :----: | --- |
| Körs | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | Din hanterade domän körs utan problem och har inte några kritiskt eller varningshälsotillstånd aviseringar. Den här domänen kan ha informationsaviseringar. |
| Behöver åtgärdas (varning) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Det finns inga kritiska aviseringar på din hanterade domän, men det finns en eller flera varningsaviseringar som behöver åtgärdas. |
| Behöver åtgärdas (kritisk) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Det finns en eller flera kritiska aviseringar på din hanterade domän. Du kan också ha varningar och/eller informativa aviseringar. |
| Distribuerar | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | Din domän håller på att distribueras. |

## <a name="monitors"></a>Övervakare
Övervakare har aspekter av din hanterade domän som Azure AD Domain Services övervakar regelbundet. Det bästa sättet att hålla dina Övervakare i felfritt tillstånd är att lösa några aktiva aviseringar för din hanterade domän.

Azure AD Domain Services övervakar för närvarande följande:
 - Backup
 - Synkronisering med Azure AD

### <a name="the-backup-monitor"></a>”Säkerhetskopiering” Övervakaren
Det här övervakar om utförs regelbundna säkerhetskopieringar av din hanterade domän. I följande tabell beskrivs vad som händer i kolumnen information för säkerhetskopiering Övervakare:

| Värde för information | Förklaring |
| --- | --- |
|”Aldrig har säkerhetskopierat” | Det här tillståndet är normalt för en nyligen skapad hanterad domän. Den första säkerhetskopian skapas vanligtvis 24 timmar efter din hanterade domän har etablerats. Om din hanterade domän inte har skapats nyligen eller om du ser det här tillståndet under en onormalt tidsperiod [supporten](active-directory-ds-contact-us.md). |
| Senaste säkerhetskopian skapades 1 till 14 dagar sedan | I allmänhet är förväntas det här värdet för säkerhetskopiering övervakaren. |
| Senaste säkerhetskopian skapades mer än 14 dagar sedan. | Varje gång som är längre än två veckor är ovanligt lång tid sedan den senaste säkerhetskopieringen. Aktiva kritiska aviseringar kan förhindra att din hanterade domän säkerhetskopieras regelbundet. Först måste Lös alla aktiva aviseringar för din hanterade domän och sedan om problemet fortfarande kvarstår [supporten](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>'Synkronisering med Azure AD-Övervakaren
Microsoft övervakar hur ofta din hanterade domän synkroniseras med Azure Active Directory. Antal objekt (användare och grupper) och antalet ändringar som gjorts i Azure AD-katalogen sedan den senaste synkroniseringen kan både påverkar hur lång tid en synkroniseringsperiod kan ta. Om din hanterade domän senast synkroniserades än tre dagar sedan, [supporten](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Aviseringar
Aviseringar genereras för problem på hanterade domänen som måste åtgärdas för att Azure AD Domain Services att köra. Varje avisering beskriver problemet och innehåller en upplösning URL som beskriver särskilda åtgärder för att lösa problemet. Om du vill visa alla aviseringar och deras lösningar finns i [felsöka aviseringar](active-directory-ds-troubleshoot-alerts.md) artikel.

### <a name="alert-severity"></a>Allvarlighetsgrad för avisering
Aviseringar är indelade i tre olika nivåer av allvarlighetsgrad: kritisk, varning och information.

 * **Kritiska aviseringar** problem kraftigt påverka din hanterade domän. Dessa aviseringar bör åtgärdas omedelbart, som Microsoft kan övervaka, hantera, korrigera och synkronisera din hanterade domän. 
 * **Varningsaviseringar** meddelar dig om problem som kan påverka din hanterade domän i framtiden. Dessa aviseringar ger rekommendationer för att skydda din hanterade domän.
 * **Informationsaviseringar** aviseringar som inte negativt påverka din domän. Informationsaviseringar är utformade för att hålla dig kunskap om vad som händer på din domän och Azure AD Domain Services.

## <a name="next-steps"></a>Nästa steg
- [Lösa aviseringar på din hanterade domän](active-directory-ds-troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontakta produktteamet](active-directory-ds-contact-us.md)
