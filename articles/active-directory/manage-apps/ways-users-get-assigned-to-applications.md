---
title: Förstå hur användare tilldelas till appar i Azure Active Directory
description: Ta reda på hur användare tilldelas en app som använder Azure Active Directory för identitets hantering.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 243e5ece0d0a14cb7e3ade409ee68510cef64a9c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397108"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Förstå hur användare tilldelas till appar i Azure Active Directory
I den här artikeln får du hjälp att förstå hur användare tilldelas ett program i din klient organisation.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur kommer användarna att tilldelas ett program i Azure AD?
För att en användare ska kunna komma åt ett program måste de först tilldelas till det på något sätt. Tilldelningen kan utföras av en administratör, ett företags ombud eller ibland själva användaren. Nedan beskrivs hur användare kan tilldelas program:

*  En administratör [tilldelar en användare](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) till programmet direkt
*  En administratör [tilldelar en grupp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) som användaren är medlem i programmet, inklusive:
    * En grupp som har synkroniserats lokalt
    * En statisk säkerhets grupp som skapats i molnet
    * En [dynamisk säkerhets grupp](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) som skapats i molnet
    * En Office 365-grupp som skapats i molnet
    * Gruppen [alla användare](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  En administratör möjliggör [åtkomst till självbetjänings program](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare kan lägga till ett program med [Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till app** -funktion **utan affärs godkännande**
*  En administratör möjliggör [åtkomst till självbetjänings program](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare kan lägga till ett program med hjälp av [Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till app** -funktion, men endast w**i:te före godkännande från en vald uppsättning affärs god kännare**
*  En administratör möjliggör självbetjänings [grupp hantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare kan ansluta till en grupp som ett program har tilldelats till **utan affärs godkännande**
*  En administratör möjliggör självbetjänings [grupp hantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare kan ansluta till en grupp som ett program har tilldelats, men endast **med tidigare godkännande från en vald uppsättning affärs god kännare**
*  En administratör tilldelar en licens till en användare direkt för ett program från den första parten, t. ex. [Microsoft Office 365](https://products.office.com/)
*  En administratör tilldelar en licens till en grupp som användaren är medlem i till ett program i den första parten, t. ex. [Microsoft Office 365](https://products.office.com/)
*  En [administratör godkänner ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) som används av alla användare och sedan loggar användaren in på programmet
* En användare [samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) genom att logga in på programmet

## <a name="next-steps"></a>Nästa steg
* [Snabb starts serie för program hantering](view-applications-portal.md)
* [Vad är programhantering?](what-is-application-management.md)
* [Vad är enkel inloggning?](what-is-single-sign-on.md)
