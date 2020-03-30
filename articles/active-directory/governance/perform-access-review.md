---
title: Granska åtkomsten till grupper & program i åtkomstgranskningar - Azure AD
description: Lär dig hur du granskar åtkomst för gruppmedlemmar eller programåtkomst i Azure Active Directory-åtkomstgranskningar.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128458"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Granska åtkomsten till grupper och program i Azure AD-åtkomstgranskningar

Azure Active Directory (Azure AD) förenklar hur företag hanterar åtkomst till grupper och program i Azure AD och andra Microsoft Online-tjänster med en funktion som kallas Azure AD-åtkomstgranskningar.

I den här artikeln beskrivs hur en utsedd granskare utför en åtkomstgranskning för medlemmar i en grupp eller användare med åtkomst till ett program.

## <a name="open-the-access-review"></a>Öppna åtkomstgranskningen

Det första steget för att utföra en åtkomstgranskning är att hitta och öppna åtkomstgranskningen.

1. Leta efter ett e-postmeddelande från Microsoft som ber dig granska åtkomsten. Här är ett exempel på e-post för att granska åtkomsten för en grupp.

    ![Exempel på e-post från Microsoft för att granska åtkomsten till en grupp](./media/perform-access-review/access-review-email.png)

1. Klicka på länken **Starta granskning** för att öppna åtkomstgranskningen.

Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomstgranskningar genom att följa dessa steg.

1. Logga in på MyApps-portalen på [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![MyApps-portalen med appar som du har behörighet att](./media/perform-access-review/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på användarsymbolen, som visar ditt namn och standardorganisationen. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

1. Klicka på panelen **Access-granskningar** om du vill se en lista över de pågående åtkomstgranskningarna.

    Om panelen inte är synlig finns det inga åtkomstgranskningar att utföra för den organisationen, och ingen åtgärd krävs just nu.

    ![Lista över väntande åtkomstgranskningar för appar och grupper](./media/perform-access-review/access-reviews-list.png)

1. Klicka på länken **Börja granskning** för den åtkomstgranskning som du vill utföra.

## <a name="perform-the-access-review"></a>Utför åtkomstgranskningen

När du har öppnat åtkomstgranskningen visas namnen på användare som behöver granskas.

Om begäran är att granska din egen åtkomst, kommer sidan att se annorlunda ut. Mer information finns i [Granska åtkomst för dig själv till grupper eller program](review-your-access.md).

![Granskning av öppen åtkomst med en lista över de användare som behöver granskas](./media/perform-access-review/perform-access-review.png)

Det finns två sätt att godkänna eller neka åtkomst:

- Du kan godkänna eller neka åtkomst för en eller flera användare, eller
- Du kan acceptera systemrekommendationerna, som är det enklaste och snabbaste sättet.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Godkänna eller neka åtkomst för en eller flera användare

1. Granska listan över användare för att avgöra om de ska godkänna eller neka fortsatt åtkomst.

1. Om du vill godkänna eller neka åtkomst för en enskild användare klickar du på raden för att öppna ett fönster för att ange vilken åtgärd som ska vidtas. Om du vill godkänna eller neka åtkomst för flera användare lägger du till bockar bredvid användarna och klickar sedan på knappen **Granska X-användare** för att öppna ett fönster för att ange vilken åtgärd som ska vidtas.

1. Klicka på **Godkänn** eller **Neka**. Om du är osäker kan du klicka på **Vet ej**. Om du gör det får användaren att behålla sin åtkomst, men valet återspeglas i granskningsloggarna.

    ![Åtgärdsfönster som innehåller alternativ för Godkänna, Neka och Vet inte](./media/perform-access-review/approve-deny.png)

1. Om det behövs anger du en orsak i rutan **Orsak.**

    Administratören för åtkomstgranskningen kan kräva att du anger en orsak till att du godkänner fortsatt åtkomst eller gruppmedlemskap.

1. När du har angett åtgärden som ska vidtas klickar du på **Spara**.

    Om du vill ändra ditt svar markerar du raden och uppdaterar svaret. Du kan till exempel godkänna en tidigare nekad användare eller neka en tidigare godkänd användare. Du kan ändra ditt svar när som helst tills åtkomstgranskningen har avslutats.

    Om det finns flera granskare registreras det senast inlämnade svaret. Tänk dig ett exempel där en administratör utser två granskare – Alice och Bob. Alice öppnar åtkomstgranskningen först och godkänner åtkomst. Innan granskningen avslutas öppnar Bob åtkomstgranskningen och nekar åtkomst. Det sista neka svaret är vad som registreras.

    > [!NOTE]
    > Om en användare nekas åtkomst tas de inte bort omedelbart. De tas bort när granskningen har avslutats eller när en administratör stoppar granskningen.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Godkänna eller neka åtkomst baserat på rekommendationer

För att göra åtkomstgranskningar enklare och snabbare för dig ger vi också rekommendationer som du kan acceptera med ett enda klick. Rekommendationerna genereras baserat på användarens inloggningsaktivitet.

1. Klicka på **Acceptera rekommendationer**i det blå fältet längst ned på sidan .

    ![Granskning av öppen åtkomst med knappen Acceptera rekommendationer](./media/perform-access-review/accept-recommendations.png)

    Du ser en sammanfattning av de rekommenderade åtgärderna.

    ![Fönster som visar en sammanfattning av de rekommenderade åtgärderna](./media/perform-access-review/accept-recommendations-summary.png)

1. Klicka på **Ok** om du vill godkänna rekommendationerna.

## <a name="next-steps"></a>Nästa steg

- [Slutföra en åtkomstgranskning av grupper eller program](complete-access-review.md)
