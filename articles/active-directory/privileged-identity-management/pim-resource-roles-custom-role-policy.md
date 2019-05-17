---
title: Använda anpassade roller för Azure-resurser i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du använder anpassade roller för Azure-resurser i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e814cde49374b52266f725b4d57657a507874ab
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602058"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Använda anpassade roller för Azure-resurser i PIM

Du kan behöva gäller strikta inställningar för Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för vissa medlemmar i en roll, samtidigt som det ger större självständigt för andra. Tänk dig ett scenario där anlitar din organisation flera kontrakt associates att underlätta utvecklingen av ett program som körs i en Azure-prenumeration.

Som administratör av en resurs kan du anställda att vara berättigade till åtkomst utan att kräva godkännande. Alla avtal associates måste vara godkända när de begär åtkomst till organisationens resurser.

Följ stegen som beskrivs i nästa avsnitt för att konfigurera inställningar för riktade PIM för Azure-resursroller.

## <a name="create-the-custom-role"></a>Skapa anpassad roll

Om du vill skapa en anpassad roll för en resurs, följer du stegen som beskrivs i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../role-based-access-control-custom-roles.md).

När du skapar en anpassad roll kan du inkludera ett beskrivande namn så att du lätt kan komma ihåg vilken inbyggd roll som du försöker att duplicera.

> [!NOTE]
> Kontrollera att den anpassade rollen är en dubblett av den inbyggda rollen som du vill duplicera och att den inbyggda rollen som överensstämmer med sitt omfång.

## <a name="apply-pim-settings"></a>Använda PIM-inställningar

När rollen skapas i din klient i Azure-portalen går du till den **Privileged Identity Management - Azure-resurser** fönstret. Välj den resurs som rollen gäller.

![The "Privileged Identity Management - Azure resources" pane](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurera PIM rollinställningar](pim-resource-roles-configure-role-settings.md) som ska tillämpas på dessa medlemmar i rollen.

Slutligen [tilldela roller](pim-resource-roles-assign-roles.md) till distinkta grupp med medlemmar som du vill använda med de här inställningarna.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
- [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)
