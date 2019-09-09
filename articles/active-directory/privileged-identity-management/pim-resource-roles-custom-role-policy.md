---
title: Använd anpassade roller för Azure-resurser i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du använder anpassade roller för Azure-resurser i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d36514c97cf1f45ee0a435d3b716019d2762e5a
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804192"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Använd anpassade roller för Azure-resurser i PIM

Du kan behöva använda inställningar för strikt Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för vissa medlemmar i en roll, samtidigt som du ger större självständighet för andra. Tänk dig ett scenario där din organisation anställer flera kontrakts partner för att hjälpa till med utvecklingen av ett program som kommer att köras i en Azure-prenumeration.

Som resurs administratör vill du att personalen ska vara berättigad till åtkomst utan att kräva godkännande. Dock måste alla kontrakts partner godkännas när de begär åtkomst till organisationens resurser.

Följ stegen som beskrivs i nästa avsnitt för att konfigurera riktade PIM-inställningar för Azure-resurs roller.

## <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Om du vill skapa en anpassad roll för en resurs följer du stegen som beskrivs i [skapa anpassade roller för Azure Role-baserade Access Control](../role-based-access-control-custom-roles.md).

När du skapar en anpassad roll ska du inkludera ett beskrivande namn så att du enkelt kan komma ihåg vilken inbyggd roll du avsåg att duplicera.

> [!NOTE]
> Se till att den anpassade rollen är en dubblett av den inbyggda rollen som du vill duplicera och att dess omfattning matchar den inbyggda rollen.

## <a name="apply-pim-settings"></a>Använd PIM-inställningar

När rollen har skapats i din klient går du till fönstret **Privileged Identity Management Azure-resurser** i Azure Portal. Välj den resurs som rollen gäller för.

![Fönstret Privileged Identity Management-Azure-resurser](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurera inställningar för PIM-roller](pim-resource-roles-configure-role-settings.md) som ska gälla för dessa medlemmar i rollen.

Ange slutligen [roller](pim-resource-roles-assign-roles.md) för den distinkta gruppen med medlemmar som du vill ska vara mål för de här inställningarna.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure-resurs roller i PIM](pim-resource-roles-configure-role-settings.md)
- [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)
