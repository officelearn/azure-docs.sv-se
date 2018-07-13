---
title: Så här startar du en åtkomstgranskning | Microsoft Docs
description: Lär dig hur du skapar en åtkomstgranskning för privilegierade identiteter med Azure Privileged Identity Management-programmet.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3ead2fe01c932c76a83e989d8908de4c9bfa541b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590354"
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Så här startar du en åtkomstgranskning i Azure AD Privileged Identity Management
Rolltilldelningar blir ”inaktuell” när användarna har privilegierad åtkomst som de inte behöver längre. För att minska riskerna med dessa inaktuella rolltilldelningar Privilegierade roller bör eller globala administratörer regelbundet skapa åtkomstgranskningar om du vill ställa administratörer att granska de roller som användare har fått. Det här dokumentet beskriver steg för att starta en åtkomstgranskning i Azure AD Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Starta en åtkomstgranskning
> [!NOTE]
> Om du inte har lagt till PIM-programmet på instrumentpanelen i Azure-portalen, hittar du i [komma igång med Azure Privileged Identity Management](pim-getting-started.md)
> 
> 

Det finns tre sätt att starta en åtkomstgranskning från PIM programmet huvudsidan:

* **Åtkomstgranskningar** > **Lägg till**
* **Roller** > **granska** knappen
* Välj rollen specifika granskas från listan över roller > **granska** knappen

När du klickar på den **granska** knapp, den **starta en åtkomstgranskning** bladet visas. På det här bladet ska du konfigurera granskningen med ett namn och en tid, Välj en roll att granska och bestämma vem som ska utföra granskningen.

![Starta en åtkomstgranskning – skärmbild](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>Konfigurera granskningen
Om du vill skapa en åtkomstgranskning, måste du ge den namnet och ange ett start- och datum.

![Konfigurera granskning – skärmbild](./media/pim-how-to-start-security-review/PIM_review_configure.png)

Kontrollera längden på Granska tillräckligt länge för användare att slutföra den. Om du är klar innan slutdatumet kan stoppa du alltid granskningen tidigt.

### <a name="choose-a-role-to-review"></a>Välj en roll att granska
Varje recension fokuserar på endast en roll. Såvida du inte startade åtkomstgranskningen från en viss roll-bladet, måste du välja en roll nu.

1. Gå till **granska rollmedlemskap**
   
    ![Granska rollmedlemskap – skärmbild](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. Välj en roll i listan.

### <a name="decide-who-will-perform-the-review"></a>Bestämma vem som ska utföra granskningen
Det finns tre alternativ för att utföra en granskning. Du kan tilldela granskningen till någon annan att slutföra, du kan göra det själv eller du kan ha varje användare ska granska sin egen åtkomst.

1. Gå till **Välj granskare**
   
    ![Välj granskare – skärmbild](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. Välj något av alternativen:
   
   * **Välj granskare**: Använd det här alternativet om du inte vet vilka som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägaren eller gruppansvarig för att slutföra.
   * **Mig**: användbart om du vill förhandsgranska hur åtkomstgranskningar arbete eller du vill granska åt personer som inte.
   * **Medlemmar kan granska själva**: Använd det här alternativet för att be användarna granska sin egen rolltilldelningar.

### <a name="start-the-review"></a>Starta granskningen
Slutligen har möjlighet att kräva att användare måste ange en orsak till om de godkänner deras åtkomst. Om du vill lägga till en beskrivning av granskningen och välj **starta**.

Kontrollera att du ger användarna vet att det finns en åtkomstgranskning som väntar på dem och visa dem [så här utför du en åtkomstgranskning](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen
Du kan följa förloppet när granskarna har slutfört sina granskningar i instrumentpanelen för Azure AD PIM i avsnittet om åtkomst granskningar. Ingen behörighet kommer att ändras i katalogen tills [granskningen har slutförts](pim-how-to-complete-review.md).

Tills granskningsperioden är över, kan du påminna användarna om att slutföra sina granskningar eller stoppa granskningen tidigt från avsnittet om åtkomst granskningar.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>PIM innehållsförteckning
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
