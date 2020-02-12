---
title: Återställa en borttagen instrument panel i Azure Portal | Microsoft Docs
description: Om du tar bort en publicerad instrument panel i Azure Portal kan du återställa instrument panelen.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133295"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Återställa en borttagen instrument panel i Azure Portal

Om du är i det offentliga Azure-molnet och du tar bort en _publicerad_ instrument panel i Azure Portal kan du återställa instrument panelen inom 14 dagar från borttagningen. Om du befinner dig i ett moln i Azure-myndigheter eller om instrument panelen inte har publicerats kan du inte återställa den och du måste återskapa den. Mer information om hur du publicerar en instrument panel finns i [publicera instrument panel](azure-portal-dashboard-share-access.md#publish-dashboard). Följ de här stegen för att återställa en publicerad instrument panel:

1. Välj **resurs grupper**på Azure Portal-menyn och välj sedan den resurs grupp där du publicerade instrument panelen (som standard kallas den för **instrument paneler**).

1. Under **aktivitets logg**expanderar du åtgärden **ta bort instrument panel** . Välj fliken **ändrings historik** och välj sedan **\<borttagen resurs\>** .

    ![Skärm bild av fliken ändrings historik](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Markera och kopiera innehållet i den vänstra rutan och spara sedan till en textfil med fil namns tillägget _. JSON_ . Portalen använder JSON-filen för att återskapa instrument panelen.

    ![Skärm bild av skillnad i ändrings historik](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Välj **instrument paneler**på Azure Portal-menyn och välj sedan **Ladda upp**.

    ![Skärm bild av inladdade instrument panel](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Välj den JSON-fil som du sparade. Portalen skapar instrument panelen på nytt med samma namn och element som den borttagna instrument panelen.

1. Välj **dela** för att publicera instrument panelen och återupprätta lämplig åtkomst kontroll.

    ![Skärm bild av instrument panelens resurs](media/recover-shared-deleted-dashboard/dashboard-share.png)
