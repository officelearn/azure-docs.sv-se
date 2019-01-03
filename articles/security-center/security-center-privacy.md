---
title: Hantera användardata i Azure Security Center | Microsoft Docs
description: " Lär dig hur du hanterar användardata i Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: rkarlin
ms.openlocfilehash: 35868545c46acb65ba487c546b66d8834bcb81a7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543197"
---
# <a name="manage-user-data-in-azure-security-center"></a>Hantera användardata i Azure Security Center
Den här artikeln innehåller information om hur du kan hantera användardata i Azure Security Center. Hantera användardata ger möjlighet att komma åt, ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

En Security Center-användare har tilldelats rollen Läsare, ägare, deltagare eller kontoadministratören kan komma åt kundinformation i verktyget. Se [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md) mer information om rollerna som läsare, ägare och deltagare. Se [Azure prenumerationens administratörer](../billing/billing-add-change-azure-subscription-administrator.md) vill veta mer om administratörsrollen för kontot.

## <a name="searching-for-and-identifying-personal-data"></a>Söka efter och identifiera personliga data
En Security Center-användare kan visa deras personliga data via Azure portal. Security Center lagrar bara säkerhetskontaktuppgifter, till exempel e-postadresser och telefonnummer. Se [lägga till kontaktuppgifter i Azure Security Center](security-center-provide-security-contact-details.md) för mer information.

En användare kan visa tillåtna IP-konfigurationer med hjälp av Security Centers just-in-funktion för åtkomst till virtuell dator i Azure-portalen. Mer information finns i [Manage virtual machine access using just in time](security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).

En användare kan visa säkerhetsaviseringar som tillhandahålls av Security Center, inklusive IP-adresser och angripare information i Azure-portalen. Se [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) för mer information.

## <a name="classifying-personal-data"></a>Klassificera personliga data
Du behöver inte klassificera personliga data som finns i Security Center kontakta säkerhetsfunktion. Data som sparas är en e-postadress (eller flera e-postadresser) och ett telefonnummer. [Kontakta data](security-center-provide-security-contact-details.md) har verifierats av Security Center.

Du behöver inte klassificera IP-adresser och portnummer som sparats av Security Center [just-in-time](security-center-just-in-time.md) funktionen.

Endast en användare som tilldelats rollen som administratör kan klassificera personliga data med [visning av aviseringar](security-center-managing-and-responding-alerts.md) i Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Skydda och kontrollera åtkomst till personliga data
En Security Center-användare har tilldelats rollen Läsare, ägare, deltagare eller kontoadministratören kan komma åt [kontakta säkerhetsdata](security-center-provide-security-contact-details.md).

En Security Center-användare har tilldelats rollen Läsare, ägare, deltagare eller kontoadministratören kan komma åt sina [just-in-time](security-center-just-in-time.md) principer.

En Security Center-användare har tilldelats rollen Läsare, ägare, deltagare eller kontoadministratören kan du visa deras [aviseringar](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Uppdatera personliga data
En Security Center-användare som tilldelats rollen som ägare, deltagare, eller kontoadministratören kan du uppdatera [kontakta säkerhetsdata](security-center-provide-security-contact-details.md) via Azure portal.

En Security Center-användare tilldelas rollen som ägare, deltagare, eller kontoadministratören kan uppdatera sina [just-in-time-principer](security-center-just-in-time.md).

En kontoadministratör kan inte redigera avisering incidenter. En [incidentavisering](security-center-managing-and-responding-alerts.md) anses säkerhetsdata och är skrivskyddad.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center-användare tilldelas rollen som ägare, deltagare, eller kontoadministratören kan ta bort [kontakta säkerhetsdata](security-center-provide-security-contact-details.md) via Azure portal.

En Security Center-användare tilldelas rollen som ägare, deltagare, eller kontoadministratören kan ta bort den [just-in-time-principer](security-center-just-in-time.md) via Azure portal.

En Security Center-användare kan inte ta bort aviseringen incidenter. På grund av säkerhetsbehov, en [incidentavisering](security-center-managing-and-responding-alerts.md) anses endast läsdata.

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center-användare har tilldelats rollen Läsare, ägare, deltagare eller kontoadministratören kan exportera [kontakta säkerhetsdata](security-center-provide-security-contact-details.md) genom att:

- Utföra en kopia från Azure portal
- Kör Azure REST API-anrop, få HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

En Security Center-användare som har tilldelats rollen av kontoadministratören kan exportera de [just-in-time-principer](security-center-just-in-time.md) som innehåller IP-adresser genom att:

- Utföra en kopia från Azure portal
- Kör Azure REST API-anrop, få HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

En kontoadministratör kan exportera aviseringsinformation genom att:

- Utföra en kopia från Azure portal
- Kör Azure REST API-anrop, få HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Se [få säkerhetsaviseringar (hämta samling)](https://msdn.microsoft.com/library/mt704050.aspx) för mer information.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Att begränsa användningen av personuppgifter för profilering eller marknadsföring utan medgivande
En Security Center-användare kan välja att avanmäla dig genom att ta bort deras [kontakta säkerhetsdata](security-center-provide-security-contact-details.md).

[Just-in-time-data](security-center-just-in-time.md) betraktas som icke-identifierbara data och bevaras under en period på 30 dagar.

[Aviseringsdata](security-center-managing-and-responding-alerts.md) anses säkerhetsdata och bevaras under en period på två år.

## <a name="auditing-and-reporting"></a>Granskning och rapportering
Granskningsloggar för säkerhetskontakt, precis i tid och varna uppdateringar underhålls i [Azure-aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du hanterar användardata, [hanterar användardata finns i en Azure Security Center-undersökning](security-center-investigation-user-data.md).
