---
title: Hur program visas på åtkomst panelen | Microsoft Docs
description: Felsöka varför ett program visas på åtkomst panelen
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22ba0709f4c5ca2294f515bdf1a96bff661b7293
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760838"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Hur program visas på åtkomst panelen

Åtkomst panelen är en webbaserad portal som gör det möjligt för en användare med ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Administratören kan etablera programmet till användaren direkt eller till en grupp som en användare är en del av i programmet som visas på användarens åtkomst panel.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först

-   Om ett program har tagits bort från en användare eller grupp som användaren är medlem i, försöker du logga in och ut igen i användarens åtkomst panel efter några minuter för att se om programmet har tagits bort.

-   Om en licens har tagits bort från en användare eller grupp kan användaren ta lång tid, beroende på storleken och komplexiteten i gruppen för att ändringar ska göras. Tillåt extra tid innan du loggar in på åtkomst panelen.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem som rör tilldelning av program till användare

En användare kan se ett program på sin åtkomst panel eftersom de tidigare hade tilldelats dem. Följande är några sätt att kontrol lera:

-   [Kontrol lera om en användare är tilldelad till programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Kontrol lera om en användare har en licens som är relaterad till programmet](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrol lera om en användare är tilldelad till programmet

Följ dessa steg om du vill kontrol lera om en användare är tilldelad till programmet:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

6. **Sök** efter namnet på programmet i fråga.

7. Klicka på **användare och grupper**.

8. Kontrol lera om användaren är tilldelad till programmet.

   * Om du vill ta bort användaren från programmet **klickar du på raden** för användaren och väljer **ta bort**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrol lera om en användare har en licens som är relaterad till programmet

Följ dessa steg om du vill kontrol lera en användares tilldelade licenser:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

   * Om användaren är tilldelad till en Office-licens så gör detta att första partens Office-program visas på användarens åtkomst panel.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem som rör tilldelning av program till grupper

En användare kan se ett program på sin åtkomst panel eftersom de ingår i en grupp som har tilldelats programmet. Följande är några sätt att kontrol lera:

-   [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)

-   [Kontrol lera om en användare är medlem i en grupp som har tilldelats en licens](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Kontrol lera en användares grupp medlemskap

Följ dessa steg om du vill kontrol lera en grupps medlemskap:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **grupper.**

8. Kontrol lera om användaren är en del av en grupp som tilldelats programmet.

   * Om du vill ta bort användaren från gruppen **klickar du på raden** i gruppen och väljer Ta bort.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Kontrol lera om en användare är medlem i en grupp som har tilldelats en licens

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **grupper.**

8. Klicka på raden för en speciell grupp.

9. Klicka på **licenser** för att se vilka licenser gruppen har tilldelats till den.

   * Om gruppen är tilldelad till en Office-licens kan detta medföra att vissa Office-program från första part visas på användarens åtkomst panel.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här fel söknings stegen inte löser problemet

öppna ett support ärende med följande information om den är tillgänglig:

-   Korrelations fel-ID

-   UPN (användarens e-postadress)

-   Klientorganisations-ID

-   Typ av webbläsare

-   Tidszon och tid/tidsram vid fel inträffar

-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
