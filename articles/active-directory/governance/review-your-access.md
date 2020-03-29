---
title: Granska din åtkomst till grupper & appar i åtkomstgranskningar - Azure AD
description: Lär dig hur du granskar din egen åtkomst till grupper eller program i Azure Active Directory-åtkomstgranskningar.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422407"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Granska åtkomsten för dig själv till grupper eller program i Azure AD-åtkomstgranskningar

Azure Active Directory (Azure AD) förenklar hur företag hanterar åtkomst till grupper eller program i Azure AD och andra Microsoft Online-tjänster med en funktion som kallas Azure AD-åtkomstgranskningar.

I den här artikeln beskrivs hur du granskar din egen åtkomst till en grupp eller ett program.

## <a name="open-the-access-review"></a>Öppna åtkomstgranskningen

Det första steget för att utföra en åtkomstgranskning är att hitta och öppna åtkomstgranskningen.

1. Leta efter ett e-postmeddelande från Microsoft som ber dig granska åtkomsten. Här är ett exempel på e-post för att granska din åtkomst till en grupp.

    ![Exempel på e-post från Microsoft för att granska din åtkomst till en grupp](./media/review-your-access/access-review-email.png)

1. Klicka på länken **Granska åtkomst** för att öppna åtkomstgranskningen.

Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomstgranskningar genom att följa dessa steg.

1. Logga in på MyApps-portalen på [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![MyApps-portalen med appar som du har behörighet att](./media/review-your-access/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på användarsymbolen, som visar ditt namn och standardorganisationen. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

1. Klicka på panelen **Access-recensioner** till höger på sidan om du vill se en lista över de pågående åtkomstgranskningarna.

    Om panelen inte är synlig finns det inga åtkomstgranskningar att utföra för den organisationen, och ingen åtgärd krävs just nu.

    ![Lista över granskningar av åtkomst för dina appar och grupper](./media/review-your-access/access-reviews-list.png)

1. Klicka på länken **Börja granskning** för den åtkomstgranskning som du vill utföra.

## <a name="perform-the-access-review"></a>Utför åtkomstgranskningen

När du har öppnat åtkomstgranskningen kan du se din åtkomst.

1. Granska din åtkomst och bestäm om du fortfarande behöver åtkomst.

    Om begäran är att granska åtkomst för andra ser sidan annorlunda ut. Mer information finns i [Granska åtkomst till grupper eller program](perform-access-review.md).

    ![Granskning med öppen åtkomst och frågar om du fortfarande behöver åtkomst till en grupp](./media/review-your-access/perform-access-review.png)

1. Klicka på **Ja** om du vill behålla åtkomsten eller klicka på **Nej** om du vill ta bort åtkomsten.

1. Om du klickar på **Ja**kan du behöva ange en justering i rutan **Orsak.**

    ![Slutförd åtkomstgranskning och frågar om du fortfarande behöver åtkomst till en grupp](./media/review-your-access/perform-access-review-submit.png)

1. Klicka på **Skicka**.

    Ditt val skickas in och du återvände till MyApps-portalen.

    Om du vill ändra ditt svar öppnar du sidan för åtkomstgranskningar igen och uppdaterar ditt svar. Du kan ändra ditt svar när som helst tills åtkomstgranskningen har avslutats.

    > [!NOTE]
    > Om du angav att du inte längre behöver åtkomst tas du inte bort omedelbart. Du tas bort när granskningen har avslutats eller när en administratör stoppar granskningen.

## <a name="next-steps"></a>Nästa steg

- [Slutföra en åtkomstgranskning av grupper eller program](complete-access-review.md)
