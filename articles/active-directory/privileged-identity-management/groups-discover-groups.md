---
title: Identifiera roll tilldelnings bara grupper att hantera i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du identifierar roll tilldelnings bara grupper som ska hanteras som privilegierade åtkomst grupper i Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506009"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Identifiera privilegierade åtkomst grupper (för hands version) för att hantera i Privileged Identity Management

I Azure Active Directory (Azure AD) kan du tilldela inbyggda Azure AD-roller till moln grupper för att förenkla hanteringen av roll tilldelningar. Nu kan du använda Privileged Identity Management (PIM) för att tilldela behörighet till medlemskap eller ägarskap för de här grupperna, för att skydda Azure AD-roller och säkra åtkomst. Innan du kan hantera en Azure AD-roll som kan tilldelas en grupp som privilegie rad åtkomst grupp i Privileged Identity Management måste du identifiera den och ta med den i hanteringen i PIM.

## <a name="discover-resources"></a>Identifiera resurser

1. [Logga in på Azure AD](https://aad.portal.azure.com) med administratörs behörighet för privilegie rad roll.
1. Skapa en roll tilldelnings bara grupp i Azure AD. Du måste vara ägare till gruppen för att kunna identifiera och hantera den med Privileged Identity Management.
1. Öppna **Privileged Identity Management**.
1. Välj **privilegie rad åtkomst (för hands version)**.

    ![Kommandot identifiera grupper för första gången](./media/groups-discover-groups/groups-discover-groups.png)

1. Välj **identifiera grupper**.
1. Sök efter grupp namn.
1. Välj din grupp och välj **hantera grupper** för att ta med den under PIM-hantering.

    ![Identifiera grupper utan resurser som visas för första gången](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > När en privilegie rad åtkomst grupp hanteras kan den inte tas bort från hanteringen. Detta förhindrar att en annan resurs administratör tar bort Privileged Identity Management inställningar.

1. Om du ser ett meddelande om att bekräfta onboarding av den valda resursen för hantering väljer du **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera tilldelningar av privilegierade åtkomst grupper i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Tilldela privilegierade åtkomst grupper i Privileged Identity Management](pim-resource-roles-assign-roles.md)
