---
title: E-postmeddelanden i PIM - Azure Active Directory | Microsoft-dokument
description: Beskriver e-postmeddelanden i Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72756389"
---
# <a name="email-notifications-in-pim"></a>E-postmeddelanden i PIM

Pim (Privileged Identity Management) talar om när viktiga händelser inträffar i din Azure Active Directory -organisation (Azure AD), till exempel när en roll tilldelas eller aktiveras. Privilegierad identitetshantering håller dig informerad genom att skicka e-postmeddelanden till dig och andra deltagare. Dessa e-postmeddelanden kan också innehålla länkar till relevanta uppgifter, till exempel aktivera eller förnya en roll. I den här artikeln beskrivs hur dessa e-postmeddelanden ser ut, när de skickas och vem som tar emot dem.

## <a name="sender-email-address-and-subject-line"></a>E-postadress och ämnesrad avsändare

E-postmeddelanden som skickas från Privilegierad identitetshantering för både Azure AD- och Azure-resursroller har följande e-postadress för avsändaren:

- E-postadress: **\@azure-noreply microsoft.com**
- Visningsnamn: Microsoft Azure

Dessa e-postmeddelanden innehåller ett **PIM-prefix** i ämnesraden. Här är ett exempel:

- PIM: Alain Charon tilldelades rollen backup reader permanent

## <a name="notifications-for-azure-ad-roles"></a>Meddelanden för Azure AD-roller

Privilegierad identitetshantering skickar e-post när följande händelser inträffar för Azure AD-roller:

- När en privilegierad rollaktivering väntar på godkännande
- När en privilegierad rollaktiveringsbegäran har slutförts
- När Azure AD Privileged Identity Management är aktiverat

Vem som tar emot dessa e-postmeddelanden för Azure AD-roller beror på din roll, händelsen och inställningen för aviseringar:

| Användare | Rollaktivering väntar på godkännande | Begäran om rollaktivering har slutförts | PIM är aktiverat |
| --- | --- | --- | --- |
| Administratör för privilegierad roll</br>(Aktiverad/stödberättigande) | Ja</br>(endast om inga explicita godkännare har angetts) | Ja* | Ja |
| Säkerhetsadministratör</br>(Aktiverad/stödberättigande) | Inga | Ja* | Ja |
| Global administratör</br>(Aktiverad/stödberättigande) | Inga | Ja* | Ja |

\*Om [inställningen **Meddelanden** ](pim-how-to-change-default-settings.md#notifications) är inställd **på Aktivera**.

Följande visar ett exempel på e-post som skickas när en användare aktiverar en Azure AD-roll för den fiktiva Contoso-organisationen.

![Ny e-post för privilegierad identitetshantering för Azure AD-roller](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Veckovis sammanfattad hantering av privilegierad identitetshantering för Azure AD-roller

Ett veckovis e-postmeddelande om privilegierad identitetshantering för Azure AD-roller skickas till privilegierade rolladministratörer, säkerhetsadministratörer och globala administratörer som har aktiverat privilegierad identitetshantering. Det här e-postmeddelandet varje vecka innehåller en ögonblicksbild av aktiviteter för privilegierad identitetshantering för veckan samt privilegierade rolltilldelningar. Den är endast tillgänglig för klienter i det offentliga molnet. Här är ett exempel på e-post:

![Veckovis sammanfattad hantering av privilegierad identitetshantering för Azure AD-roller](./media/pim-email-notifications/email-directory-weekly.png)

E-postmeddelandet innehåller fyra paneler:

| Panel | Beskrivning |
| --- | --- |
| **Användare aktiverade** | Antal gånger användare aktiverat sin kvalificerade roll i klienten. |
| **Användare som gjorts permanenta** | Antal gånger användare med en berättigad tilldelning görs permanent. |
| **Rolltilldelningar i Privilegierad identitetshantering** | Antal gånger användare tilldelas en kvalificerad roll i Privilegierad identitetshantering. |
| **Rolltilldelningar utanför PIM** | Antal gånger användare tilldelas en permanent roll utanför Privilegierad identitetshantering (i Azure AD). |

I avsnittet **Översikt över dina topproller** visas de fem översta rollerna i din klientorganisation baserat på det totala antalet permanenta och kvalificerade administratörer för varje roll. Länken **Vidta åtgärd** öppnar [PIM-guiden](pim-security-wizard.md) där du kan konvertera permanenta administratörer till kvalificerade administratörer i batchar.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-postmeddelanden för Azure-resursroller

Privilegierad identitetshantering skickar e-post till ägare och användaråtkomstadministratörer när följande händelser inträffar för Azure-resursroller:

- När en rolltilldelning väntar på godkännande
- När en roll tilldelas
- När en roll snart ska löpa ut
- När en roll kan förlängas
- När en roll förnyas av en slutanvändare
- När en rollaktiveringsbegäran har slutförts

Privilegierad identitetshantering skickar e-post till slutanvändare när följande händelser inträffar för Azure-resursroller:

- När en roll tilldelas användaren
- När en användares roll har upphört att gälla
- När en användares roll utökas
- När en användares rollaktiveringsbegäran har slutförts

Följande visar ett exempel e-post som skickas när en användare tilldelas en Azure-resursroll för den fiktiva Contoso-organisationen.

![E-post för ny privilegierad identitetshantering för Azure-resursroller](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD-rollinställningar i privilegierad identitetshantering](pim-how-to-change-default-settings.md)
- [Godkänna eller neka begäranden om Azure AD-roller i privilegierad identitetshantering](azure-ad-pim-approval-workflow.md)
