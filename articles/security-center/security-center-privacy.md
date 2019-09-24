---
title: Hantera användar data i Azure Security Center | Microsoft Docs
description: " Lär dig hur du hanterar användar data i Azure Security Center. "
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
ms.openlocfilehash: 40299b2ff9a20792cf25828051d2f937f3f1e9da
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201281"
---
# <a name="manage-user-data-in-azure-security-center"></a>Hantera användar data i Azure Security Center
Den här artikeln innehåller information om hur du kan hantera användar data i Azure Security Center. Att hantera användar data innebär att du kan komma åt, ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

En Security Center användare som har tilldelats rollen läsare, ägare, deltagare eller konto administratör kan komma åt kund information i verktyget. Se [inbyggda roller för rollbaserad åtkomst kontroll i Azure](../role-based-access-control/built-in-roles.md) om du vill veta mer om rollerna läsare, ägare och deltagare. Se [Azure-prenumerations administratörer](../billing/billing-add-change-azure-subscription-administrator.md) för att lära dig mer om rollen konto administratör.

## <a name="searching-for-and-identifying-personal-data"></a>Söka efter och identifiera personliga data
En Security Center användare kan visa sina personliga data via Azure Portal. Security Center lagrar endast säkerhets kontakt information, till exempel e-postadresser och telefonnummer. Mer information finns i [Ange säkerhets kontakt uppgifter i Azure Security Center](security-center-provide-security-contact-details.md) .

I Azure Portal kan en användare Visa tillåtna IP-konfigurationer med hjälp Security Center av funktionen för just-in-Time-åtkomst för virtuella datorer. Mer information finns i [Manage virtual machine access using just in time](security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).

I Azure Portal kan en användare se säkerhets aviseringar som tillhandahålls av Security Center inklusive information om IP-adresser och angripare. Mer information finns i [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) .

## <a name="classifying-personal-data"></a>Klassificera person uppgifter
Du behöver inte klassificera person uppgifter som finns i Security Centerens säkerhets kontakt funktion. De data som sparas är en e-postadress (eller flera e-postadresser) och ett telefonnummer. [Kontakt data](security-center-provide-security-contact-details.md) verifieras av Security Center.

Du behöver inte klassificera IP-adresserna och port numren som sparats med Security Center funktionen [just-in-Time](security-center-just-in-time.md) .

Endast en användare som har tilldelats rollen administratör kan klassificera personliga data genom att [Visa aviseringar](security-center-managing-and-responding-alerts.md) i Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Skydda och kontrol lera åtkomst till personliga data
En Security Center användare som har tilldelats rollen som läsare, ägare, deltagare eller konto administratör kan komma åt [säkerhets kontakt data](security-center-provide-security-contact-details.md).

En Security Center användare som har tilldelats rollen läsare, ägare, deltagare eller konto administratör kan komma åt sina principer för [just-in-Time](security-center-just-in-time.md) .

En Security Center användare som tilldelats rollen läsare, ägare, deltagare eller konto administratör kan se sina [aviseringar](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Uppdatera personliga data
En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan uppdatera [säkerhets kontakt data](security-center-provide-security-contact-details.md) via Azure Portal.

En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan uppdatera sina principer för [just-in-Time](security-center-just-in-time.md).

En konto administratör kan inte redigera varnings incidenter. En [varnings incident](security-center-managing-and-responding-alerts.md) betraktas som säkerhets data och är skrivskyddad.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan ta bort [säkerhets kontakt data](security-center-provide-security-contact-details.md) via Azure Portal.

En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan ta bort just- [in-Time-principerna](security-center-just-in-time.md) via Azure Portal.

En Security Center användare kan inte ta bort varnings incidenter. På grund av säkerhets behov betraktas en [varnings incident](security-center-managing-and-responding-alerts.md) som skrivskyddade data.

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center användare som tilldelats rollen läsare, ägare, deltagare eller konto administratör kan exportera [säkerhets kontakt data](security-center-provide-security-contact-details.md) genom att:

- Utföra en kopia från Azure Portal
- Kör Azure REST API-anropet, Hämta HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

En Security Center användare som tilldelats rollen som konto administratör kan exportera [just-in-Time-principerna](security-center-just-in-time.md) som innehåller IP-adresserna genom att:

- Utföra en kopia från Azure Portal
- Kör Azure REST API-anropet, Hämta HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

En konto administratör kan exportera aviserings informationen genom att:

- Utföra en kopia från Azure Portal
- Kör Azure REST API-anropet, Hämta HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Mer information finns i [Hämta säkerhets aviseringar (Hämta samling)](https://msdn.microsoft.com/library/mt704050.aspx) .

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Begränsa användningen av person uppgifter för profilering eller marknadsföring utan medgivande
En Security Center användare kan välja att avanmäla sig genom att ta bort sina [säkerhets kontakt data](security-center-provide-security-contact-details.md).

[Precis i tid betraktas data](security-center-just-in-time.md) som icke-identifierbara data och bevaras under en period på 30 dagar.

[Aviserings data](security-center-managing-and-responding-alerts.md) anses vara säkerhets data och bevaras under en period av två år.

## <a name="auditing-and-reporting"></a>Granskning och rapportering
Gransknings loggar för säkerhets kontakt, just i tiden och aviserings uppdateringar underhålls i [Azure aktivitets loggar](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar användar data finns [i hantera användar data som finns i en Azure Security Center undersökning](security-center-investigation-user-data.md).
