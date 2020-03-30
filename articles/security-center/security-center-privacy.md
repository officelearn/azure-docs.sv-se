---
title: Hantera användardata i Azure Security Center | Microsoft-dokument
description: Lär dig hur du hanterar användardata i Azure Security Center. Hantering av användardata omfattar möjligheten att komma åt, ta bort eller exportera data.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 6edea1d0de53e2dc9f764de26209dc1f3110556e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978604"
---
# <a name="manage-user-data-in-azure-security-center"></a>Hantera användardata i Azure Security Center
Den här artikeln innehåller information om hur du kan hantera användardata i Azure Security Center. Hantering av användardata omfattar möjligheten att komma åt, ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

En Security Center-användare som tilldelats rollen som läsare, ägare, deltagare eller kontoadministratör kan komma åt kunddata i verktyget. Mer information om rollen Kontoadministratör finns [i Inbyggda roller för Azure-rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md) om du vill veta mer om rollerna Läsare, Ägare och Deltagare. Se [Azure-prenumerationsadministratörer](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Söker efter och identifierar personuppgifter
En Security Center-användare kan visa sina personuppgifter via Azure-portalen. Security Center lagrar endast säkerhetskontaktinformation som e-postadresser och telefonnummer. Mer information finns [i Ange information om säkerhetskontakter i Azure Security Center](security-center-provide-security-contact-details.md).

I Azure-portalen kan en användare visa tillåtna IP-konfigurationer med Hjälp av Security Centers åtkomstfunktion för just-in-time-vm. Mer information finns [i Hantera åtkomst till virtuella datorer med just-in-time](security-center-just-in-time.md).

I Azure-portalen kan en användare visa säkerhetsaviseringar som tillhandahålls av Security Center, inklusive IP-adresser och angripare. Mer information finns i [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klassificera personuppgifter
Du behöver inte klassificera personuppgifter som finns i Security Centers säkerhetskontaktfunktion. De data som sparas är en e-postadress (eller flera e-postadresser) och ett telefonnummer. [Kontaktdata](security-center-provide-security-contact-details.md) valideras av Security Center.

Du behöver inte klassificera IP-adresser och portnummer som sparats av Security Centers [just-in-time-funktion.](security-center-just-in-time.md)

Endast en användare som tilldelats rollen som administratör kan klassificera personuppgifter genom [att visa aviseringar](security-center-managing-and-responding-alerts.md) i Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Säkerställande och kontroll av tillgång till personuppgifter
En Security Center-användare som tilldelats rollen som läsare, ägare, deltagare eller kontoadministratör kan komma åt [säkerhetskontaktdata](security-center-provide-security-contact-details.md).

En Security Center-användare som tilldelats rollen som läsare, ägare, deltagare eller kontoadministratör kan komma åt sina [just-in-time-principer.](security-center-just-in-time.md)

En Security Center-användare som tilldelats rollen som läsare, ägare, deltagare eller kontoadministratör kan visa sina [aviseringar](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Uppdatera personliga data
En Security Center-användare som tilldelats rollen som ägare, deltagare eller kontoadministratör kan uppdatera [säkerhetskontaktdata](security-center-provide-security-contact-details.md) via Azure-portalen.

En Security Center-användare som tilldelats rollen som ägare, deltagare eller kontoadministratör kan uppdatera sina [just-in-time-principer](security-center-just-in-time.md).

En kontoadministratör kan inte redigera varningsincidenter. En [aviseringsincident](security-center-managing-and-responding-alerts.md) betraktas som säkerhetsdata och är skrivskyddad.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center-användare som tilldelats rollen som ägare, deltagare eller kontoadministratör kan ta bort [säkerhetskontaktdata](security-center-provide-security-contact-details.md) via Azure-portalen.

En Security Center-användare som tilldelats rollen som ägare, deltagare eller kontoadministratör kan ta bort [just-in-time-principerna](security-center-just-in-time.md) via Azure-portalen.

En Security Center-användare kan inte ta bort varningsincidenter. Av säkerhetsskäl betraktas en [varningsincident](security-center-managing-and-responding-alerts.md) som skrivskyddade data.

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center-användare som tilldelats rollen som läsare, ägare, deltagare eller kontoadministratör kan exportera [säkerhetskontaktdata](security-center-provide-security-contact-details.md) genom att:

- Kopiera från Azure-portalen
- Kör Azure REST API-anrop, FÅ HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

En Security Center-användare som tilldelats rollen som kontoadministratör kan exportera [just-in-time-principerna](security-center-just-in-time.md) som innehåller IP-adresserna genom att:

- Kopiera från Azure-portalen
- Kör Azure REST API-anrop, FÅ HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

En kontoadministratör kan exportera varningsinformationen genom att:

- Kopiera från Azure-portalen
- Kör Azure REST API-anrop, FÅ HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Mer information finns i [Hämta säkerhetsvarningar (GET Collection)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Begränsa användningen av personuppgifter för profilering eller marknadsföring utan samtycke
En Security Center-användare kan välja bort genom att ta bort sina [säkerhetskontaktdata](security-center-provide-security-contact-details.md).

[Just-in-time-data](security-center-just-in-time.md) anses vara icke-identifierbara data och lagras under en period av 30 dagar.

[Varningsdata](security-center-managing-and-responding-alerts.md) betraktas som säkerhetsdata och lagras under en period av två år.

## <a name="auditing-and-reporting"></a>Revision och rapportering
Granskningsloggar för säkerhetskontakt, just-in-time och varningsuppdateringar underhålls i [Azure Activity Logs](../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar användardata finns [i Hantera användardata som hittades i en Undersökning av Azure Security Center](security-center-investigation-user-data.md).
