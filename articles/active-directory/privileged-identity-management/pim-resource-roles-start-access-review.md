---
title: Starta en åtkomstgranskning för Azure-resursroller i PIM | Microsoft Docs
description: Lär dig mer om att starta en åtkomstgranskning för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 607104c5a8021107be9e343d71ade049712454f6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160057"
---
# <a name="start-an-access-review-for-azure-resource-roles-in-pim"></a>Starta en åtkomstgranskning för Azure-resursroller i PIM
Rolltilldelningar blir ”inaktuell” när användarna har privilegierad åtkomst som de inte behöver längre. För att minska risken som associeras med de här rolltilldelningarna som inaktuella, Privilegierade rolladministratörer bör regelbundet läsa igenom roller. Det här dokumentet beskriver steg för att starta en åtkomstgranskning i Privileged Identity Management (PIM) för Azure-resurser.

Gå till från PIM programmet huvudsidan:

* **Åtkomstgranskningar** > **Lägg till**

![Lägg till åtkomstgranskningar](media/azure-pim-resource-rbac/rbac-access-review-home.png)

När du väljer den **Lägg till** knapp, den **skapa en åtkomstgranskning** bladet visas. Konfigurera granskningen med ett namn och en tidsgräns på det här bladet, Välj en roll att granska och sedan bestämmer vem som gör att granskningen.

![Skapa en åtkomstgranskning](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Konfigurera granskningen
Om du vill skapa en åtkomstgranskning Döp först och ange sedan ett start- och datum.

![Konfigurera granskning – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Kontrollera längden på Granska tillräckligt länge för användare att slutföra den. Om de är klara före slutdatumet, kan de alltid stoppa granskningen tidigt.

### <a name="choose-a-role-to-review"></a>Välj en roll att granska
Varje recension fokuserar på endast en roll. Såvida du inte startade åtkomstgranskningen från en viss roll-bladet, måste du välja en roll nu.

1. Gå till **granska rollmedlemskap**
   
    ![Granska rollmedlemskap – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Välj en roll i listan.

### <a name="decide-who-will-perform-the-review"></a>Bestämma vem som ska utföra granskningen
Det finns tre alternativ för att utföra en granskning. Du kan tilldela granskningen till någon annan att slutföra, du kan göra det själv eller varje användare kan granska sin egen åtkomst.

1. Välj något av alternativen:
   
   * **Markerade användare**: Använd det här alternativet när du inte vet vilka som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägaren eller gruppansvarig för att slutföra.
   * **Tilldelad (själv)**: Använd det här alternativet för att be användarna granska sin egen rolltilldelningar.
   
2. Gå till **Välj granskare**.
   
    ![Välj granskare – skärmbild](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Starta granskningen
Slutligen kan du kräva att användare måste ange en anledning för att godkänna åtkomst. Lägg till en beskrivning av granskningen om du vill. Välj sedan **starta**.

Kontrollera att du ger användarna vet att det finns en åtkomstgranskning som väntar på dem och visa dem [så här utför du en åtkomstgranskning](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen
Du kan spåra förloppet när granskarna har slutfört sina granskningar i instrumentpanelen för PIM-Azure-resurser. Ingen behörighet har ändrats i katalogen tills [granskningen har slutförts](pim-resource-roles-complete-access-review.md).

Tills granskningsperioden är över, kan du påminna användarna om att slutföra sina granskningar eller stoppa granskningen tidigt från avsnittet om åtkomst granskningar.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomstgranskning för Azure-resursroller i PIM](pim-resource-roles-complete-access-review.md)
- [Utför en åtkomstgranskning av mina Azure-resursroller i PIM](pim-resource-roles-perform-access-review.md)
- [Starta en åtkomstgranskning för Azure AD-katalogroller i PIM](pim-how-to-start-security-review.md)
