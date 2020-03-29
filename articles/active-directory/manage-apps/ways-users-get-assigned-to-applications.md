---
title: Så här tilldelar du användare till program | Microsoft-dokument
description: Förstå hur användare tilldelas ett program i din klientorganisation
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825979"
---
# <a name="how-to-assign-users-to-applications"></a>Så här tilldelar du användare till program

Den här artikeln hjälper dig att förstå hur användare tilldelas ett program i din klientorganisation.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur tilldelas användare till ett program i Azure AD?

För att en användare ska komma åt ett program måste de först tilldelas det på något sätt. Tilldelningen kan utföras av en administratör, ett affärsombud eller ibland användaren själva. Nedan beskrivs hur användare kan tilldelas program:

1.  En administratör [tilldelar en användare](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) direkt till programmet

2.  En administratör [tilldelar en grupp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) som användaren är medlem i i programmet, inklusive:

    * En grupp som synkroniserades från lokala

    * En statisk säkerhetsgrupp som skapats i molnet

    * En [dynamisk säkerhetsgrupp](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) som skapats i molnet

    * En Office 365-grupp som skapats i molnet

    * Gruppen [Alla användare](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  En administratör aktiverar [självbetjäningstillträde för att](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) tillåta en användare att lägga till ett program med funktionen **Lägg till app** för [programåtkomstpanel utan](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **affärsgodkännande**

4.  En administratör aktiverar [självbetjäningstillträde för att](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) tillåta en användare att lägga till ett program med funktionen **Lägg till app** för [programpanel,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) men endast**efter förhandsgodkännande från en vald uppsättning affärsgodkännanden**

5.  En administratör gör det möjligt för [självbetjäningsgrupphantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) att tillåta en användare att gå med i en grupp som ett program har tilldelats **utan affärsgodkännande**

6.  En administratör gör det möjligt för [självbetjäningsgrupphantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) att tillåta en användare att gå med i en grupp som ett program har tilldelats, men endast **med förhandsgodkännande från en vald uppsättning affärsgodkännanden**

7.  En administratör tilldelar en licens till en användare direkt för ett program från första part, till exempel [Microsoft Office 365](https://products.office.com/)

8.  En administratör tilldelar en licens till en grupp som användaren är medlem i i ett program från första part, till exempel [Microsoft Office 365](https://products.office.com/)

9.  En [administratör samtycker till att ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) används av alla användare och sedan loggar en användare in på programmet

10. En användare [samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) själva genom att logga in på programmet

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
