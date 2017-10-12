---
title: "Hantera användare med Azure AD-åtkomstgranskningar| Microsoft Docs"
description: "Lär dig att hantera användarnas åtkomst som medlemskap i en grupp eller tilldelning till ett program med Azure AD-åtkomstgranskningar"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Hantera användare med Azure AD-åtkomstgranskningar

Med Azure Active Directory kan du enkelt se till att användarna har lämplig åtkomst genom att be användarna själva, eller en beslutsfattare, att delta i en åtkomstgranskning och certifiera om (eller "intyga") användarnas åtkomst.  Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst, baserat på förslag från Azure AD. När en åtkomstgranskning är klar kan du göra ändringar och ta bort åtkomst för användare som inte längre behöver den.

> [!NOTE]
> Om du enbart vill granska gästanvändares åtkomst och inte alla typer av användaråtkomster kan du läsa [managing guest user access with access reviews](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) (hantera gästanvändares åtkomst med åtkomstgranskningar).  Och om du vill granska användarens medlemskap i administrativa roller som global administratör läser du [How to start an access review in Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md) (Så här startar du en åtkomstgranskning i Azure AD PIM). 
>
>

## <a name="prerequisites"></a>Krav 

Åtkomstgranskningar är tillgängliga med Premium P2-versionen av Azure Active Directory, som ingår i EMS E5. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).  Varje användare som använder den här funktionen för att skapa en granskning, granska åtkomst eller tillämpa en granskning kräver en licens.


## <a name="creating-and-performing-an-access-review"></a>Skapa och utföra en åtkomstgranskning

Du kan ha en eller flera användare som granskare i en åtkomstgranskning.  

1. Välj en grupp i Azure Active Directory som har en eller flera medlemmar, eller ett program som är anslutet till Azure Active Directory som har en eller flera användare tilldelade till sig. 
2. Bestäm om varje användare ska granska sin egen åtkomst eller om en eller flera användare ska granska allas åtkomst.
3. Aktivera att åtkomstgranskningar ska visas på granskares åtkomstpaneler.  Som global administratör går du till [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Starta åtkomstgranskningen. Läs mer i [how to create an access review](active-directory-azure-ad-controls-create-access-review.md) (så här skapar du en åtkomstgranskning).
5. Be granskarna att komma med reflektioner. Som standard får var och en ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen, där de ska [genomföra sin åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md).
6. Om granskarna inte har gett några åsikter kan du be att Azure AD skickar en påminnelse.  Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.
7. När granskarna har gett sin information avslutar du åtkomstgranskningen och tillämpar ändringarna. Läs mer i [Så här slutför du en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](active-directory-azure-ad-controls-create-access-review.md)




