---
title: Använda anpassade roller för Azure-resurser i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du använder anpassade roller för Azure-resurser i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847054"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Använda anpassade roller för Azure-resurser i privilegierad identitetshantering

Du kan behöva tillämpa strikta PIM-inställningar (Privileged Identity Management) på vissa användare i en privilegierad roll i din Azure Active Directory-organisation (Azure AD), samtidigt som du ger andra större självständighet. Tänk till exempel på ett scenario där din organisation anställer flera kontraktsmedarbetare för att hjälpa till med utvecklingen av ett program som körs i en Azure-prenumeration.

Som resursadministratör vill du att medarbetare ska vara berättigade till åtkomst utan att behöva godkännas. Alla kontraktsmedarbetare måste dock godkännas när de begär åtkomst till organisationens resurser.

Följ stegen i nästa avsnitt för att konfigurera riktade inställningar för privilegierad identitetshantering för Azure-resursroller.

## <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Om du vill skapa en anpassad roll för en resurs följer du stegen som beskrivs i [Skapa anpassade roller för Azure-rollbaserad åtkomstkontroll](../role-based-access-control-custom-roles.md).

När du skapar anpassad roll ska du inkludera ett beskrivande namn så att du enkelt kan komma ihåg vilken inbyggd roll du avsåg att duplicera.

> [!NOTE]
> Kontrollera att den anpassade rollen är en dubblett av den inbyggda roll som du vill duplicera och att dess omfattning matchar den inbyggda rollen.

## <a name="apply-pim-settings"></a>Använda PIM-inställningar

När rollen har skapats i din Azure AD-organisation går du till sidan **Privilegierad identitetshantering - Azure-resurser** i Azure-portalen. Välj den resurs som rollen gäller för.

![Fönstret Privilegierad identitetshantering - Azure-resurser](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurera rollinställningar för privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md) som ska gälla för dessa medlemmar i rollen.

Slutligen [tilldelar du roller](pim-resource-roles-assign-roles.md) till den olika grupp medlemmar som du vill inrikta dig på med dessa inställningar.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md)
- [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)
