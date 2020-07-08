---
title: Så här tilldelar du användare till program | Microsoft Docs
description: Ta reda på hur användare tilldelas ett program i din klient organisation
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45124862fffa3a1ef6f601733407fbbea4eb5e74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762980"
---
# <a name="how-to-assign-users-to-applications"></a>Tilldela användare till program

I den här artikeln får du hjälp att förstå hur användare tilldelas ett program i din klient organisation.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur kommer användarna att tilldelas ett program i Azure AD?

För att en användare ska kunna komma åt ett program måste de först tilldelas till det på något sätt. Tilldelningen kan utföras av en administratör, ett företags ombud eller ibland själva användaren. Nedan beskrivs hur användare kan tilldelas program:

1.  En administratör [tilldelar en användare](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) till programmet direkt

2.  En administratör [tilldelar en grupp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) som användaren är medlem i programmet, inklusive:

    * En grupp som har synkroniserats lokalt

    * En statisk säkerhets grupp som skapats i molnet

    * En [dynamisk säkerhets grupp](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) som skapats i molnet

    * En Office 365-grupp som skapats i molnet

    * Gruppen [alla användare](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  En administratör möjliggör [åtkomst till självbetjänings program](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare kan lägga till ett program med hjälp av [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till app** -funktion **utan affärs godkännande**

4.  En administratör möjliggör [åtkomst till självbetjänings program](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare kan lägga till ett program med hjälp av [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till app** -funktion, men endast w**i:te före godkännande från en vald uppsättning affärs god kännare**

5.  En administratör möjliggör självbetjänings [grupp hantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare kan ansluta till en grupp som ett program har tilldelats till **utan affärs godkännande**

6.  En administratör möjliggör självbetjänings [grupp hantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare kan ansluta till en grupp som ett program har tilldelats, men endast **med tidigare godkännande från en vald uppsättning affärs god kännare**

7.  En administratör tilldelar en licens till en användare direkt för ett program från den första parten, t. ex. [Microsoft Office 365](https://products.office.com/)

8.  En administratör tilldelar en licens till en grupp som användaren är medlem i till ett program i den första parten, t. ex. [Microsoft Office 365](https://products.office.com/)

9.  En [administratör godkänner ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) som används av alla användare och sedan loggar användaren in på programmet

10. En användare [samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) genom att logga in på programmet

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
