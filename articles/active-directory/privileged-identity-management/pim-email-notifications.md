---
title: E-postmeddelanden i PIM - Azure | Microsoft Docs
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
ms.component: pim
ms.date: 09/07/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: de1d29d3ab1b370257c3a2d6b6ff9f677197fc2a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303072"
---
# <a name="email-notifications-in-pim"></a>E-postmeddelanden i PIM

När viktiga händelser inträffar i Azure AD Privileged Identity Management (PIM), skickas e-postmeddelanden. Till exempel skickar PIM e-postmeddelanden för följande händelser:

- När en aktivering av Privilegierade roller väntar på godkännande
- När en begäran om aktivering av Privilegierade roller är slutförd
- När en privilegierad roll är aktiverad
- När en privilegierad roll tilldelas
- När Azure AD PIM är aktiverat

E-postmeddelanden skickas till följande administratörer:

- Privilegierad rolladministratör
- Säkerhetsadministratör

E-postmeddelanden skickas också till användaren som har den privilegierade rollen för följande händelser:

- När en begäran om aktivering av Privilegierade roller är slutförd
- När en privilegierad roll tilldelas

Med början vid slutet av juli 2018, har e-postmeddelanden som skickas via PIM nya avsändarens e-postadress och en ny visuell design. Den här uppdateringen påverkar både PIM för Azure AD och PIM för Azure-resurser. Alla händelser som tidigare utlösts ett e-postmeddelande kommer att fortsätta att skicka ett e-postmeddelande. E-postmeddelanden har uppdaterat innehåll att tillhandahålla mer riktad information.

## <a name="sender-email-address"></a>Avsändarens e-postadress

E-postmeddelanden har med början vid slutet av juli 2018, följande adress:

- E-postadress:  **azure-noreply@microsoft.com**
- Visningsnamn: Microsoft Azure

Tidigare hade e-postaviseringar följande adress:

- E-postadress:  **azureadnotifications@microsoft.com**
- Visningsnamn: Microsoft Azure AD-meddelandetjänst

## <a name="email-subject-line"></a>Ämnesraden för e-post

Med början vid slutet av juli 2018, e-postmeddelanden för både Azure AD och Azure-resursroller har en **PIM** prefix i ämnesraden. Här är ett exempel:

- PIM: Alain Charon tilldelades permanent läsarroll för säkerhetskopiering.

## <a name="pim-emails-for-azure-ad-roles"></a>PIM-e-postmeddelanden för Azure AD-roller

Med början vid slutet av juli 2018, har e-postmeddelanden för PIM för Azure AD-roller en uppdaterad design. Nedan visas ett exempel e-postmeddelande som skickas när en användare aktiverar en privilegierad roll för det fiktiva företaget Contoso.

![Nytt PIM e-postmeddelande för Azure AD-roller](./media/pim-email-notifications/email-directory-new.png)

När en användare har aktiverat en privilegierad roll kan tittat tidigare e-postmeddelandet som liknar följande.

![Gamla e-PIM för Azure AD-roller](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-postmeddelanden för Azure-resursroller

E-postmeddelanden för PIM för Azure-resursroller har startar i slutet av juli 2018, en uppdaterad design. Nedan visas ett exempel e-postmeddelande som skickas när en användare har tilldelats en privilegierad roll för det fiktiva företaget Contoso.

![Nytt PIM e-postmeddelande för Azure-resursroller](./media/pim-email-notifications/email-resources-new.png)

När en användare har tilldelats en privilegierad roll, tittat tidigare e-postmeddelandet som liknar följande.

![Gamla e-PIM för Azure-resursroller](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD directory rollinställningar i PIM](pim-how-to-change-default-settings.md)
- [Godkänn eller neka begäranden för Azure AD-katalogroller i PIM](azure-ad-pim-approval-workflow.md)
