---
title: Hämta en lista över användare i Azure Active Directory-portalen | Microsoft-dokument
description: Hämta användarposter i grupp i Azure-administrationscentret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532667"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Hämta en lista över användare i Azure Active Directory-portalen

Azure Active Directory (Azure AD) stöder massimportåtgärder för användare.Azure Active Directory (Azure AD) supports bulk user import (create) operations.

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill hämta listan över användare från Azure AD-administrationscentret måste du vara inloggad med en användare som tilldelats en eller flera administratörsroller på organisationsnivå i Azure AD (Användaradministratör är den minsta roll som krävs). Gäst inbjudna och programutvecklare betraktas inte som administratörsroller.

## <a name="to-download-a-list-of-users"></a>Så här hämtar du en lista över användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett användarkonto för användaradministratörer i organisationen.
2. Navigera till Azure Active Directory >-användare. Välj sedan de användare som du vill inkludera i hämtningen genom att markera rutan i den vänstra kolumnen bredvid varje användare. Obs: För närvarande finns det inget sätt att välja alla användare för export. Var och en måste väljas individuellt.
3. I Azure AD väljer du **Användare** > **Hämta användare**.
4. På sidan **Hämta användare** väljer du **Start** för att ta emot en CSV-fil med användarprofilegenskaper. Om det finns fel kan du hämta och visa resultatfilen på sidan Massåtgärdsresultat. Filen innehåller orsaken till varje fel.

   ![Välj var du vill ha listan vilka användare du vill hämta](./media/users-bulk-download/bulk-download.png)

   Hämtningsfilen innehåller den filtrerade listan över användare.

   Följande användarattribut ingår:

   - userPrincipalName
   - displayName
   - surname
   - e-post
   - förnamn
   - Objectid
   - userType (användare)
   - jobbTitle
   - avdelning
   - accountEnabled
   - användningLokalisering
   - streetAddress (streetAddress)
   - state
   - land
   - fysisktDeliveryOfficeName
   - city
   - Postnummer
   - telefonAntal
   - mobil
   - autentiseringTelefonnummer
   - autentiseringAlternativePhoneNumber
   - autentiseringEpost
   - alternateEmailAddress
   - ageGroup
   - samtyckeTillhandaladeForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för dina väntande massbegäranden på sidan **Massåtgärdsresultat.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Begränsningar för masshämtningstjänsten

Varje massaktivitet för att skapa en lista över användare kan köras i upp till en timme. Detta gör det möjligt att skapa och hämta en lista över minst 500 000 användare.

## <a name="next-steps"></a>Nästa steg

- [Masstillägg av användare](users-bulk-add.md)
- [Massborttagning användare](users-bulk-delete.md)
- [Användare av massåterställning](users-bulk-restore.md)
