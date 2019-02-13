---
title: Hur program visas på åtkomstpanelen | Microsoft Docs
description: Felsöka anledningen till att ett program visas i åtkomstpanelen
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a488c30f5b9438a91d2eae429cf65689af992dcb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167090"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Hur program visas på åtkomstpanelen

Åtkomstpanelen är en webbaserad portal, vilket gör att en användare med ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program att Azure AD-administratör har gett dem åtkomst till. Dessa program är konfigurerade för användarens räkning på Azure AD-portalen. Administratören kan etablera programmet till användaren direkt eller till en grupp en användare är en del av vilket resulterar i programmet visas på användarens åtkomstpanelen.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Om ett program har tagits bort från en användare eller grupp som användaren är medlem i, försök att logga in och ut igen i åtkomstpanelen för användarens efter ett par minuter att se om programmet tas bort.

-   Om en licens har tagits bort från en användare eller grupp användare som är medlem i det här kan ta lång tid, beroende på storleken och komplexiteten i gruppen för ändringar görs. Tillåt extra tid innan du loggar in på åtkomstpanelen.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem som rör tilldela program till användare

En användare kan se ett program på sina Åtkomstpaneler eftersom de tidigare hade tilldelats till den. Följande är några sätt att kontrollera:

-   [Kontrollera om en användare är kopplad till programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Kontrollera om en användare är under en licens som rör programmet](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrollera om en användare är kopplad till programmet

Följ dessa steg om du vill kontrollera om en användare har tilldelats till programmet:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

6.  **Sök** för namnet på programmet i fråga.

7.  Klicka på **användare och grupper**.

8.  Kontrollera om dina användare tilldelas till programmet.

  * Om du vill ta bort användaren från programmet, **klickar du på raden** av användare och välj **ta bort**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrollera om en användare är under en licens som rör programmet

Följ dessa steg om du vill kontrollera en användares tilldelade licenser:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser du för närvarande har tilldelats.

   * Om användaren har tilldelats en licens för Office, kan detta första part Office-program ska visas på användarens åtkomstpanelen.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem som rör tilldela program till grupper

En användare kan se ett program på sina Åtkomstpaneler eftersom de är en del av en grupp som har tilldelats programmet. Följande är några sätt att kontrollera:

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Kontrollera om en användare är medlem i en grupp som har tilldelats en licens](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Följ dessa steg om du vill kontrollera en grupps medlemskap:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper.**

8.  Kontrollera om dina användare är medlem i en grupp som tilldelats programmet.

   * Om du vill ta bort användaren från gruppen **klickar du på raden** av gruppen och välj Ta bort.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Kontrollera om en användare är medlem i en grupp som har tilldelats en licens

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper.**

8.  Klicka på raden i en specifik grupp.

9.  Klicka på **licenser** att se vilka licenser gruppen har tilldelats.

  * Om gruppen har tilldelats en licens för Office, kan detta vissa första part Office-program ska visas på användarens åtkomstpanelen.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här felsökningsstegen gör inte lösa problemet

Öppna ett supportärende med följande information om de är tillgängliga:

-   Korrelations-ID för fel

-   UPN (användarens e-postadress)

-   Klient-ID:t

-   Typ av webbläsare

-   Tidszon och tid/tidsramen under fel inträffar

-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
