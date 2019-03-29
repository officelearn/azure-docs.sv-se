---
title: E-postmeddelanden i PIM - Azure Active Directory | Microsoft Docs
description: Beskriver e-postaviseringar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576955"
---
# <a name="email-notifications-in-pim"></a>E-postmeddelanden i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) får du reda på när viktiga händelser inträffar, till exempel när en roll tilldelas eller aktiveras. PIM håller dig informerad genom att skicka e-postmeddelanden du och andra deltagare. Dessa e-postmeddelanden kan också innehålla länkar till relevanta uppgifter, sådana aktivera eller förnya en roll. Den här artikeln beskriver hur dessa e-postmeddelanden som ska se ut när de skickas och som tar emot dem.

## <a name="sender-email-address-and-subject-line"></a>Avsändaren e-postadress och ämne rad

E-postmeddelanden skickas från PIM för både Azure AD och Azure-resursroller har följande e-postadress för avsändaren:

- E-postadress: **azure noreply\@microsoft.com**
- Visningsnamn: Microsoft Azure

Dessa e-postmeddelanden innehåller en **PIM** prefix i ämnesraden. Här är ett exempel:

- PIM: Alain Charon har permanent tilldelats rollen Läsare för säkerhetskopiering

## <a name="pim-emails-for-azure-ad-roles"></a>PIM-e-postmeddelanden för Azure AD-roller

PIM skickar e-postmeddelanden när följande händelser inträffar för Azure AD-roller:

- När en aktivering av Privilegierade roller väntar på godkännande
- När en begäran om aktivering av Privilegierade roller är slutförd
- När Azure AD PIM är aktiverat

Vem som får dessa e-postmeddelanden för Azure AD-roller beror på din roll och händelsen meddelanden inställning:

| Användare | Rollaktivering väntar på godkännande | Begäran om rollaktivering har slutförts | PIM är aktiverat |
| --- | --- | --- | --- |
| Privilegierad rolladministratör</br>(Aktiverat/berättigade) | Ja</br>(endast om inga godkännare som explicit anges) | Ja* | Ja |
| Säkerhetsadministratör</br>(Aktiverat/berättigade) | Nej | Ja* | Ja |
| Global administratör</br>(Aktiverat/berättigade) | Nej | Ja* | Ja |

\* Om den [ **meddelanden** inställningen](pim-how-to-change-default-settings.md#notifications) är inställd på **aktivera**.

Nedan visas ett exempel e-postmeddelande som skickas när en användare aktiverar en Azure AD-roll för det fiktiva företaget Contoso.

![Nytt PIM e-postmeddelande för Azure AD-roller](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Veckans PIM sammanfattad e-postmeddelande för Azure AD-roller

En veckovis PIM sammanfattande e-postmeddelande för Azure AD-roller skickas till administratörer med privilegierad roll, säkerhetsadministratörer och globala administratörer som har aktiverat PIM. Den här veckans e-postmeddelande innehåller en ögonblicksbild av PIM-aktiviteter för vecka samt Priviligierade rolltilldelningar. Det är bara tillgängliga för klienter i det offentliga molnet. Här är ett exempel e-postmeddelande:

![Veckans PIM sammanfattad e-postmeddelande för Azure AD-roller](./media/pim-email-notifications/email-directory-weekly.png)

E-postmeddelandet innehåller fyra paneler:

| Upprepa | Beskrivning |
| --- | --- |
| **Användare som har aktiverats** | Antal gånger som användare aktiverat sin berättigad roll i klienten. |
| **Användare som har gjorts permanenta** | Antal gånger som användare med en berättigad uppgift görs permanent. |
| **Rolltilldelningar i PIM** | Antal gånger som användare har tilldelats en berättigad roll i PIM. |
| **Rolltilldelningar utanför PIM** | Antal gånger som användare har tilldelats en permanent roll utanför PIM (i Azure AD). |

Den **översikt över övre rollerna** avsnittet listas de översta fem roller i din klient baserat på totala antalet permanent och berättigade administratörer för varje roll. Den **vidta åtgärder** länken öppnas den [PIM guiden](pim-security-wizard.md) där du kan konvertera permanenta administratörer till berättigade administratörer i batchar.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-postmeddelanden för Azure-resursroller

PIM skickar e-postmeddelanden till ägare och administratörer för åtkomst när följande händelser inträffar för Azure-resursroller:

- När en rolltilldelning väntar på godkännande
- När en roll tilldelas
- När en roll är snart att gälla
- När en roll är behörig att utöka
- När en roll förnyas av en slutanvändare
- När en begäran om rollaktivering är slutförd

PIM skickar e-post till slutanvändare när följande händelser inträffar för Azure-resursroller:

- När en roll tilldelas till användaren
- När en användares roll har upphört att gälla
- När en användares roll har utökats
- När en användares begäran om rollaktivering är slutförd

Nedan visas ett exempel e-postmeddelande som skickas när en användare har tilldelats en roll för Azure-resurs för det fiktiva företaget Contoso.

![Nytt PIM e-postmeddelande för Azure-resursroller](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-rollen i PIM](pim-how-to-change-default-settings.md)
- [Godkänn eller neka begäranden för Azure AD-roller i PIM](azure-ad-pim-approval-workflow.md)
