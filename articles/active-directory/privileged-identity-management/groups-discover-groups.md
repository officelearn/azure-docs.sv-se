---
title: Identifiera en grupp som ska hanteras i Privileged Identity Management – Azure AD | Microsoft Docs
description: Lär dig hur du kan publicera roll tilldelnings bara grupper för hantering som privilegierade åtkomst grupper i Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f7b61b6fb065409a2fe4b2ed8f599f321cd1db
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542316"
---
# <a name="bring-a-privileged-access-groups-preview-into-privileged-identity-management"></a>Ta med privilegierade åtkomst grupper (för hands version) i Privileged Identity Management

I Azure Active Directory (Azure AD) kan du tilldela inbyggda Azure AD-roller till moln grupper för att förenkla hanteringen av roll tilldelningar. För att skydda Azure AD-roller och för att skydda åtkomst kan du nu använda Privileged Identity Management (PIM) för att hantera just-in-Time-åtkomst för medlemmar eller ägare av dessa grupper. Om du vill hantera en Azure AD-roll som kan tilldelas gruppen som privilegie rad åtkomst grupp i Privileged Identity Management måste du ta med den i hantering i PIM.

## <a name="identify-groups-to-manage"></a>Identifiera grupper som ska hanteras

Du kan skapa en roll tilldelnings grupp i Azure AD enligt beskrivningen i [skapa en grupp tilldelnings bara grupp i Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). Du är ägare till gruppen för att hantera den med Privileged Identity Management.

1. [Logga in på Azure AD](https://aad.portal.azure.com) med administratörs behörighet för privilegie rad roll.
1. Välj **grupper** och välj sedan den roll tilldelnings bara grupp som du vill hantera i Pim. Du kan söka efter och filtrera listan.

    ![Hitta en roll tilldelnings bara grupp att hantera i PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Öppna gruppen och välj **privilegie rad åtkomst (förhands granskning)**.

    ![Öppna Privileged Identity Managements upplevelsen](./media/groups-discover-groups/groups-discover-groups.png)

1. Börja hantera tilldelningar i PIM.

    ![Hantera tilldelningar i Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> När en privilegie rad åtkomst grupp hanteras kan den inte tas bort från hanteringen. Detta förhindrar att en annan resurs administratör tar bort Privileged Identity Management inställningar.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera tilldelningar av privilegierade åtkomst grupper i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Tilldela privilegierade åtkomst grupper i Privileged Identity Management](pim-resource-roles-assign-roles.md)
