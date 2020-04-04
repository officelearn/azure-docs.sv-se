---
title: Kontrollera hälsotillståndet för Azure Active Directory Domain Services | Microsoft-dokument
description: Lär dig hur du kontrollerar hälsotillståndet för en Azure Active Directory Domain Services (Azure AD DS) hanterad domän och förstår statusmeddelanden med Azure-portalen.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655644"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Kontrollera hälsotillståndet för en hanterad Azure Active Directory Domain Services-domän

Azure Active Directory Domain Services (Azure AD DS) kör vissa bakgrundsuppgifter för att hålla den hanterade domänen felfri och uppdaterad. Dessa uppgifter omfattar att ta säkerhetskopior, tillämpa säkerhetsuppdateringar och synkronisera data från Azure AD. Om det finns problem med azure AD DS-hanterade domänen kanske dessa uppgifter inte slutförs. Om du vill granska och lösa eventuella problem kan du kontrollera hälsotillståndet för en Azure AD DS-hanterad domän med Azure-portalen.

Den här artikeln visar hur du visar Azure AD DS hälsostatus och förstår den information eller aviseringar som visas.

## <a name="view-the-health-status"></a>Visa hälsostatus

Hälsostatus för en Azure AD DS-hanterad domän visas med Azure-portalen. Information om den senaste säkerhetskopieringstiden och synkroniseringen med Azure AD kan ses, tillsammans med eventuella aviseringar som indikerar ett problem med den hanterade domänens hälsa. Så här visar du hälsostatus för en Azure AD DS-hanterad domän:

1. Sök efter och välj **Azure AD Domain Services**i Azure-portalen .
1. Välj din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **Hälsa**till vänster i resursfönstret för Azure AD DS . Följande exempel skärmbild visar en felfri Azure AD DS-hanterad domän och status för den senaste säkerhetskopieringen och Azure AD-synkroniseringen:

    ![Hälsosidaöversikt i Azure-portalen som visar Azure Active Directory Domain Services-status](./media/check-health/health-page.png)

Den *senast utvärderade tidsstämpeln* för hälsosidan visas när den Azure AD DS-hanterade domänen senast kontrollerades. Hälsotillståndet för en Azure AD DS-hanterad domän utvärderas varje timme. Om du gör några ändringar i en Azure AD DS-hanterad domän väntar du tills nästa utvärderingscykel visar den uppdaterade hälsostatusen.

Statusen längst upp till höger anger den övergripande hälsotillståndet för azure AD DS-hanterad domän. Statusen påverkar alla befintliga aviseringar på domänen. I följande tabell beskrivs tillgängliga statusindikatorer:

| Status | Ikon | Förklaring |
| --- | :----: | --- |
| Körs | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Den Hanterade Azure AD DS-domänen körs korrekt och har inga kritiska varningar eller varningsaviseringar. Domänen kan ha informationsaviseringar. |
| Behöver uppmärksamhet (varning) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Det finns inga kritiska aviseringar på Azure AD DS-hanterade domänen, men det finns en eller flera varningsaviseringar som ska åtgärdas. |
| Behöver uppmärksamhet (kritisk) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Det finns en eller flera kritiska aviseringar på Azure AD DS-hanterad domän som måste åtgärdas. Du kan också ha varnings- och/eller informationsvarningar. |
| Distribuera | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Azure AD DS-domänen distribueras. |

## <a name="understand-monitors-and-alerts"></a>Förstå övervakare och varningar

Hälsostatusen för en Azure AD DS-hanterad domän visar två typer av information - *övervakare*och *aviseringar*. Övervakare visar den tid som grundläggande bakgrundsuppgifter slutfördes. Aviseringar ger information eller förslag för att förbättra stabiliteten i den hanterade domänen.

### <a name="monitors"></a>Övervakare

Övervakare är områden i en Azure AD DS-hanterad domän som kontrolleras regelbundet. Om det finns några aktiva aviseringar för Azure AD DS-hanterad domän kan det leda till att en av övervakarna rapporterar ett problem. Azure AD Domain Services har för närvarande övervakare för följande områden:

* Backup
* Synkronisering med Azure AD

#### <a name="backup-monitor"></a>Bildskärm för säkerhetskopiering

Säkerhetskopieringsövervakaren kontrollerar att automatiska regelbundna säkerhetskopior av Azure AD DS-hanterade domänen har körts. I följande tabell beskrivs statusen för den tillgängliga övervakaren för säkerhetskopiering:

| Detaljvärde | Förklaring |
| --- | --- |
| Aldrig säkerhetskopieras | Det här tillståndet är normalt för nya Azure AD DS-hanterade domäner. Den första säkerhetskopian bör skapas 24 timmar efter att Azure AD DS-hanterad domän har distribuerats. Om den här statusen kvarstår [öppnar du en Azure-supportbegäran][azure-support]. |
| Senaste säkerhetskopieringen togs för 1 till 14 dagar sedan | Det här tidsintervallet är den förväntade statusen för säkerhetskopieringsövervakaren. Automatiserade regelbundna säkerhetskopior bör ske under denna period. |
| Senaste säkerhetskopieringen togs för mer än 14 dagar sedan. | En tidsrymd längre än två veckor indikerar att det finns ett problem med de automatiska regelbundna säkerhetskopiorna. Aktiva kritiska aviseringar kan förhindra att Azure AD DS-hanterad domän säkerhetskopieras. Lös alla aktiva aviseringar för azure AD DS-hanterad domän. Om säkerhetskopieringsövervakaren inte uppdaterar statusen för att rapportera en säkerhetskopia nyligen [öppnar du en Azure-supportbegäran][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synkronisering med Azure AD-övervakare

En Azure AD DS-hanterad domän synkroniseras regelbundet med Azure Active Directory. Antalet användare och gruppobjekt och antalet ändringar som gjorts i Azure AD-katalogen sedan den senaste synkroniseringen påverkar hur lång tid det tar att synkronisera. Om azure AD DS-hanterade domänen senast synkroniserades för över tre dagar sedan, söker efter och löser eventuella aktiva aviseringar. Om synkroniseringsövervakaren inte uppdaterar statusen för att visa en ny synkronisering när du har åtgärdat eventuella aktiva aviseringar [öppnar du en Azure-supportbegäran][azure-support].

### <a name="alerts"></a>Aviseringar

Aviseringar genereras för problem i en Azure AD DS-hanterad domän som måste åtgärdas för att tjänsten ska fungera korrekt. Varje avisering förklarar problemet och ger en URL som beskriver specifika steg för att lösa problemet. Mer information om möjliga aviseringar och deras lösningar finns i [Felsöka aviseringar](troubleshoot-alerts.md).

Hälsostatusvarningar kategoriseras i följande allvarlighetsgrad:

 * **Kritiska aviseringar** är problem som allvarligt påverkar Azure AD DS-hanterad domän. Dessa varningar bör åtgärdas omedelbart. Azure-plattformen kan inte övervaka, hantera, korrigera och synkronisera den hanterade domänen tills problemen har lösts.
 * **Varningsaviseringar** meddelar dig om problem som kan påverka Azure AD DS-hanterade domänåtgärder om problemet kvarstår. Dessa aviseringar ger också rekommendationer för att skydda den hanterade domänen.
 * **Informationsaviseringar** är meddelanden som inte påverkar den Hanterade Azure AD DS-domänen negativt. Informationsaviseringar ger en inblick i vad som händer i den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Mer information om aviseringar som visas på hälsostatussidan finns i [Lösa aviseringar på din hanterade domän][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
