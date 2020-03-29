---
title: Så här visas program på åtkomstpanelen | Microsoft-dokument
description: Felsöka varför ett program visas på åtkomstpanelen
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784419"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Så här visas program på åtkomstpanelen

Åtkomstpanelen är en webbaserad portal som gör det möjligt för en användare med ett arbets- eller skolkonto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har gett dem åtkomst till. Dessa program konfigureras för användarens räkning i Azure AD-portalen. Administratören kan etablera programmet för användaren direkt eller till en grupp som en användare är en del av vilket resulterar i att programmet visas på användarens åtkomstpanel.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Om ett program har tagits bort från en användare eller grupp som användaren är medlem i försöker du logga in och ut igen på användarens åtkomstpanel efter några minuter för att se om programmet tas bort.

-   Om en licens har tagits bort från en användare eller grupp kan det ta lång tid att användaren är medlem i den här, beroende på gruppens storlek och komplexitet för ändringar. Vänta på extra tid innan du loggar in på åtkomstpanelen.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem med att tilldela program till användare

En användare kan se ett program på sin åtkomstpanel eftersom de tidigare hade tilldelats det. Här följer några sätt att kontrollera:

-   [Kontrollera om en användare har tilldelats programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Kontrollera om en användare har en licens som är relaterad till programmet](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrollera om en användare har tilldelats programmet

Så här kontrollerar du om en användare har tilldelats programmet:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

6. **Sök efter** namnet på programmet i fråga.

7. klicka på **Användare och grupper**.

8. Kontrollera om användaren har tilldelats programmet.

   * Om du vill ta bort användaren från programmet **klickar du på raden** för användaren och väljer ta **bort**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrollera om en användare har en licens som är relaterad till programmet

Så här kontrollerar du en användares tilldelade licenser:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7. Klicka på **Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

   * Om användaren har tilldelats en Office-licens gör detta att Office-program för första part kan visas på användarens åtkomstpanel.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem med att tilldela program till grupper

En användare kan se ett program på åtkomstpanelen eftersom de ingår i en grupp som har tilldelats programmet. Här följer några sätt att kontrollera:

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Kontrollera om en användare är medlem i en grupp som tilldelats en licens](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Så här kontrollerar du en grupps medlemskap:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7. klicka på **Grupper.**

8. Kontrollera om användaren ingår i en grupp som har tilldelats programmet.

   * Om du vill ta bort användaren från gruppen **klickar du på gruppens rad** och väljer ta bort.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Kontrollera om en användare är medlem i en grupp som tilldelats en licens

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7. klicka på **Grupper.**

8. klicka på raden för en viss grupp.

9. Klicka på **Licenser** för att se vilka licenser gruppen har tilldelat den.

   * Om gruppen har tilldelats en Office-licens kan det göra det möjligt för vissa Office-program från första part att visas på användarens åtkomstpanel.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om dessa felsökningssteg inte löser problemet

öppna en supportbiljett med följande information om sådan finns:

-   Korrelationsfel-ID

-   UPN (användarens e-postadress)

-   Klient-ID:t

-   Typ av webbläsare

-   Tidszon och tid/tidsram under fel inträffar

-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
