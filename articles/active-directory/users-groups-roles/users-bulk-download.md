---
title: Hämta en lista med användare (för hands version) i Azure Active Directory Portal | Microsoft Docs
description: Hämta användar poster i bulk i Azure administrations centret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063840"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Hämta en lista med användare (förhands granskning) i Azure Active Directory Portal

Azure Active Directory (Azure AD) stöder åtgärder för Mass import av användare (skapa).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste vara inloggad med en användare som är tilldelad en eller flera administratörs roller på organisations nivå i Azure AD för att kunna hämta listan över användare från Azure AD Admin Center. Gäst deltagare och programutvecklare betraktas inte som administratörs roller.

## <a name="to-download-a-list-of-users"></a>Hämta en lista med användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett användar administratörs konto i organisationen.
2. Navigera till Azure Active Directory > användare. Välj sedan de användare som du vill inkludera i nedladdningen genom att kryssa i rutan i den vänstra kolumnen bredvid varje användare. Obs! det finns för tillfället inget sätt att markera alla användare för export. Var och en måste väljas individuellt.
3. I Azure AD väljer **du användare** > **Ladda ned användare**.
4. På sidan **Ladda ned användare** väljer du **Start** för att ta emot en CSV-fil som visar egenskaper för användar profiler. Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för Mass åtgärder. Filen innehåller orsaken för varje fel.

   ![Välj var du vill att listan över användare som du vill ladda ned ska hämtas](./media/users-bulk-download/bulk-download.png)

   Nedladdnings filen innehåller den filtrerade listan över användare.

   Följande användarattribut ingår:

   - userPrincipalName
   - displayName
   - surname
   - e-post
   - givenName
   - objekt-ID
   - userType
   - jobTitle
   - avdelning
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - ursprungslandet
   - physicalDeliveryOfficeName
   - city
   - Postnummer
   - telephoneNumber
   - mobila
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
