---
title: Azure AD Domain Services-kontrol lera den hanterade domänens hälso tillstånd | Microsoft Docs
description: Kontrol lera hälso tillståndet för din hanterade domän med hjälp av sidan hälso tillstånd i Azure Portal.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 6b808126fe4366d3ca3cc19c674b489ec3055665
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234163"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Kontrol lera hälso tillståndet för en Azure AD Domain Services hanterad domän

## <a name="overview-of-the-health-page"></a>Översikt över sidan hälsa
Med hjälp av sidan hälso tillstånd i Azure Portal kan du hålla dig uppdaterad om vad som händer i din hanterade domän. Den här artikeln går igenom elementen på sidan hälso tillstånd.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Så här visar du hälsan för din hanterade domän
1. Gå till [sidan Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure Portal.
2. Klicka på den domän som du vill visa hälso tillståndet för.
3. I det vänstra navigerings fönstret klickar du på **hälsa**.

Följande bild illustrerar ett exempel på en hälso sida: ![Exempel på hälso Sidan](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Din hanterade domän hälsa utvärderas varje timme. När du har gjort ändringar i din hanterade domän väntar du tills nästa utvärderings cykel visar den hanterade domänens uppdaterade hälsa. Tidsstämpeln "Senast utvärderad" i det övre högra hörnet visar när den hanterade domänens hälso tillstånd senast utvärderades.
>

### <a name="status-of-your-managed-domain"></a>Status för din hanterade domän
Statusen överst till höger på hälso sidan visar den övergripande hälsan för din hanterade domän. Status faktorerna i alla befintliga aviseringar på din domän. Du kan också visa status för din domän på sidan Översikt för Azure AD Domain Services.

| Status | Ikon | Förklaring |
| --- | :----: | --- |
| Körs | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Din hanterade domän körs smidigt och saknar kritiska eller varnings aviseringar. Den här domänen kan innehålla informations aviseringar. |
| Kräver åtgärd (varning) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Det finns inga kritiska aviseringar på den hanterade domänen, men det finns en eller flera varnings aviseringar som måste åtgärdas. |
| Behöver åtgärdas (kritisk) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Det finns en eller flera kritiska aviseringar på din hanterade domän. Du kan också ha varnings-och/eller informations aviseringar. |
| Deploy | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Din domän håller på att distribueras. |

## <a name="monitors"></a>Övervakare
Övervakare är aspekter av din hanterade domän som Azure AD Domain Services övervakas regelbundet. Det bästa sättet att hålla dina övervakare i felfritt tillstånd är att lösa eventuella aktiva aviseringar för din hanterade domän.

Azure AD Domain Services övervakar för närvarande följande:
 - Backup
 - Synkronisering med Azure AD

### <a name="the-backup-monitor"></a>Övervakaren "säkerhets kopiering"
Detta övervakar om regelbundna säkerhets kopieringar av din hanterade domän utförs. I följande tabell förklaras hur du förväntar dig i kolumnen information i Övervakaren för säkerhets kopiering:

| Detalj värde | Förklaring |
| --- | --- |
|"Aldrig säkerhets kopie ras" | Det här läget är normalt för en nyligen skapad hanterad domän. I allmänhet skapas den första säkerhets kopieringen 24 timmar efter att din hanterade domän har tillhandahållits. [Kontakta supporten](contact-us.md)om din hanterade domän inte har skapats nyligen eller om du ser det här läget under en onormal tid. |
| Den senaste säkerhets kopieringen tog 1 till 14 dagar sedan | I allmänhet förväntas detta värde för säkerhets kopierings övervakaren. |
| Den senaste säkerhets kopieringen vidtogs över 14 dagar sedan. | Tiden längre än två veckor är en ovanligt lång tid sedan den senaste säkerhets kopieringen. Aktiva kritiska aviseringar kan förhindra att den hanterade domänen säkerhets kopie ras regelbundet. Lös först alla aktiva aviseringar för din hanterade domän och [kontakta supporten](contact-us.md)om problemet kvarstår. |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Övervakaren "synkronisering med Azure AD"
Microsoft övervakar hur ofta din hanterade domän ska synkroniseras med Azure Active Directory. Antalet objekt (användare & grupper) och antalet ändringar som gjorts i Azure AD-katalogen sedan den senaste synkroniseringen kan båda påverka hur lång tid en synkronisering kan ta. [Kontakta supporten](contact-us.md)om din hanterade domän senast synkroniserades över tre dagar sedan.

## <a name="alerts"></a>Aviseringar
Aviseringar genereras för problem i din hanterade domän som måste åtgärdas för att Azure AD Domain Services ska kunna köras. Varje avisering förklarar problemet och ger en lösnings-URL som beskriver specifika steg för att lösa problemet. Om du vill visa alla aviseringar och deras resolutioner går du till artikeln [felsök aviseringar](troubleshoot-alerts.md) .

### <a name="alert-severity"></a>Allvarlighets grad för avisering
Aviseringar kategoriseras i tre olika nivåer av allvarlighets grad: kritisk, varning och information.

 * **Kritiska aviseringar** är problem som påverkar din hanterade domän allvarligt. Dessa aviseringar bör åtgärdas omedelbart, eftersom Microsoft inte kan övervaka, hantera, korrigera och synkronisera din hanterade domän. 
 * **Varnings aviseringar** meddelar dig om problem som kan påverka din hanterade domän i framtiden. De här aviseringarna ger rekommendationer för att skydda din hanterade domän.
 * **Informations aviseringar** är aviseringar som inte påverkar din domän negativt. Informations aviseringar är utformade för att du ska kunna se vad som händer i din domän och Azure AD Domain Services.

## <a name="next-steps"></a>Nästa steg
- [Lösa aviseringar på din hanterade domän](troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](overview.md)
- [Kontakta produkt teamet](contact-us.md)
