---
title: Visa och tilldela behörigheter i Azure Active Directory för administratör | Microsoft Docs
description: Du kan nu se och hantera medlemmar i en Azure AD-administratörsroll i portalen. För de som ofta hanterar rolltilldelningar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: cb2e5286eb8e910b555e221242a735f00dff4778
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182832"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Visa och tilldela administratörsroller i Azure Active Directory

Du kan nu se och hantera alla medlemmar i administratörsroller i Azure Active Directory-portalen. Om du ofta hantera rolltilldelningar, kommer du förmodligen föredrar säkerhetsvarningen. Och om du någonsin undrat ”vad den markeringen rollerna verkligen gör”?, kan du se en detaljerad lista över behörigheter för var och en av Azure AD-administratörsroller.

Det är enkelt att se dina egna behörigheter även. Klicka på **din roll** få Snabbåtkomst till din användarsidan för en lista över alla dina aktiva tilldelade roller. Klicka på de tre punkterna till höger på varje rad för att öppna den detaljerade beskrivningen av rollen.

![lista över roller i Azure AD-portalen](./media/directory-manage-roles-portal/role-list.png)

Välj raden för en roll att visa de användare som tilldelats till rollen. Du kan välja **hantera i PIM** för ytterligare hanteringsfunktioner. Privilegierade Rolladministratörer kan ändra ”Permanent” (alltid aktiv i rollen) tilldelningar till ”berättigade” (i rollen bara om utökad). Om du inte har PIM kan du fortfarande välja **hantera i PIM** att registrera dig för en utvärderingsversion. Privileged Identity Management kräver en [Azure AD Premium P2-licensplan](../privileged-identity-management/subscription-requirements.md).

![lista över medlemmar i en administratörsroll](./media/directory-manage-roles-portal/member-list.png)

Om du är en Global administratör eller en privilegierad roll-administratör kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller Välj medlemmar för att se deras tilldelade roller aktiv.

## <a name="view-role-permissions"></a>Visa behörigheter

När du visar en rollmedlemmar, Välj **beskrivning** att se den fullständiga listan med behörigheter som beviljats av rolltilldelningen. Sidan innehåller länkar till relevant dokumentation som hjälper dig hantera katalogroller.

![listan över behörigheter för en administratörsroll](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Nästa steg

* Passa på att dela med oss på den [Azure AD-forum för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och administratören rolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
* Standardinställningar för användarbehörigheter, finns en [jämförelse av standard-Gäst och medlemmen användarbehörigheter](../fundamentals/users-default-permissions.md).
