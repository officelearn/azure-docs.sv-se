---
title: Utför åtkomst granskningar i Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du startar en åtkomst-granskning i Privileged Identity Management för Azure-resurser
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b5c2f13386a996a6c7895bd4755b6cf609a5df72
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233262"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Utför åtkomst granskningar i Azure-resurser med hjälp av Privileged Identity Management
Rolltilldelningar blir ”inaktiva” när användare privilegierad åtkomst som de inte behöver längre. För att minska risken för att associeras med dessa inaktuella rolltilldelningar bör Privilegierade rollen administratörer regelbundet granska roller. Det här dokumentet beskriver steg för att starta en åtkomst-granskning i Privileged Identity Management (PIM) för Azure-resurser.

PIM programmet huvudsidan, gå till:

* **Åtkomst till granskningar** > **Lägg till**

![Lägg till åtkomst granskningar](media/azure-pim-resource-rbac/rbac-access-review-home.png)

När du väljer den **Lägg till** knappen, den **skapa en åtkomst-granska** bladet visas. Konfigurera granskning med ett namn och en tid, Välj en roll för att granska och sedan bestämmer vem som gör att granskningen på det här bladet.

![Skapa en åtkomstgranskning](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Konfigurera granskning
Om du vill skapa en åtkomst-granskning först namnet och ange sedan start-och slutpunkt.

![Konfigurera granskning – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Kontrollera längden på Granska tillräckligt länge för användare att slutföra den. Om de avslutas innan slutdatum slutar de alltid vid granskningen tidigt.

### <a name="choose-a-role-to-review"></a>Välj en roll för att granska
Varje granska fokuserar på endast en roll. Såvida du inte startade granskningen åtkomst från en viss roll-bladet, måste du välja en roll nu.

1. Gå till **granska rollmedlemskap**
   
    ![Granska rollmedlemskap – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Välj en roll i listan.

### <a name="decide-who-will-perform-the-review"></a>Bestämma vem som utför granskningen
Det finns tre alternativ för att utföra en granskning. Du kan tilldela granskningen till någon annan att slutföra, så kan du göra det själv eller varje användare kan granska sina egna åtkomst.

1. Välj något av alternativen:
   
   * **Markerade användare**: Använd det här alternativet när du inte vet vem som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägare eller grupp manager för att slutföra.
   * **Tilldelade (self)**: Använd det här alternativet för att be användarna att granska sina egna rolltilldelningar.
   
2. Gå till **Markera granskare**.
   
    ![Välj granskare – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Starta granskningen
Slutligen kan du kräva att användare anger en anledning för att godkänna åtkomst. Lägg till en beskrivning för granska om du vill. Välj sedan **starta**.

Kontrollera att du ger användarna om att det finns en åtkomst-granskning väntar på dem och visa dem [hur du utför en åtkomst-granskning](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomst-granskning
Du kan följa förloppet när granskare Slutför granskningarna i instrumentpanelen i Azure PIM-resurser. Ingen behörighet har ändrats i katalogen tills [granskningen har slutförts](pim-resource-roles-complete-access-review.md).

Tills omprövningsperioden är över, du påminna användare att slutföra granskning eller stoppa granska tidigt från avsnittet åtkomst granskningar.

