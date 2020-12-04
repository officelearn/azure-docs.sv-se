---
title: Hämta en lista med användare i Azure Active Directory Portal | Microsoft Docs
description: Hämta användar poster i bulk i Azure administrations centret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042139a39c28ee4944a7f3f766fc61b163629843
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574402"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Hämta en lista med användare i Azure Active Directory Portal

Azure Active Directory (Azure AD) stöder åtgärder för Mass import av användare (skapa).

## <a name="required-permissions"></a>Behörigheter som krävs

Om du vill ladda ned listan med användare från Azure AD Admin Center måste du vara inloggad med en användare som tilldelats en eller flera administratörs roller på organisations nivå i Azure AD (användar administratör är den lägsta roll som krävs). Gäst deltagare och programutvecklare betraktas inte som administratörs roller.

## <a name="to-download-a-list-of-users"></a>Hämta en lista med användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett användar administratörs konto i organisationen.
2. Navigera till Azure Active Directory > användare. Välj sedan de användare som du vill inkludera i nedladdningen genom att kryssa i rutan i den vänstra kolumnen bredvid varje användare. Obs! det finns för tillfället inget sätt att markera alla användare för export. Var och en måste väljas individuellt.
3. I Azure AD väljer **du användare**  >  **Ladda ned användare**.
4. På sidan **Ladda ned användare** väljer du **Start** för att ta emot en CSV-fil som visar egenskaper för användar profiler. Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för Mass åtgärder. Filen innehåller orsaken för varje fel.

   ![Välj var du vill att listan över användare som du vill ladda ned ska hämtas](./media/users-bulk-download/bulk-download.png)

   Nedladdnings filen innehåller den filtrerade listan över användare.

   Följande användarattribut ingår:

   - userPrincipalName
   - displayName
   - surname
   - e-post
   - förnamn
   - objectId
   - userType
   - Befattning
   - avdelning
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - land
   - physicalDeliveryOfficeName
   - city
   - Post nummer
   - telephoneNumber
   - mobil
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för väntande Mass begär Anden på sidan med **Mass åtgärds resultat** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att skapa en lista med användare kan köras i upp till en timme. På så sätt kan du skapa och ladda ned en lista med minst 500 000 användare.

## <a name="next-steps"></a>Nästa steg

- [Masstillägg av användare](users-bulk-add.md)
- [Massborttag användare](users-bulk-delete.md)
- [Massåterställ användare](users-bulk-restore.md)
