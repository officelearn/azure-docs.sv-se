---
title: Använda en grupp för att hantera åtkomst till SaaS-appar – Azure AD | Microsoft-dokument
description: Så här använder du grupper i Azure Active Directory för att tilldela åtkomst till SaaS-program som är integrerade med Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026844"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Använd en grupp för att hantera åtkomst till SaaS-program

Med Azure Active Directory (Azure AD) med en Azure AD Premium-licensplan kan du använda grupper för att tilldela åtkomst till ett SaaS-program som är integrerat med Azure AD. Om du till exempel vill tilldela åtkomst för marknadsavdelningen för att använda fem olika SaaS-program kan du skapa en grupp som innehåller användarna på marknadsavdelningen och sedan tilldela den gruppen till dessa fem SaaS-program som behövs av marknadsavdelningen. På så sätt kan du spara tid genom att hantera medlemskap i marknadsavdelningen på ett ställe. Användare tilldelas sedan till programmet när de läggs till som medlemmar i marknadsföringsgruppen och har sina tilldelningar borttagna från programmet när de tas bort från marknadsföringsgruppen. Den här funktionen kan användas med hundratals program som du kan lägga till från Azure AD Application Gallery.

> [!IMPORTANT]
> Du kan bara använda den här funktionen när du har påbörjat en Utvärderingsversion av Azure AD Premium eller köpt Azure AD Premium-licensplan.
> Gruppbaserad tilldelning stöds endast för säkerhetsgrupper.
> Kapslade gruppmedlemskap stöds inte för gruppbaserad tilldelning till program just nu.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Så här tilldelar du åtkomst för en användare eller grupp till ett SaaS-program

1. Välj **Enterprise-program**i [Azure AD-administrationscentret](https://aad.portal.azure.com).
2. Välj ett program som du har lagt till i programgalleriet för att öppna det.
3. Välj **Användare och grupper**och välj sedan Lägg till **användare**.
4. I **Lägg till tilldelning**väljer du Användare och **grupper** för att öppna **urvalslistan Användare och grupper.**
6. Markera så många grupper eller användare du vill och klicka eller tryck sedan på **Markera** för att lägga till dem i listan **Lägg till tilldelning.** Du kan också tilldela en roll till en användare i det här skedet.
7. Välj **Tilldela** om du vill tilldela användare eller grupper till det valda företagsprogrammet.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
