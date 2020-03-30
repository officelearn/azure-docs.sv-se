---
title: Skapa en åtkomstgranskning av ett åtkomstpaket i Azure AD-berättigandehantering
description: Lär dig hur du skapar en princip för åtkomstgranskning av åtkomst för behörighetshantering för åtkomstpaket för berättigandehantering i Azure Active Directory-åtkomstgranskningar (förhandsversion).
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
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608844"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Skapa en åtkomstgranskning av ett åtkomstpaket i Azure AD-berättigandehantering

För att minska risken för inaktuell åtkomst bör du aktivera regelbundna granskningar av användare som har aktiva tilldelningar till ett åtkomstpaket i Azure AD-berättigandehantering. Du kan aktivera granskningar när du skapar ett nytt åtkomstpaket eller redigerar ett befintligt åtkomstpaket. I den här artikeln beskrivs hur du aktiverar åtkomstgranskningar av åtkomstpaket.

## <a name="prerequisites"></a>Krav

Om du vill aktivera granskningar av åtkomstpaket måste du uppfylla förutsättningarna för att skapa ett åtkomstpaket:
- Azure AD Premium P2
- Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

Mer information finns i [Licenskrav](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Skapa en åtkomstgranskning av ett åtkomstpaket

Du kan aktivera åtkomstgranskningar när [du skapar ett nytt åtkomstpaket](entitlement-management-access-package-create.md) eller [redigerar en befintlig princip för åtkomstpaket.](entitlement-management-access-package-lifecycle-policy.md) Följ dessa steg för att aktivera åtkomstgranskningar av ett åtkomstpaket:

1. Öppna fliken **Livscykel** för ett åtkomstpaket och bläddra ned till **Access Recensioner**.

1. Flytta växlingsknappen **Kräv åtkomstgranskning** till **Ja**.

    ![Lägg till åtkomstgranskningen](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Ange det datum då granskningarna ska börja **bredvid Från och med**.

1. Ange sedan **granskningsfrekvensen** till **Årligen**, **Varannan år**, **Kvartalsvis** eller **Månadsvis**.
Den här inställningen avgör hur ofta åtkomstgranskningar ska ske.

1. Ange **varaktigheten** för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för indata från granskare. Du kan till exempel schemalägga en årlig granskning som börjar den 1 januari och är öppen för granskning i 30 dagar så att granskarna har fram till slutet av månaden på sig att svara.

1. Bredvid **Granskare**väljer du **Självgranskning** om du vill att användarna ska utföra sin egen åtkomstgranskning eller välja **Specifika granskare** om du vill utse en granskare.

    ![Välj Lägg till granskare](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Om du har valt **Specifika granskare**anger du vilka användare som ska göra åtkomstgranskningen:
    1. Välj **Lägg till granskare**.
    1. Sök efter och välj de användare som du vill vara granskare i fönstret **Välj granskare.**
    1. När du har valt granskarna klickar du på knappen **Välj.**

    ![Ange granskarna](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Klicka på **Granska + Skapa** om du skapar ett nytt åtkomstpaket eller **Uppdatera** om du redigerar ett åtkomstpaket längst ned på sidan.

## <a name="view-the-status-of-the-access-review"></a>Visa status för åtkomstgranskningen

Efter startdatumet visas en åtkomstgranskning i avsnittet **Access-granskningar.** Så här visar du status för en åtkomstgranskning:

1. I **Identitetsstyrning**klickar du på **Access-paket** och väljer sedan det åtkomstpaket med den åtkomstgranskningsstatus som du vill kontrollera.   

1. När du har fått översikt över åtkomstpaketet klickar du på **Access-recensioner** på den vänstra menyn.
    
    ![Välj åtkomstgranskningar](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. En lista visas som innehåller alla principer som har åtkomstgranskningar som är associerade med dem. Klicka på granskningen om du vill se rapporten.

    ![Lista över åtkomstgranskningar](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. När du visar rapporten visas antalet granskade användare och de åtgärder som granskaren har vidtagit.

    ![Visa granskningsstatus](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Få tillgång till e-postmeddelanden om recensioner
Du kan ange granskare eller så kan användare granska sin åtkomst själva. Som standard skickar Azure AD ett e-postmeddelande till granskare eller självgranskare kort efter att granskningen har startat.

E-postmeddelandet innehåller instruktioner om hur du granskar åtkomst till åtkomstpaket. Om granskningen är till för användare att granska sin åtkomst, visa dem instruktionerna om hur man utför en självgranskning av sina åtkomstpaket.
  
Om du har tilldelat gästanvändare som granskare och de inte har accepterat sin Azure AD-gästanbjudan får de inte e-postmeddelanden från Azure AD-åtkomstgranskningar. De måste först acceptera inbjudan och skapa ett konto med Azure AD innan de kan ta emot e-postmeddelandena. 

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomstpaket för åtkomstpaket](entitlement-management-access-reviews-review-access.md)
- [Självgranskning av åtkomstpaket](entitlement-management-access-reviews-self-review.md)
