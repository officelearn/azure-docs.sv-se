---
title: Granska åtkomst till grupper & appar i åtkomst granskningar – Azure AD
description: Lär dig hur du granskar din egen åtkomst till grupper eller program i Azure Active Directory åtkomst granskningar.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422407"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Granska åtkomst för dig själv till grupper eller program i åtkomst granskningar för Azure AD

Azure Active Directory (Azure AD) gör det enklare för företag att hantera åtkomst till grupper eller program i Azure AD och andra Microsoft Online Services med en funktion som kallas Azure AD-åtkomst granskningar.

Den här artikeln beskriver hur du granskar din egen åtkomst till en grupp eller ett program.

## <a name="open-the-access-review"></a>Öppna åtkomst granskningen

Det första steget för att utföra en åtkomst granskning är att hitta och öppna åtkomst granskningen.

1. Sök efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Här är ett exempel på ett e-postmeddelande för att granska din åtkomst till en grupp.

    ![Exempel på e-post från Microsoft för att granska din åtkomst till en grupp](./media/review-your-access/access-review-email.png)

1. Klicka på länken **Granska åtkomst** för att öppna åtkomst granskningen.

Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomst granskningar genom att följa dessa steg.

1. Logga in på webbplatsen för Mina appar på [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Appar för portalen som visar appar som du har behörighet att](./media/review-your-access/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på användarsymbolen, som visar ditt namn och standardorganisationen. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

1. Klicka på panelen **åtkomst granskningar** på höger sida av sidan för att se en lista över väntande åtkomst granskningar.

    Om panelen inte är synlig finns det inga åtkomstgranskningar att utföra för den organisationen, och ingen åtgärd krävs just nu.

    ![Lista över väntande åtkomst granskningar för dina appar och grupper](./media/review-your-access/access-reviews-list.png)

1. Klicka på länken **Starta granskning** för den åtkomst granskning som du vill utföra.

## <a name="perform-the-access-review"></a>Utför åtkomst granskningen

När du har öppnat åtkomst granskningen kan du se din åtkomst.

1. Granska din åtkomst och bestäm om du fortfarande behöver åtkomst.

    Om begäran är att granska åtkomsten för andra, ser sidan annorlunda ut. Mer information finns i [Granska åtkomst till grupper eller program](perform-access-review.md).

    ![Öppna åtkomst granskning och fråga om du fortfarande behöver åtkomst till en grupp](./media/review-your-access/perform-access-review.png)

1. Klicka på **Ja** om du vill behålla åtkomsten eller på **Nej** om du vill ta bort åtkomsten.

1. Om du klickar på **Ja**kan du behöva ange en motivering i rutan **orsak** .

    ![Slutförd åtkomst granskning som frågar om du fortfarande behöver åtkomst till en grupp](./media/review-your-access/perform-access-review-submit.png)

1. Klicka på **Skicka**.

    Ditt val skickas och du returnerade till portalen för Mina appar.

    Om du vill ändra ditt svar öppnar du sidan åtkomst granskningar på nytt och uppdaterar ditt svar. Du kan ändra ditt svar när du vill tills åtkomst granskningen har slutförts.

    > [!NOTE]
    > Om du har angett att du inte längre behöver åtkomst, tas du inte bort direkt. Du tas bort när granskningen har slutförts eller när en administratör stoppar granskningen.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
