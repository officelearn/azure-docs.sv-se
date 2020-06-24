---
title: Återställa en borttagen instrument panel i Azure Portal | Microsoft Docs
description: Om du tar bort en publicerad instrument panel i Azure Portal kan du återställa instrument panelen.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7b3cc088a87731d2a118a4fe5183831e4d1bd6cc
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763983"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Återställa en borttagen instrumentpanel i Azure-portalen

Om du är i det offentliga Azure-molnet och du tar bort en _publicerad_ instrument panel i Azure Portal kan du återställa instrument panelen inom 14 dagar från borttagningen. Om du befinner dig i ett moln i Azure-myndigheter eller om instrument panelen inte har publicerats kan du inte återställa den och du måste återskapa den. Mer information om hur du publicerar en instrument panel finns i [publicera instrument panel](azure-portal-dashboard-share-access.md#publish-dashboard). Följ de här stegen för att återställa en publicerad instrument panel:

1. Välj **resurs grupper**på Azure Portal-menyn och välj sedan den resurs grupp där du publicerade instrument panelen (som standard kallas den för **instrument paneler**).

1. Under **aktivitets logg**expanderar du åtgärden **ta bort instrument panel** . Välj fliken **ändrings historik** och välj sedan **\<deleted resource\>** .

    ![Skärm bild av fliken ändrings historik](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Markera och kopiera innehållet i den vänstra rutan och spara sedan till en textfil med fil namns tillägget _. JSON_ . Portalen använder JSON-filen för att återskapa instrument panelen.

    ![Skärm bild av skillnad i ändrings historik](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Välj **instrument paneler**på Azure Portal-menyn och välj sedan **Ladda upp**.

    ![Skärm bild av inladdade instrument panel](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Välj den JSON-fil som du sparade. Portalen skapar instrument panelen på nytt med samma namn och element som den borttagna instrument panelen.

1. Välj **dela** för att publicera instrument panelen och återupprätta lämplig åtkomst kontroll.

    ![Skärm bild av instrument panelens resurs](media/recover-shared-deleted-dashboard/dashboard-share.png)
