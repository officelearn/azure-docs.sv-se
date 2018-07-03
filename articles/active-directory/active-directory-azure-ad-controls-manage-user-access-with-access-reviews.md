---
title: Hantera användare med Azure AD-åtkomstgranskningar| Microsoft Docs
description: Lär dig att hantera användarnas åtkomst som medlemskap i en grupp eller tilldelning till ett program med Azure AD-åtkomstgranskningar
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.openlocfilehash: 86ca14c8865ee82d61cb63a20f66c508f532f124
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020010"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Hantera användare med Azure AD-åtkomstgranskningar

Med Azure Active Directory (Azure AD) kan du enkelt se till att användarna har lämplig åtkomst. Du kan be användarna själva, eller en beslutsfattare, att delta i en åtkomstgranskning och certifiera om (eller intyga) användarnas åtkomst. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst baserat på förslag från Azure AD. När en åtkomstgranskning är klar kan du göra ändringar och ta bort åtkomst för användare som inte längre behöver den.

> [!NOTE]
> Om du enbart vill granska gästanvändares åtkomst och inte alla typer av användaråtkomster kan du läsa [Manage guest user access with access reviews](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) (Hantera gästanvändares åtkomst med åtkomstgranskningar). Om du vill granska användarens medlemskap i administrativa roller, som global administratör, läser du [Start an access review in Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md) (Starta en åtkomstgranskning i Azure AD Privileged Identity Management). 
>
>

## <a name="prerequisites"></a>Nödvändiga komponenter 


Åtkomstgranskningar är tillgängliga med Premium P2-versionen av Azure AD, som ingår i Microsoft Enterprise Mobility + Security, E5. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md). Varje användare som använder den här funktionen, inklusive för att skapa en granskning, fylla i en granskning eller bekräfta sin åtkomst, behöver en licens. 

## <a name="create-and-perform-an-access-review"></a>Skapa och utför du en åtkomstgranskning

Du kan ha en eller flera användare som granskare i en åtkomstgranskning.  

1. Välj en grupp i Azure AD som har en eller flera medlemmar. Alternativt kan du välja ett program har anslutit till Azure AD som har en eller flera användare tilldelade till sig. 

2. Bestäm om varje användare ska granska sin egen åtkomst eller om en eller flera användare ska granska allas åtkomst.

3. Aktivera att åtkomstgranskningar ska visas på granskarnas åtkomstpaneler. Som global administratör eller användarkonto-administratör går du till [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Starta åtkomstgranskningen. Mer information finns i [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md).

5. Be granskarna att komma med reflektioner. Som standard får alla ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen där de ska [genomföra sin åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md).

6. Om granskarna inte har framfört några åsikter kan du be att Azure AD skickar en påminnelse. Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.

7. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](active-directory-azure-ad-controls-create-access-review.md)




