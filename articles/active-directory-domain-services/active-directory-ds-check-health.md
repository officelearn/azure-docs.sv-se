---
title: "Azure AD Domain Services - Kontrollera hälsotillståndet för din hanterade domän | Microsoft Docs"
description: "Kontrollera hälsotillståndet för din hanterade domän med hjälp av sidan hälsotillstånd i Azure-portalen."
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: ergreenl
ms.openlocfilehash: 4adbce0305bdc1a9b261f5cf644fad876d101bc6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-ad-domain-services---check-the-health-of-your-managed-domain"></a>Azure AD Domain Services - Kontrollera hälsotillståndet för din hanterade domän

## <a name="your-domains-health"></a>Hälsa för din domän

På sidan hälsa på Azure-portalen, kan du hålla dig uppdaterad på vad som händer på din hanterade domän. Den här artikeln steg till alla delar av hälsa av sidan och lära dig hur du kontrollerar att din domän är i tip-top form.

### <a name="view-your-health-page"></a>Visa sidan hälsa

1. Gå till sidan Azure AD Domain Services i den [Azure-portalen](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)
2. Klicka på den domän som du vill visa hälsotillstånd.
3. Klicka på ”hälsan” i det vänstra navigeringsfönstret.

Följande bild är ett exempel hälsotillstånd sida.

![Exempelsida för hälsotillstånd](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> Din domän hälsa utvärderas runt varje timme. När du gör ändringar i din hanterade domän, måste du vänta tills nästa utvärderingscykel för att visa din domän uppdateras hälsa. Du kan kontrollera när din domän senast utvärderades med hjälp av ”senast utvärderades” tidsstämpeln finns till i det övre högra hörnet.
>

### <a name="status-of-your-managed-domain"></a>Status för din hanterade domän

Status i övre höger i din hälsa sidan anges den övergripande hälsan för din domän. Status faktorer i alla befintliga aviseringar på din domän. Du kan också visa status för din domän på översiktssidan av Azure AD Domain Services.

Status för en hanterad domän:

| Status | Ikon | Förklaring |
| --- | :----: | --- |
| Körs | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | Din hanterade domän körs utan problem och har inte några kritiskt eller varningshälsotillstånd aviseringar. Den här domänen kan ha informationsaviseringar. |
| Behöver åtgärdas (varning) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Det finns inga kritiska aviseringar på din hanterade domän, men det finns en eller flera varningsaviseringar som behöver åtgärdas. |
| Behöver åtgärdas (kritisk) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Det finns en eller flera kritiska aviseringar på din hanterade domän. Du kan också ha varningar och/eller informativa aviseringar. |
| Distribuerar | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | Din domän håller på att distribueras. |

## <a name="monitors"></a>Övervakare

Övervakar information om vissa aspekter om hanterade domänen som övervakar Azure AD Domain Services. Det bästa sättet att hålla dina Övervakare i felfritt tillstånd är att lösa alla aviseringar på din hanterade domän.

Övervakare som är tillgängliga är:
 - Backup
 - Synkronisering med AAD

### <a name="backup"></a>Backup

Det här övervakar hur ofta vi göra säkerhetskopior av din hanterade domän. Följande är en tabell som förklarar kolumnen information för säkerhetskopiering och vilka värden som väntat.

| Värde för information | Förklaring |
| --- | --- |
|”Aldrig har säkerhetskopierat” | Det här tillståndet är normalt för en nyligen skapad domän. Den första säkerhetskopian skapas vanligtvis efter 24 timmar. Om din hanterade domän inte har skapats nyligen eller om du är i det här tillståndet för en onormalt lång tid, [supporten](active-directory-ds-contact-us.md). |
| Senaste säkerhetskopian skapades 1 till 14 dagar sedan | I allmänhet är detta det förväntade värdet för säkerhetskopiering. |
| Senaste säkerhetskopian skapades mer än 14 dagar sedan. | Varje gång som är längre än två veckor är ovanligt lång tid sedan den senaste säkerhetskopieringen. Först måste Lös alla aviseringar som visas på din hanterade domän och sedan om problemet kvarstår [supporten](active-directory-ds-contact-us.md). |


### <a name="synchronization-with-azure-ad"></a>Synkronisering med Azure AD

Microsoft håller reda på hur ofta din hanterade domän synkroniseras med Azure Active Directory. Mängden användare i din hanterade domän samt mängden ändringar som gjorts sedan den senaste synkroniseringen kan både påverkar hur lång tid en synkroniseringsperiod kan ta. I allmänhet om det är längre än tre dagar sedan den senaste synkroniseringen, rekommenderas att [supporten](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Aviseringar

Aviseringar är problem med din hanterade domän som måste åtgärdas för att Azure AD Domain Services att köra. Varje avisering beskriver problemet och innehåller en URL som beskriver särskilda åtgärder för att lösa problemet. Om du vill visa alla aviseringar och deras lösningar finns i [felsöka aviseringar](active-directory-ds-troubleshoot-alerts.md) artikel.

### <a name="severity"></a>Allvarsgrad
Aviseringar är indelade i tre olika nivåer av allvarlighetsgrad: kritisk, varning och information.

 * **Kritiska aviseringar** problem kraftigt påverka din hanterade domän. Dessa aviseringar bör åtgärdas omedelbart, som Microsoft kan övervaka, hantera, korrigera och synkronisera din hanterade domän.
 * **Varningsaviseringar** kan vara problem som kan påverka din domän i framtiden, men inte nödvändigtvis ”bryter” din tjänst. Dessa aviseringar beskrivs bästa praxis och ger förslag för att skydda din hanterade domän.
 * **Informationsaviseringar** aviseringar som inte negativt påverka din domän. Informationsaviseringar är utformade för att hålla dig kunskap om vad som händer på din domän och Azure AD Domain Services.

## <a name="next-steps"></a>Nästa steg
- [Lösa aviseringar på din hanterade domän](active-directory-ds-troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](active-directory-ds-features.md)
- [Kontakta oss](active-directory-ds-contact-us.md)
