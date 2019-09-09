---
title: E-postmeddelanden i PIM – Azure Active Directory | Microsoft Docs
description: Beskriver e-postaviseringar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0db4828ef7a6d3392ce53b2f2f95b0f7f014d65
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804540"
---
# <a name="email-notifications-in-pim"></a>E-postmeddelanden i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du se när viktiga händelser inträffar, till exempel när en roll tilldelas eller aktive ras. PIM håller dig informerad genom att skicka e-postmeddelanden till dig och andra deltagare. Dessa e-postmeddelanden kan också innehålla länkar till relevanta uppgifter, t. ex. aktivering eller förnyelse av en roll. Den här artikeln beskriver vad dessa e-postmeddelanden ser ut när de skickas och vem som tar emot dem.

## <a name="sender-email-address-and-subject-line"></a>Avsändar-e-postadress och ämnesrad

E-postmeddelanden som skickas från PIM för både Azure AD-och Azure-resurs roller har följande e-postadress till avsändar tjänsten:

- E-post adress: **Azure-\@noreply Microsoft.com**
- Visnings namn: Microsoft Azure

Dessa e-postmeddelanden innehåller ett **PIM** -prefix på ämnes raden. Här är ett exempel:

- PIM Alain Charon har permanent tilldelats rollen som säkerhets kopierings läsare

## <a name="pim-emails-for-azure-ad-roles"></a>PIM-e-post för Azure AD-roller

PIM skickar e-postmeddelanden när följande händelser inträffar för Azure AD-roller:

- När en aktivering av privilegierade roller väntar på godkännande
- När en aktiverings förfrågan för en privilegie rad roll har slutförts
- När Azure AD PIM är aktiverat

Vem som får dessa e-postmeddelanden för Azure AD-roller beror på din roll, händelsen och meddelande inställningen:

| Användare | Roll aktiveringen väntar på godkännande | Begäran om roll aktivering har slutförts | PIM är aktiverat |
| --- | --- | --- | --- |
| Privilegierad rolladministratör</br>(Aktive rad/berättigad) | Ja</br>(endast om inga uttryckliga god kännare har angetts) | Ja* | Ja |
| Säkerhetsadministratör</br>(Aktive rad/berättigad) | Nej | Ja* | Ja |
| Global administratör</br>(Aktive rad/berättigad) | Nej | Ja* | Ja |

\*Om inställningen [ **meddelanden** ](pim-how-to-change-default-settings.md#notifications) är inställd på **Aktivera**.

Följande visar ett exempel på ett e-postmeddelande som skickas när en användare aktiverar en Azure AD-roll för den fiktiva Contoso-organisationen.

![Nytt PIM-e-postmeddelande för Azure AD-roller](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Veckovis PIM sammandrag av e-post för Azure AD-roller

En veckovis PIM-e-post för Azure AD-roller skickas till privilegierade roll administratörer, säkerhets administratörer och globala administratörer som har aktiverat PIM. Detta vecko Visa e-postmeddelande innehåller en ögonblicks bild av PIM-aktiviteter för veckan samt privilegierade roll tilldelningar. Den är endast tillgänglig för klienter i det offentliga molnet. Här är ett exempel på ett e-postmeddelande:

![Veckovis PIM sammandrag av e-post för Azure AD-roller](./media/pim-email-notifications/email-directory-weekly.png)

E-postmeddelandet innehåller fyra paneler:

| Upprepa | Beskrivning |
| --- | --- |
| **Användare aktiverade** | Antal gånger som användare har aktiverat sin berättigade roll i klienten. |
| **Användare som gjorts permanenta** | Antalet gånger som användare med en berättigad tilldelning görs permanenta. |
| **Roll tilldelningar i PIM** | Antal gånger som användare tilldelas en berättigad roll i PIM. |
| **Roll tilldelningar utanför PIM** | Antal gånger som användare tilldelas en permanent roll utanför PIM (i Azure AD). |

I avsnittet **Översikt över de främsta rollerna** visas de fem främsta rollerna i klienten baserat på det totala antalet permanenta och kvalificerade administratörer för varje roll. Länken **ta åtgärd** öppnar PIM- [guiden](pim-security-wizard.md) där du kan konvertera permanenta administratörer till berättigade administratörer i batchar.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-post för Azures resurs roller

PIM skickar e-post till ägare och användar åtkomst administratörer när följande händelser inträffar för Azure-resurs roller:

- När en roll tilldelning väntar på godkännande
- När en roll tilldelas
- När en roll snart upphör att gälla
- När en roll är kvalificerad att utöka
- När en roll förnyas av en användare
- När en begäran om att aktivera en roll är slutförd

PIM skickar e-post till slutanvändare när följande händelser inträffar för Azure-resurs roller:

- När en roll tilldelas till användaren
- När en användares roll har upphört att gälla
- När en användares roll utökas
- När en användares roll aktiverings förfrågan har slutförts

Följande visar ett exempel på ett e-postmeddelande som skickas när en användare tilldelas en Azure-resurs roll för den fiktiva Contoso-organisationen.

![Nytt PIM-e-postmeddelande för Azures resurs roller](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-roller i PIM](pim-how-to-change-default-settings.md)
- [Godkänn eller neka begär Anden för Azure AD-roller i PIM](azure-ad-pim-approval-workflow.md)
