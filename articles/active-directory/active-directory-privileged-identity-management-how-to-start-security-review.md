---
title: "Så här startar du en åtkomst-granskning | Microsoft Docs"
description: "Lär dig hur du skapar en åtkomst-granskning för privilegierade identiteter med Azure Privileged Identity Management-programmet."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 3e52b731-55f4-4c8a-ba87-9fd34033f52f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2b516e2f05aa883c5e37f5864e5ee8a2b37d3a46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Hur du startar en åtkomst-granskning i Azure AD Privileged Identity Management
Rolltilldelningar blir ”inaktiva” när användare privilegierad åtkomst som de inte behöver längre. För att minska riskerna i samband med dessa inaktuella rolltilldelningar bör Privilegierade rollen administratörer regelbundet granska de roller som användarna har fått. Det här dokumentet beskriver steg för att starta en åtkomst-granskning i Azure AD Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Starta en åtkomst-granskning
> [!NOTE]
> Om du inte har lagt till programmet PIM på instrumentpanelen i Azure-portalen, hittar du i [komma igång med Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)
> 
> 

Från PIM programmet huvudsidan finns det tre sätt att starta en åtkomst-granskning:

* **Åtkomst till granskningar** > **Lägg till**
* **Roller** > **granska** knappen
* Välj den specifika rollen som ska granskas från listan över roller > **granska** knappen

Om du klickar på den **granska** knappen, den **startar en åtkomst-granskning** bladet visas. På det här bladet ska du konfigurera granskning med ett namn och en tid, Välj en roll för att granska och bestämma vem som utför granskningen.

![Starta en åtkomst-granskning – skärmbild][1]

### <a name="configure-the-review"></a>Konfigurera granskning
Om du vill skapa en åtkomst-granskning, måste du ge den namnet och ange start-och.

![Konfigurera granskning – skärmbild][2]

Kontrollera längden på Granska tillräckligt länge för användare att slutföra den. Om du är klar innan slutdatum stoppar du alltid granskningen tidigt.

### <a name="choose-a-role-to-review"></a>Välj en roll för att granska
Varje granska fokuserar på endast en roll. Såvida du inte startade granskningen åtkomst från en viss roll-bladet, måste du välja en roll nu.

1. Navigera till **granska rollmedlemskap**
   
    ![Granska rollmedlemskap – skärmbild][3]
2. Välj en roll i listan.

### <a name="decide-who-will-perform-the-review"></a>Bestämma vem som utför granskningen
Det finns tre alternativ för att utföra en granskning. Du kan tilldela granskningen till någon annan att slutföra, så kan du göra det själv eller du kan låta användarna granska sina egna åtkomst.

1. Gå till **Välj granskare**
   
    ![Välj granskare – skärmbild][4]
2. Välj något av alternativen:
   
   * **Välj granskare**: Använd det här alternativet när du inte vet vem som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägare eller grupp manager för att slutföra.
   * **Mig**: användbart om du vill förhandsgranska hur åtkomst granskar arbete eller du vill granska för personer som inte.
   * **Medlemmar Läs själva**: Använd det här alternativet för att be användarna att granska sina egna rolltilldelningar.

### <a name="start-the-review"></a>Starta granskningen
Slutligen har möjlighet att kräva att användare anger en orsak till om de godkänner deras åtkomst. Om du vill lägga till en beskrivning för granska och välj **starta**.

Kontrollera att du ger användarna om att det finns en åtkomst-granskning väntar på dem och visa dem [hur du utför en åtkomst-granskning](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomst-granskning
Du kan följa förloppet när granskare Slutför granskningarna i Azure AD PIM-instrumentpanelen i avsnittet åtkomst granskningar. Ingen behörighet kommer att ändras i katalogen tills [granskningen är klar](active-directory-privileged-identity-management-how-to-complete-review.md).

Tills omprövningsperioden är över, du påminna användare att slutföra granskning eller stoppa granska tidigt från avsnittet åtkomst granskningar.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>PIM innehållsförteckning
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
