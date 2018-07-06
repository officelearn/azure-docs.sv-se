---
title: Visa medlemmarna i en administratörsroll i Azure Active Directory | Microsoft Docs
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
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 588e475a451ea00e52dd995edc3f1bf0e284044f
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872328"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>Visa alla medlemmar i en administratörsroll i Azure Active Directory

Du kan nu se och hantera alla medlemmar i administratörsroller i Azure Active Directory-portalen. Om du ofta hantera rolltilldelningar, kommer du förmodligen föredrar säkerhetsvarningen. Och om du någonsin undrat ”vad den markeringen rollerna verkligen gör”?, kan du se en detaljerad lista över behörigheter för var och en av Azure AD-administratörsroller.

Det är enkelt att se dina egna behörigheter även. Klicka på **din roll** få Snabbåtkomst till din användarsidan för en lista över alla dina aktiva tilldelade roller. Klicka på de tre punkterna till höger på varje rad för att öppna den detaljerade beskrivningen av rollen.

![lista över roller i Azure AD-portalen](./media/directory-manage-roles-portal/role-list.png)

Markera hela raden att visa listan över tilldelade medlemmar. Du kan välja **hantera i PIM** för ytterligare hanteringsfunktioner. Privilegierade Rolladministratörer kan ändra ”Permanent” (alltid aktiv i rollen) tilldelningar till ”berättigade” (i rollen bara om utökad). Om du inte har PIM kan du fortfarande välja **hantera i PIM** att registrera dig för en utvärderingsversion. Privileged Identity Management kräver en [Azure AD Premium P2-licensplan](../privileged-identity-management/subscription-requirements.md).

![lista över medlemmar i en administratörsroll](./media/directory-manage-roles-portal/member-list.png)

Om du är en Global administratör eller en privilegierad roll-administratör kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller Välj medlemmar för att gå till användarsidan att se deras tilldelade roller aktiv. 

## <a name="role-details-in-the-portal"></a>Rollinformation i portalen

När du visar en rollmedlemmar, Välj **beskrivning** att se den fullständiga listan med behörigheter som beviljats av rolltilldelningen. Sidan innehåller länkar till relevant dokumentation som hjälper dig hantera katalogroller.

![listan över behörigheter för en administratörsroll](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Nästa steg

* Passa på att dela med oss på den [Azure AD-forum för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och administratören rolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
* Standardinställningar för användarbehörigheter, finns en [jämförelse av standard-Gäst och medlemmen användarbehörigheter](../fundamentals/users-default-permissions.md).
