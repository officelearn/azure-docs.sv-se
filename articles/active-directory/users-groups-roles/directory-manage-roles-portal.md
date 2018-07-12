---
title: Visa medlemmar i en administratörsroll och behörigheter i Azure Active Directory | Microsoft Docs
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
ms.date: 07/10/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5a42f48e85eea95211b36e0c08dcb0edb4928a20
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38489930"
---
# <a name="view-members-and-descriptions-of-administrator-roles-in-azure-active-directory"></a>Visa medlemmar och beskrivningar av administratörsroller i Azure Active Directory

Du kan nu se och hantera alla medlemmar i administratörsroller i Azure Active Directory-portalen. Om du ofta hantera rolltilldelningar, kommer du förmodligen föredrar säkerhetsvarningen. Och om du någonsin undrat ”vad den markeringen rollerna verkligen gör”?, kan du se en detaljerad lista över behörigheter för var och en av Azure AD-administratörsroller.

Det är enkelt att se dina egna behörigheter även. Klicka på **din roll** få Snabbåtkomst till din användarsidan för en lista över alla dina aktiva tilldelade roller. Klicka på de tre punkterna till höger på varje rad för att öppna den detaljerade beskrivningen av rollen.

![lista över roller i Azure AD-portalen](./media/directory-manage-roles-portal/role-list.png)

Markera hela raden att visa listan över tilldelade medlemmar. Du kan välja **hantera i PIM** för ytterligare hanteringsfunktioner. Privilegierade Rolladministratörer kan ändra ”Permanent” (alltid aktiv i rollen) tilldelningar till ”berättigade” (i rollen bara om utökad). Om du inte har PIM kan du fortfarande välja **hantera i PIM** att registrera dig för en utvärderingsversion. Privileged Identity Management kräver en [Azure AD Premium P2-licensplan](../privileged-identity-management/subscription-requirements.md).

![lista över medlemmar i en administratörsroll](./media/directory-manage-roles-portal/member-list.png)

Om du är en Global administratör eller en privilegierad roll-administratör kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller Välj medlemmar för att gå till användarsidan att se deras tilldelade roller aktiv. 

## <a name="detailed-role-permissions-in-the-portal"></a>Detaljerade behörigheter i portalen

När du visar en rollmedlemmar, Välj **beskrivning** att se den fullständiga listan med behörigheter som beviljats av rolltilldelningen. Sidan innehåller länkar till relevant dokumentation som hjälper dig hantera katalogroller.

![listan över behörigheter för en administratörsroll](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Nästa steg

* Passa på att dela med oss på den [Azure AD-forum för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och administratören rolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
* Standardinställningar för användarbehörigheter, finns en [jämförelse av standard-Gäst och medlemmen användarbehörigheter](../fundamentals/users-default-permissions.md).
