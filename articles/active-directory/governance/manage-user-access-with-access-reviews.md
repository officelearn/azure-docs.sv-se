---
title: Hantera användare med Azure AD-Åtkomstgranskningar | Microsoft Docs
description: Lär dig att hantera användarnas åtkomst som medlemskap i en grupp eller tilldelning till ett program med Azure Active Directory-Åtkomstgranskningar
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 656a1d387a345d9ffcc549c19a131de758e18c35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879998"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Hantera användare med Azure AD-Åtkomstgranskningar

Med Azure Active Directory (Azure AD) kan du enkelt se till att användarna har lämplig åtkomst. Du kan be användarna själva, eller en beslutsfattare, att delta i en åtkomstgranskning och certifiera om (eller intyga) användarnas åtkomst. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst baserat på förslag från Azure AD. När en åtkomstgranskning är klar kan du göra ändringar och ta bort åtkomst för användare som inte längre behöver den.

> [!NOTE]
> Om du enbart vill granska gästanvändares åtkomst och inte alla typer av användaråtkomster kan du läsa [Manage guest user access with access reviews](manage-guest-access-with-access-reviews.md) (Hantera gästanvändares åtkomst med åtkomstgranskningar). Om du vill granska användarens medlemskap i administrativa roller, som global administratör, läser du [Start an access review in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md) (Starta en åtkomstgranskning i Azure AD Privileged Identity Management). 
>
>

## <a name="prerequisites"></a>Förutsättningar 


Åtkomstgranskningar är tillgängliga med Premium P2-versionen av Azure AD, som ingår i Microsoft Enterprise Mobility + Security, E5. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md). Varje användare som använder den här funktionen, inklusive för att skapa en granskning, fylla i en granskning eller bekräfta sin åtkomst, behöver en licens. 

## <a name="create-and-perform-an-access-review"></a>Skapa och utför du en åtkomstgranskning

Du kan ha en eller flera användare som granskare i en åtkomstgranskning.  

1. Välj en grupp i Azure AD som har en eller flera medlemmar. Alternativt kan du välja ett program har anslutit till Azure AD som har en eller flera användare tilldelade till sig. 

2. Bestäm om varje användare ska granska sin egen åtkomst eller om en eller flera användare ska granska allas åtkomst.

3. Som en global administratör eller Användaradministratör går du till den [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Skapa åtkomstgranskningen. Mer information finns i [skapa en åtkomstgranskning av grupper eller program som](create-access-review.md).

5. När åtkomstgranskningen startar be granskarna till reflektioner. Som standard får alla ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen, där de [granska åtkomst till grupper eller program](perform-access-review.md).

6. Om granskarna inte har framfört några åsikter kan du be att Azure AD skickar en påminnelse. Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.

7. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning av grupper eller program som](complete-access-review.md).


## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)




