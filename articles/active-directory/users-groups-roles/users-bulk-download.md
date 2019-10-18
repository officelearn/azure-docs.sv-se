---
title: Hämta en lista med användare (för hands version) i Azure Active Directory Portal | Microsoft Docs
description: Hämta användar poster i bulk i Azure administrations centret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd0829afca05058892d3a0ceeb50c9955d792dc3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517070"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Hämta en lista med användare (förhands granskning) i Azure Active Directory Portal

Azure Active Directory (Azure AD) stöder åtgärder för Mass import av användare (skapa).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste vara inloggad med en användare som är tilldelad en eller flera administratörs roller på organisations nivå i Azure AD för att kunna hämta listan över användare från Azure AD Admin Center. Gäst deltagare och programutvecklare betraktas inte som administratörs roller.

## <a name="to-download-a-list-of-users"></a>Hämta en lista med användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett användar administratörs konto i organisationen.
1. I Azure AD väljer **du användare** > **Ladda ned användare**.
1. På sidan **Ladda ned användare** väljer du **Start** för att ta emot en CSV-fil som visar egenskaper för användar profiler. Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för Mass åtgärder. Filen innehåller orsaken för varje fel.

   ![Välj var du vill att listan över användare som du vill ladda ned ska hämtas](./media/users-bulk-download/bulk-download.png)

   Nedladdnings filen innehåller den filtrerade listan över användare.

   Följande användarattribut kommer att ingå: 

   - userPrincipalName
   - displayName
   - surname
   - e-post
   - GivenName
   - objectId
   - userType
   - Befattning
   - avdelning
   - Cachehanteraren
   - AccountEnabled
   - usageLocation
   - streetAddress
   - state
   - Ursprungslandet
   - physicalDeliveryOfficeName
   - city
   - Post nummer
   - TelephoneNumber
   - enheter
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för väntande Mass begär Anden på sidan för **Mass åtgärds resultat (förhands granskning)** .

   ![Kontrol lera uppladdnings status på sidan Mass åtgärds resultat](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att skapa en lista med användare kan köras i upp till en timme. På så sätt kan du skapa och ladda ned en lista med minst 500 000 användare.

## <a name="next-steps"></a>Nästa steg

- [Mass tillägg av användare](users-bulk-add.md)
- [Mass borttagnings användare](users-bulk-delete.md)
- [Mass återställnings användare](users-bulk-restore.md)
