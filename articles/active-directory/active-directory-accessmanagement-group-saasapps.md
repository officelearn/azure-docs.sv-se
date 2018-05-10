---
title: Hantera åtkomst till SaaS-program med hjälp av en grupp | Microsoft Docs
description: Hur du använder grupper i Azure Active Directory Premium eller Basic du tilldelar åtkomst till SaaS-program som är integrerade med Azure Active Directory.
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
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: bdcda54d4e43c81bde587b949cdc12a0237469a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Använd en grupp för att hantera åtkomst till SaaS-program
Använder Azure Active Directory (AD Azure) med en Azure AD Premium eller Azure AD Basic-licens kan använda du grupper för att tilldela åtkomst till ett SaaS-program som är integrerad med Azure AD. Till exempel om du vill tilldela åtkomst för marknadsföringsavdelningen att använda fem olika SaaS-program kan du skapa en grupp som innehåller användarna i marknadsföringsavdelningen och sedan tilldela den gruppen för dessa fem SaaS-program som krävs av marknadsföringsavdelningen. Det här sättet kan du spara tid genom att hantera medlemskap i marknadsföringsavdelningen på ett ställe. Användare sedan tilldelas till programmet när de läggs till som medlemmar i gruppen marknadsföring och deras tilldelningar ta bort från programmet när de tas bort från gruppen marknadsföring. Den här funktionen kan användas med hundratals program som du kan lägga till i Azure AD Application Gallery.

> [!IMPORTANT]
> Du kan använda den här funktionen när du startar en utvärderingsversion av Azure AD Premium eller köpa licenser för Azure AD Premium eller Azure AD Basic. Gruppbaserad tilldelning stöds endast för säkerhetsgrupper. Kapslade gruppmedlemskap stöds inte för gruppbaserad tilldelning till program just nu.

**Tilldela åtkomst för en användare eller grupp till ett SaaS-program**

1. I den [administrationscentret för Azure AD](https://aad.portal.azure.com)väljer **företagsprogram**.
2. Välj ett program som du lagt till från galleriet programmet att öppna den.
3. Välj **användare och grupper**, och välj sedan **Lägg till användare**.
4. På **Lägg uppdrag**väljer **användare och grupper** att öppna den **användare och grupper** urvalslistan.
6. Välj så många grupper eller användare som du vill, klicka eller tryck på **Välj** lägga till dem i den **Lägg uppdrag** lista. Du kan också tilldela en roll till en användare i det här skedet.
7. Välj **tilldela** tilldela användare eller grupper till de valda företagsprogram.

### <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Vad är Azure Active Directory?](active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
