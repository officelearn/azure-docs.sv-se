---
title: E-postmeddelanden i Azure AD PIM | Microsoft Docs
description: Beskriver e-postaviseringar i Azure AD Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/14/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 6c329554b5854f113fb216f874fa5a918110f9c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059783"
---
# <a name="email-notifications-in-azure-ad-pim"></a>E-postaviseringar i Azure AD PIM

När viktiga händelser inträffar i Azure AD Privileged Identity Management (PIM), skickas e-postmeddelanden till relevant administratör eller användare. Till exempel skickar PIM e-postmeddelanden för följande händelser:

- När en aktivering av Privilegierade roller väntar på godkännande
- När en begäran om aktivering av Privilegierade roller har godkänts
- När en privilegierad roll är aktiverad
- När en privilegierad roll tilldelas
- När Azure AD PIM är aktiverat

Med början vid slutet av juli 2018, har e-postmeddelanden som skickas via PIM en ny avsändarens e-postadress och en ny visuell design. Den här uppdateringen påverkar både PIM för Azure AD och PIM för Azure-resurser. Alla händelser som tidigare utlösts ett e-postmeddelande kommer att fortsätta att skicka ett e-postmeddelande. Den här uppdateringen är enbart en visuella ändringen utan ändringar i funktionalitet.

## <a name="sender-email-address"></a>Avsändarens e-postadress

E-postmeddelanden har med början vid slutet av juli 2018, följande adress:

- E-postadress:  **azure-noreply@microsoft.com**
- Visningsnamn: Microsoft Azure

Tidigare hade e-postaviseringar följande adress:

- E-postadress:  **azureadnotifications@microsoft.com**
- Visningsnamn: Microsoft Azure AD-meddelandetjänst

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

- [Så här hanterar du rollaktiveringsinställningar i Azure AD PIM](pim-how-to-change-default-settings.md)
- [Godkännanden i Azure AD PIM](azure-ad-pim-approval-workflow.md)
