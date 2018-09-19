---
title: Hantera åtkomst till SaaS-program med hjälp av en grupp | Microsoft Docs
description: Hur du använder grupper i Azure Active Directory Premium eller Basic för att tilldela åtkomst till SaaS-program som är integrerade med Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/14/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 5d8e6c0db2ce6c35935c2b00aa40de2a5d5a050f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297630"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Använd en grupp för att hantera åtkomst till SaaS-program
Med Azure Active Directory (Azure AD) med Azure AD Premium eller Azure AD Basic-licens kan använda du grupper för att tilldela åtkomst till ett SaaS-program som är integrerad med Azure AD. Till exempel om du vill tilldela åtkomst för marknadsföringsavdelningen att använda fem olika SaaS-program du kan skapa en grupp som innehåller användare i marknadsföringsavdelningen och sedan tilldela den gruppen för dessa fem SaaS-program som krävs av den marknadsföringsavdelningen. På så sätt kan du spara tid genom att hantera medlemskapet för marknadsföringsavdelningen på samma ställe. Användare sedan tilldelas till programmet när de läggs till som medlemmar i gruppen marknadsföring och har tilldelningarna tas bort från programmet när de tas bort från gruppen marknadsföring. Den här funktionen kan användas med hundratals program som du kan lägga till från inom Azure AD-Programgalleriet.

> [!IMPORTANT]
> Du kan använda den här funktionen när du startar en Azure AD Premium-utvärderingsversion eller köpa Azure AD Premium eller Azure AD Basic-licenser. Gruppbaserad tilldelning stöds endast för säkerhetsgrupper. Kapslade gruppmedlemskap stöds inte för gruppbaserad tilldelning till program just nu.

**Att tilldela åtkomst för en användare eller grupp till ett SaaS-program**

1. I den [Azure AD administratörscenter](https://aad.portal.azure.com)väljer **företagsprogram**.
2. Välj ett program som du lagt till från den galleriet för att öppna den.
3. Välj **användare och grupper**, och välj sedan **Lägg till användare**.
4. På **Lägg till tilldelning**väljer **användare och grupper** att öppna den **användare och grupper** urvalslistan.
6. Välj så många grupper eller användare som du vill ha och klicka eller tryck **Välj** lägga till dem i den **Lägg till tilldelning** lista. Du kan också tilldela en roll till en användare i det här skedet.
7. Välj **tilldela** att tilldela användare eller grupper till de valda företagsprogram.

### <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
