---
title: Startar en åtkomst-granskning i PIM för Azure-resurser | Microsoft Docs
description: Beskriver hur du startar en åtkomst-granskning i Privileged Identity Management för Azure-resurser
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management - resurs role - Start åtkomst granskning
Rolltilldelningar blir ”inaktiva” när användare privilegierad åtkomst som de inte behöver längre. För att minska riskerna i samband med dessa inaktuella rolltilldelningar bör Privilegierade rollen administratörer regelbundet granska de roller som användarna har fått. Det här dokumentet beskriver steg för att starta en åtkomst-granskning i Privileged Identity Management (PIM) för Azure-resurser.

Från PIM programmet huvudsidan, navigerar du till:

* **Åtkomst till granskningar** > **Lägg till**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Om du klickar på den **Lägg till** knappen, den **skapa en åtkomst-granska** bladet visas. På det här bladet ska du konfigurera granskning med ett namn och en tid, Välj en roll för att granska och bestämma vem som utför granskningen.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Konfigurera granskning
Om du vill skapa en åtkomst-granskning, måste du ge den namnet och ange start-och.

![Konfigurera granskning – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Kontrollera längden på Granska tillräckligt länge för användare att slutföra den. Om du är klar innan slutdatum stoppar du alltid granskningen tidigt.

### <a name="choose-a-role-to-review"></a>Välj en roll för att granska
Varje granska fokuserar på endast en roll. Såvida du inte startade granskningen åtkomst från en viss roll-bladet, måste du välja en roll nu.

1. Navigera till **granska rollmedlemskap**
   
    ![Granska rollmedlemskap – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Välj en roll i listan.

### <a name="decide-who-will-perform-the-review"></a>Bestämma vem som utför granskningen
Det finns tre alternativ för att utföra en granskning. Du kan tilldela granskningen till någon annan att slutföra, så kan du göra det själv eller du kan låta användarna granska sina egna åtkomst.

1. Välj något av alternativen:
   
   * **Markerade användare**: Använd det här alternativet när du inte vet vem som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägare eller grupp manager för att slutföra.
   * **Tilldelade (self)**: Använd det här alternativet för att be användarna att granska sina egna rolltilldelningar.
   
2. Gå till **Välj granskare**
   
    ![Välj granskare – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Starta granskningen
Slutligen har möjlighet att kräva att användare anger en orsak till om de godkänner deras åtkomst. Om du vill lägga till en beskrivning för granska och välj **starta**.

Kontrollera att du ger användarna om att det finns en åtkomst-granskning väntar på dem och visa dem [hur du utför en åtkomst-granskning](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomst-granskning
Du kan följa förloppet när granskare Slutför granskningarna i instrumentpanelen för PIM Azure-resurser i avsnittet åtkomst granskningar. Ingen behörighet kommer att ändras i katalogen tills [granskningen är klar](pim-resource-roles-complete-access-review.md).

Tills omprövningsperioden är över, du påminna användare att slutföra granskning eller stoppa granska tidigt från avsnittet åtkomst granskningar.

