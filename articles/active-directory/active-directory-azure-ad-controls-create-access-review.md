---
title: "Skapa en åtkomst-granskning för medlemmar i en grupp eller användare med åtkomst till ett program med Azure AD | Microsoft Docs"
description: "Lär dig hur du skapar en åtkomst-granskning för medlemmar i en grupp eller användare med åtkomst till ett program."
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: ae926e8d3a856c9b488d46b481a1c7b5b581d532
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Skapa en åtkomst-granskning av medlemmar i gruppen eller programåtkomst med Azure AD

Access-tilldelningar blir ”inaktiva” när användare har åtkomst till de inte behövs längre. Administratörer kan använda Azure Active Directory (Azure AD) att skapa en åtkomst-granskning för medlemmar i grupper eller användare som är tilldelade till ett program för att minska riskerna i samband med inaktuella åtkomst tilldelningar. Mer information om dessa scenarier finns [hantera användarnas åtkomst](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) och [hantera gästbehörighet](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Skapa en åtkomst-granskning

1. Som en global administratör, gå till den [åtkomst går igenom sidan](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), och välj **program**.

2. Välj det program som innehåller granska åtkomstkontroll du vill skapa. **Standardprogrammet** är alltid finns, eller så kan du skapa ett annat program. Exempelvis kan du ha ett program för varje kompatibilitet eller företagsmål.

3. Programmet och välj **kontroller**, och välj sedan **Lägg till** lägga till en kontroll.

4. Namnge varje granska åtkomst. Du kan också ge varje granska en beskrivning. Namnet visas till granskare.

5. Ange start- och slutdatum. Som standard granska åtkomsten startar samma dag skapas och slutar i en månad. Du kan ändra start och slutdatum komma granska start i framtiden och senast många dagar som du vill.

6. Åtkomst granskningar kan vara medlemmar i en grupp eller användare som har tilldelats ett program. Du kan ytterligare omfång åtkomsten för endast granskning granskning gästanvändare som är medlemmar (eller tilldelade till appen), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

7. Välj en eller flera personer att granska alla användare i omfånget. Eller så kan du välja för att ha medlemmar granska sina egna åtkomst. Om resursen är en grupp, kan du be gruppen ägare att granska. Du kan också kräva att granskare ange en anledning när de godkänner åtkomst.

8. Välj slutligen **starta**.


## <a name="manage-the-access-review"></a>Hantera åtkomst-granskning

Standard skickar Azure AD ett e-postmeddelande till granskare kort efter granskningen startar. Om du inte väljer att låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst-granskning väntar de är klara. Du kan visa dem instruktioner för hur du [granska åtkomst](active-directory-azure-ad-controls-perform-access-review.md). Om din granskning är för gäster att granska sina egna åtkomst kan visa dem instruktioner för hur du [granska din egen åtkomst](active-directory-azure-ad-controls-perform-access-review.md).

Om några av granskarna gäster, meddelas gäster via e-post endast om de har redan accepterats deras inbjudan.


Du kan följa förloppet när granskare Slutför granskningarna i instrumentpanelen i Azure AD den **åtkomst granskar** avsnitt. Ingen behörighet har ändrats i katalogen tills [granskningen har slutförts](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Nästa steg

När en åtkomst-granskning har börjat skickar Azure AD automatiskt granskare ett e-postmeddelande som uppmanar dem att granska åtkomst. Om en användare inte har fått ett e-postmeddelande, kan du skicka dem instruktioner för hur du [granska åtkomst](active-directory-azure-ad-controls-perform-access-review.md). 

När omprövningsperioden åtkomst är över eller administratören stoppar åtkomst granskning, följer du stegen i [slutföra en åtkomst-granskning](active-directory-azure-ad-controls-complete-access-review.md) finns och att tillämpa resultaten.


