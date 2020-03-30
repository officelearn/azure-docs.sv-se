---
title: Visa och tilldela administratörsrollbehörigheter – Azure AD | Microsoft-dokument
description: Du kan nu se och hantera medlemmar i en Azure AD-administratörsroll i portalen. För dem som ofta hanterar rolluppgifter.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900914"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Visa och tilldela administratörsroller i Azure Active Directory

Du kan nu se och hantera alla medlemmar i administratörsrollerna i Azure Active Directory-portalen. Om du ofta hanterar rolltilldelningar föredrar du förmodligen den här upplevelsen. Och om du någonsin undrat "Vad sjutton gör dessa roller egentligen gör?", kan du se en detaljerad lista över behörigheter för var och en av Azure AD-administratörsrollerna.

## <a name="view-all-roles"></a>Visa alla roller

Sök efter och välj **Azure Active Directory**. Välj **Roller och administratörer** om du vill visa listan över alla tillgängliga roller.

Klicka på ellipsen till höger om varje rad för att öppna den detaljerade beskrivningen av rollen.

![lista över roller i Azure AD-portalen](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Visa mina roller

Det är lätt att visa dina egna behörigheter också. Välj **Din roll** på sidan Roller och **administratörer** om du vill se de roller som för närvarande har tilldelats dig.

## <a name="view-assignments-for-a-role"></a>Visa tilldelningar för en roll

Klicka på en roll om du vill visa de användare som tilldelats rollen. Du kan välja **Hantera i PIM** för ytterligare hanteringsfunktioner. Privilegierade rolladministratörer kan ändra "Permanenta" (alltid aktiva i rollen) tilldelningar till "Berättigade" (i rollen endast när de är upphöjda). Om du inte har PIM kan du fortfarande välja **Hantera i PIM** för att registrera dig för en utvärderingsversion. Privilegierad identitetshantering kräver en [Azure AD Premium P2-licensplan](../privileged-identity-management/subscription-requirements.md).

![lista över medlemmar i en administratörsroll](./media/directory-manage-roles-portal/member-list.png)

Om du är global administratör eller administratör för privilegierade roller kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller välja en medlem för att se deras aktiva tilldelade roller.

## <a name="view-a-users-role-permissions"></a>Visa en användares rollbehörigheter

När du visar en rolls medlemmar väljer du **Beskrivning** om du vill visa den fullständiga listan över behörigheter som beviljats av rolltilldelningen. Sidan innehåller länkar till relevant dokumentation som hjälper dig att hantera katalogroller.

![lista över behörigheter för en administratörsroll](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Nästa steg

* Känn dig välkommen att dela med oss på [Azure AD administrativa roller forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och administratörsrolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
* Standardanvändarbehörigheter finns i en [jämförelse av standardbehörighet för gäst- och medlemsanvändare](../fundamentals/users-default-permissions.md).
