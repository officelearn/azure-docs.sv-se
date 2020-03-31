---
title: Självgranskning av ett åtkomstpaket i Azure AD-berättigandehantering
description: Lär dig hur du granskar användaråtkomst för åtkomstpaket för berättigandehantering i Azure Active Directory-åtkomstgranskningar (förhandsversion).
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
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967760"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Självgranskning av ett åtkomstpaket i Azure AD-berättigandehantering

Azure AD-berättigandehantering förenklar hur företag hanterar åtkomst till grupper, program och SharePoint-webbplatser. I den här artikeln beskrivs hur en användare gör en självgranskning av sina tilldelade åtkomstpaket.

## <a name="open-the-access-review"></a>Öppna åtkomstgranskningen

Om du vill göra en åtkomstgranskning måste du först öppna åtkomstgranskningen. Använd följande procedur för att hitta och öppna åtkomstgranskningen:

1. Du kan få ett e-postmeddelande från Microsoft som ber dig granska åtkomsten. Leta reda på e-postmeddelandet för att öppna åtkomstgranskningen. Här är ett exempel på ett e-postmeddelande som begär en granskning av åtkomst: 
    
    ![E-post för åtkomstgranskning](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Klicka på länken **Granska åtkomst.**

1. Du kan också https://myaccess.microsoft.com gå direkt till för att hitta dina väntande åtkomstrecensioner om du inte får ett e-postmeddelande.  (För amerikanska regeringen, använd `https://myaccess.microsoft.us` istället.)

1. Klicka på **Access-granskningar** i det vänstra navigeringsfältet om du vill visa en lista över väntande åtkomstgranskningar som tilldelats dig.


1.  Klicka på den recension som du vill börja med.

## <a name="perform-the-access-review"></a>Utför åtkomstgranskningen

När du har öppnat åtkomstgranskningen kan du se din åtkomst. Använd följande procedur för att göra åtkomstgranskningen:

1.  Bestäm om du fortfarande behöver åtkomst till åtkomstpaketet. Projektet du arbetar med är till exempel inte slutfört, så du behöver fortfarande åtkomst för att fortsätta arbeta med projektet.

1.  Klicka på **Ja** om du vill behålla åtkomsten eller klicka på **Nej** om du vill ta bort åtkomsten.
    >[!NOTE]
    >Om du uppgav att du inte längre behöver åtkomst tas du inte bort från åtkomstpaketet omedelbart. Du tas bort från åtkomstpaketet när granskningen avslutas eller om en administratör stoppar granskningen.

1.  Om du klickade på **Ja**kan du behöva inkludera en motiveringssats i rutan **Orsak.**

1.  Klicka på **Skicka**.

Du kan återgå till granskningen om du ändrar dig och bestämmer dig för att ändra ditt svar före slutet av granskningen.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomsten till åtkomstpaket](entitlement-management-access-reviews-review-access.md) 
