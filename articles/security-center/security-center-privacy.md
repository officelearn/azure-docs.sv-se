---
title: Hantera användardata i Azure Security Center | Microsoft Docs
description: " Lär dig mer om att hantera användardata i Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 2495bae5c63cdafe049ec39f71ab53106c5f2df7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655724"
---
# <a name="manage-user-data-in-azure-security-center"></a>Hantera användardata i Azure Security Center
Den här artikeln innehåller information om hur du kan hantera användardata i Azure Security Center. Hantera användardata ger möjlighet att få åtkomst till, ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

En Security Center-användare som tilldelats rollen Läsare, ägare, deltagare eller kontoadministratör kan komma åt kundens data i verktyget. Se [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md) lära dig mer om rollerna Reader, ägare och deltagare. Se [Azure-prenumerationsadministratörer](../billing/billing-add-change-azure-subscription-administrator.md) vill veta mer om rollen kontoadministratör.

## <a name="searching-for-and-identifying-personal-data"></a>Söker efter och identifiera personliga data
En Security Center-användare kan visa deras personliga data via Azure-portalen. Security Center lagrar bara kontaktinformation för säkerhet, till exempel e-postadresser och telefonnummer. Se [ange säkerhet kontaktinformation i Azure Security Center](security-center-provide-security-contact-details.md) för mer information.

En användare kan visa tillåtna IP-konfigurationer med hjälp av Security Center precis i tid funktionen för virtuell dator i Azure-portalen. Mer information finns i [Manage virtual machine access using just in time](security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).

En användare kan visa säkerhetsaviseringar som tillhandahålls av Security Center, inklusive IP-adresser och angripare information i Azure-portalen. Se [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) för mer information.

## <a name="classifying-personal-data"></a>Klassificera personliga data
Du behöver inte klassificera personliga data hittades i Security Center kontakta säkerhetsfunktion. Data som sparas är en e-postadress (eller flera e-postadresser) och ett telefonnummer. [Kontakta](security-center-provide-security-contact-details.md) verifieras av Security Center.

Du behöver inte klassificera IP-adresser och portnummer som sparas av Security Center [precis i tid](security-center-just-in-time.md) funktion.

Endast en användare som tilldelats rollen som administratör kan du klassificera personuppgifter [Visa aviseringar](security-center-managing-and-responding-alerts.md) i Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Skydda och kontrollera åtkomst till personliga data
En Security Center-användare som tilldelats rollen Läsare, ägare, deltagare eller kontoadministratör kan komma åt [säkerhet kontaktdata](security-center-provide-security-contact-details.md).

En Security Center-användare som tilldelats rollen Läsare, ägare, deltagare eller kontoadministratör kan komma åt sina [precis i tid](security-center-just-in-time.md) principer.

En Security Center-användare som tilldelats rollen Läsare, ägare, deltagare eller kontoadministratör kan du visa deras [aviseringar](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Uppdaterar personliga data
Security Center användaren tilldelas rollen som ägare, deltagare, eller kontoadministratör kan du uppdatera [säkerhet kontaktdata](security-center-provide-security-contact-details.md) via Azure portal.

Security Center användaren tilldelas rollen som ägare, deltagare, eller kontoadministratör kan uppdatera sina [precis i tid principer](security-center-just-in-time.md).

Administratör konto kan inte redigera avisering incidenter. En [incidentavisering](security-center-managing-and-responding-alerts.md) anses säkerhetsdata och är skrivskyddad.

## <a name="deleting-personal-data"></a>Ta bort personliga data
Security Center användaren tilldelas rollen som ägare, deltagare, eller kontoadministratör kan ta bort [säkerhet kontaktdata](security-center-provide-security-contact-details.md) via Azure portal.

Security Center användaren tilldelas rollen som ägare, deltagare, eller kontoadministratör kan ta bort den [precis i tid principer](security-center-just-in-time.md) via Azure portal.

En Security Center-användare kan inte ta bort aviseringen incidenter. På grund av säkerhetskrav, en [incidentavisering](security-center-managing-and-responding-alerts.md) anses vara skrivskyddade endast data.

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center-användare som tilldelats rollen Läsare, ägare, deltagare eller kontoadministratör kan du exportera [säkerhet kontaktdata](security-center-provide-security-contact-details.md) av:

- Utför en kopia från Azure-portalen
- Köra Azure REST API-anrop, hämta HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

En Security Center-användare som tilldelats rollen av kontoadministratören kan exportera den [precis i tid principer](security-center-just-in-time.md) som innehåller IP-adresser per:

- Utför en kopia från Azure-portalen
- Köra Azure REST API-anrop, hämta HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

En kontoadministratör kan exportera varningsinformationen av:

- Utför en kopia från Azure-portalen
- Köra Azure REST API-anrop, hämta HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Se [hämta säkerhetsaviseringar (hämta samling)](https://msdn.microsoft.com/library/mt704050.aspx) för mer information.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Begränsa användning av personliga data för profilering eller marknadsföring utan tillstånd
En användare i Security Center kan du välja genom att ta bort deras [säkerhet kontaktdata](security-center-provide-security-contact-details.md).

[Endast i tidsdata](security-center-just-in-time.md) betraktas som icke-identifierbara data och sparas i 30 dagar.

[Aviseringsdata](security-center-managing-and-responding-alerts.md) anses säkerhetsdata och sparas i två år.

## <a name="auditing-and-reporting"></a>Granskning och rapportering
Granskningsloggar säkerhet kontakta precis i tid och varna uppdateringar underhålls i [Azure aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar användardata finns [hanterar användardata i Azure Security Center undersökning](security-center-investigation-user-data.md).
