---
title: Hur du tilldela användare till program | Microsoft Docs
description: Förstå hur användare tilldelas till ett program i din klient
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: be36bfeb0991dc4a4b7fce723cadb6b508c0d6a3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198729"
---
# <a name="how-to-assign-users-to-applications"></a>Tilldela användare till program

Den här artikeln hjälper dig att förstå hur användare tilldelas till ett program i din klient.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur användare tilldelas till ett program i Azure AD?

För en användare att komma åt ett program, måste de först tilldelas till den på något sätt. Tilldelning kan utföras av en administratör, ett företag ombud eller ibland användaren själva. Beskriver hur användare kan tilldelas till program nedan:

1.  En administratör [tilldelar en användare](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) till programmet direkt

2.  En administratör [tilldelar en grupp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) att användaren är medlem i program, inklusive:

    * En grupp som synkroniserades från en lokal plats

    * En statisk grupp skapas i molnet

    * En [dynamisk säkerhetsgruppen](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) skapas i molnet

    * En Office 365-grupp som skapas i molnet

    * Den [alla användare](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) grupp

3.  En administratör aktiverar [Självbetjäning för programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare att lägga till ett program med hjälp av den [Programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till App** funktionen **utan företag**

4.  En administratör aktiverar [Självbetjäning för programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) så att en användare att lägga till ett program med hjälp av den [Programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Lägg till App** funktionen, men endast w**användarprenumeration förhandsgodkännande från en vald uppsättning företagsgodkännaren**

5.  En administratör aktiverar [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare att ansluta till en grupp som ett program har tilldelats **utan företag**

6.  En administratör aktiverar [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) så att en användare att ansluta till en grupp som ett program är tilldelad till, men endast **med förhandsgodkännande från en vald uppsättning företagsgodkännaren**

7.  En administratör kopplar en licens till en användare direkt för en första partsprogram som [Microsoft Office 365](https://products.office.com/)

8.  En administratör kopplar en licens till en grupp att användaren är medlem i en första partsprogram som [Microsoft Office 365](https://products.office.com/)

9.  En [administratören godkänner ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) som ska användas av alla användare och sedan en användare loggar in till programmet

10. En användare [godkänner ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) själva genom att logga in till programmet

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
