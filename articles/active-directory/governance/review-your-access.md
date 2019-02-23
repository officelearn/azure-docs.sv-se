---
title: Granska åtkomst själv till grupper eller program i Azure AD-Åtkomstgranskningar | Microsoft Docs
description: Lär dig hur du granskar din egen åtkomst till grupper eller program i Azure Active Directory-Åtkomstgranskningar.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f90a5aa3f201fa5d1578ac63526be26377aedf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731216"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Granska åtkomst själv till grupper eller program i Azure AD-Åtkomstgranskningar

Azure Active Directory (Azure AD) förenklar hur företag hantera åtkomst till grupper eller program i Azure AD och andra Microsoft Online Services med en funktion som kallas Azure AD-Åtkomstgranskningar.

Den här artikeln beskriver hur du granskar du din egen åtkomst till en grupp eller ett program.

## <a name="open-the-access-review"></a>Öppna åtkomstgranskningen

Det första steget för att utföra en åtkomstgranskning är att leta upp och öppna åtkomstgranskningen.

1. Leta efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Här är ett e-postmeddelande med exempel för att granska din åtkomst till en grupp.

    ![Granska åtkomst till e-post](./media/review-your-access/access-review-email.png)

1. Klicka på den **granska åtkomst** länk för att öppna åtkomstgranskningen.

Du kan hitta din väntande åtkomstgranskningar genom att följa dessa steg om du inte har e-postmeddelandet.

1. Logga in på MyApps-portalen på [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps-portalen](./media/review-your-access/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på symbolen användaren som visar organisationens namn och standard. Om mer än en organisation visas, väljer du den organisation som har begärt en åtkomstgranskning.

1. Till höger på sidan klickar du på den **Åtkomstgranskningar** ikonen för att visa en lista över väntande åtkomstgranskningar.

    Om panelen inte visas, det finns inga åtkomstgranskningar att utföra för organisationen och ingen åtgärd krävs just nu.

    ![Åtkomstgranskningar lista](./media/review-your-access/access-reviews-list.png)

1. Klicka på den **påbörja granskning** länk för åtkomstgranskning som du vill utföra.

## <a name="perform-the-access-review"></a>Utför åtkomstgranskningen

När du har öppnat åtkomstgranskningen, ser du din åtkomst.

1. Granska din åtkomst och avgöra om du fortfarande behöver åtkomst.

    Om begäran är att granska åtkomst för andra, ser sidan annorlunda. Mer information finns i [granska åtkomst till grupper eller program](perform-access-review.md).

    ![Utför åtkomstgranskning](./media/review-your-access/perform-access-review.png)

1. Klicka på **Ja** att hålla din åtkomst eller klicka på **nr** att ta bort din åtkomst.

1. Om du klickar på **Ja**, du kan behöva ange en motivering i den **orsak** box.

    ![Utför åtkomstgranskning](./media/review-your-access/perform-access-review-submit.png)

1. Klicka på **Skicka**.

    Ditt val har skickats och du tillbaka till MyApps-portalen.

    Om du vill ändra ditt svar öppnar sidan åtkomst granskningar och uppdaterar ditt svar. Du kan ändra ditt svar när som helst tills åtkomstgranskningen har avslutats.

    > [!NOTE]
    > Om du har angett att du inte längre behöver åtkomst du tas inte bort direkt. Du tas bort när granskningen har slutförts eller när en administratör slutar granskningen.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md)
