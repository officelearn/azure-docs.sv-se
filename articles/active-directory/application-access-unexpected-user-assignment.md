---
title: "Hur du tilldela användare till program | Microsoft Docs"
description: "Förstå hur användare tilldelas till ett program i din klient"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 916238ba402a2555bac620d7f08e02799d981ae0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-assign-users-to-applications"></a>Tilldela användare till program

Den här artikeln hjälper dig att förstå hur användare tilldelas till ett program i din klient.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur användare tilldelas till ett program i Azure AD?

För en användare komma åt ett program, måste de först tilldelas till den på något sätt. Tilldelning kan utföras av en administratör, en delegat företag eller ibland användaren sig själva. Nedan beskrivs hur användare kan tilldelas till program:

1.  En administratör [tilldelas en användare](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) till programmet direkt

2.  En administratör [tilldelar en grupp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) att användaren är medlem i programmet, inklusive:

  * En grupp som synkroniserades från lokala

  * En statisk säkerhetsgrupp som skapas i molnet

  * En [dynamiska säkerhetsgrupp](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) skapas i molnet

  * En Office 365-grupp som skapats i molnet

  * Den [alla användare](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) grupp

3.  En administratör aktiverar [Self-service programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare att lägga till ett program som använder den [programmet åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till App** funktionen **utan business godkännande**

4.  En administratör aktiverar [Self-service programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare att lägga till ett program som använder den [programmet åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till App** funktionen, men endast w**: te tidigare godkännande från en vald uppsättning företag godkännare**

5.  En administratör aktiverar [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare att ansluta till en grupp som ett program har tilldelats **utan business godkännande**

6.  En administratör aktiverar [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) att tillåta användare att ansluta till en grupp som ett program är tilldelad till, men endast **med föregående godkännande från en vald uppsättning företag godkännare**

7.  En administratör tilldelar en licens till en användare direkt för ett första program tillverkare som [Microsoft Office 365](http://products.office.com/)

8.  En administratör tilldelar en licens till en grupp att användaren är medlem i ett första program tillverkare som [Microsoft Office 365](http://products.office.com/)

9.  En [administratör samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) som ska användas av alla användare och en användare loggar in på programmet

10. En användare [samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) sig genom att logga in till programmet

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](active-directory-enable-sso-scenario.md)
