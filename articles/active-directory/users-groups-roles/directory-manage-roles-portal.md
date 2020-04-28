---
title: Visa och tilldela administratörs roll behörigheter – Azure AD | Microsoft Docs
description: Nu kan du se och hantera medlemmar i en administratörs roll för Azure AD i portalen. För dem som ofta hanterar roll tilldelningar.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900914"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Visa och tilldela administratörs roller i Azure Active Directory

Nu kan du se och hantera alla medlemmar i administratörs rollerna i Azure Active Directory portalen. Om du ofta hanterar roll tilldelningar kommer du förmodligen att föredra den här upplevelsen. Och om du skulle undra "vad de här rollerna egentligen gör?" kan du se en detaljerad lista över behörigheter för var och en av administratörs rollerna för Azure AD.

## <a name="view-all-roles"></a>Visa alla roller

Sök efter och välj **Azure Active Directory**. Välj **roller och administratörer** om du vill se en lista över alla tillgängliga roller.

Öppna den detaljerade beskrivningen av rollen genom att klicka på ellipsen till höger om varje rad.

![lista över roller i Azure AD Portal](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Visa mina roller

Det är enkelt att visa dina egna behörigheter. Välj **din roll** på sidan **roller och administratörer** för att se de roller som för närvarande är tilldelade till dig.

## <a name="view-assignments-for-a-role"></a>Visa tilldelningar för en roll

Klicka på en roll om du vill visa de användare som har tilldelats rollen. Du kan välja **Hantera i PIM** för ytterligare hanterings funktioner. Privilegierade roll administratörer kan ändra "permanent" (alltid aktivt i rollen) tilldelningar till "kvalificerat" (i rollen endast när det har förhöjds). Om du inte har PIM kan du fortfarande välja **Hantera i PIM** för att registrera dig för en utvärderings version. Privileged Identity Management kräver en [Azure AD Premium P2-licens plan](../privileged-identity-management/subscription-requirements.md).

![lista över medlemmar i en administratörs roll](./media/directory-manage-roles-portal/member-list.png)

Om du är global administratör eller administratör för privilegierade roller kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller välja en medlem för att se deras aktiva tilldelade roller.

## <a name="view-a-users-role-permissions"></a>Visa en användares roll behörigheter

När du visar en Rolls medlemmar väljer du **Beskrivning** för att se en fullständig lista över behörigheter som beviljats av roll tilldelningen. Sidan innehåller länkar till relevant dokumentation för att hjälpa dig att hantera katalog roller.

![lista över behörigheter för en administratörs roll](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](directory-assign-admin-roles.md).
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
