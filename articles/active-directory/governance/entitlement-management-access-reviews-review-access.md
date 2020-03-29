---
title: Granska åtkomsten för ett åtkomstpaket i Azure AD-berättigandehantering
description: Lär dig hur du slutför en åtkomstgranskning av åtkomstpaket för berättigandehantering i Azure Active Directory-åtkomstgranskningar (förhandsversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968748"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Granska åtkomsten för ett åtkomstpaket i Azure AD-berättigandehantering

Azure AD-berättigandehantering förenklar hur företag hanterar åtkomst till grupper, program och SharePoint-webbplatser. I den här artikeln beskrivs hur du utför åtkomstgranskningar för andra användare som har tilldelats ett åtkomstpaket som en utsedd granskare.

## <a name="prerequisites"></a>Krav

Om du vill granska användarnas aktiva åtkomstpakettilldelningar måste du uppfylla förutsättningarna för att göra en åtkomstgranskning:
- Azure AD Premium P2
- Global administratör
- Utsedd användaradministratör, katalogägare eller åtkomstpakethanterare

Mer information finns i [Licenskrav](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Öppna åtkomstgranskningen

Så här hittar du och öppnar åtkomstgranskningen:

1. Du kan få ett e-postmeddelande från Microsoft som ber dig granska åtkomsten. Leta reda på e-postmeddelandet för att öppna åtkomstgranskningen. Här är ett exempel e-post för att granska åtkomst:
    
    ![E-post för åtkomstgranskningsgranskare](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Klicka på länken **Granska användaråtkomst** för att öppna åtkomstgranskningen. 

1. Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomstgranskningar genom att navigera direkt till https://myaccess.microsoft.com.  (För amerikanska regeringen, använd `https://myaccess.microsoft.us` istället.)

1. Klicka på **Access-granskningar** i det vänstra navigeringsfältet om du vill visa en lista över väntande åtkomstgranskningar som tilldelats dig.
    
    ![Välj åtkomstgranskningar på Min åtkomst](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klicka på den recension som du vill börja med.
    
    ![Välj åtkomstgranskning](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Utför åtkomstgranskningen

När du har öppnat åtkomstgranskningen visas namnen på de användare som du behöver granska. Det finns två sätt att godkänna eller neka åtkomst:
- Du kan manuellt godkänna eller neka åtkomst för en eller flera användare
- Du kan acceptera systemrekommendationerna

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Godkänna eller neka åtkomst manuellt för en eller flera användare
1. Granska listan över användare och bestäm vilka användare som måste fortsätta att ha åtkomst.

    ![Lista över användare att granska](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Om du vill godkänna eller neka åtkomst markerar du alternativknappen till vänster om användarens namn.

1. Välj **Godkänn** eller **Neka** i fältet ovanför användarnamnen.

    ![Välj användaren](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Om du är osäker kan du klicka på knappen **Vet inte.**

    Om du gör det här valet behåller användaren åtkomst och det här valet hamnar i granskningsloggarna. Loggen visar alla andra granskare som du fortfarande har slutfört granskningen.

1. Du kan behöva ange en anledning till ditt beslut. Skriv in en orsak och klicka på **Skicka**.

    ![Godkänna eller neka åtkomst](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Du kan ändra ditt beslut när som helst före granskningens. Om du vill göra det väljer du användaren i listan och ändrar beslutet. Du kan till exempel godkänna åtkomst för en användare som du tidigare nekat.

Om det finns flera granskare registreras det senast inlämnade svaret. Tänk dig ett exempel där en administratör utser två granskare – Alice och Bob. Alice öppnar granskningen först och godkänner åtkomst. Innan granskningen avslutas öppnar Bob granskningen och nekar åtkomst. I det här fallet registreras det sista beslutet om neka åtkomst.

>[!NOTE]
>Om en användare nekas åtkomst tas de inte bort från åtkomstpaketet omedelbart. Användaren tas bort från åtkomstpaketet när granskningen avslutas eller en administratör avslutar granskningen.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Godkänna eller neka åtkomst med hjälp av de systemgenererade rekommendationerna

Om du vill granska åtkomsten för flera användare snabbare kan du använda de systemgenererade rekommendationerna och acceptera rekommendationerna med ett enda klick. Rekommendationerna genereras baserat på användarens inloggningsaktivitet.

1.  Klicka på **Acceptera rekommendationer**i fältet högst upp på sidan .
    
    ![Välj Acceptera rekommendationer](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Du ser en sammanfattning av de rekommenderade åtgärderna.

1.  Klicka på **Skicka** om du vill godkänna rekommendationerna.

## <a name="next-steps"></a>Nästa steg

- [Självgranskning av åtkomstpaket](entitlement-management-access-reviews-self-review.md)