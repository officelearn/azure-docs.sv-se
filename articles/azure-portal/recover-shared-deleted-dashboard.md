---
title: Återställa en borttagen instrumentpanel i Azure-portalen | Microsoft-dokument
description: Om du tar bort en publicerad instrumentpanel i Azure-portalen kan du återställa instrumentpanelen.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133295"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Återställa en borttagen instrumentpanel i Azure-portalen

Om du är i det offentliga Azure-molnet och tar bort en _publicerad_ instrumentpanel i Azure-portalen kan du återställa instrumentpanelen inom 14 dagar efter borttagningen. Om du är i ett Azure government-moln eller om instrumentpanelen inte publiceras kan du inte återställa det och du måste återskapa det. Mer information om hur du publicerar en instrumentpanel finns i [Publicera instrumentpanel](azure-portal-dashboard-share-access.md#publish-dashboard). Så här återställer du en publicerad instrumentpanel:

1. På Portal-menyn i Azure väljer du **Resursgrupper**och väljer sedan den resursgrupp där du publicerade instrumentpanelen (som standard heter den **instrumentpaneler**).

1. Expandera åtgärden Ta **bort instrumentpanel** under **Aktivitetslogg.** Välj fliken **Ändra historik** och välj ** \<\>** sedan borttagen resurs .

    ![Skärmbild av fliken Ändringshistorik](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Markera och kopiera innehållet i den vänstra rutan och spara sedan i en textfil med _filnamnstillägget .json._ Portalen använder JSON-filen för att återskapa instrumentpanelen.

    ![Skärmbild av ändringshistorik diff](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. På Portal-menyn i Azure väljer du **Instrumentpaneler**och väljer sedan **Ladda upp**.

    ![Skärmbild av instrumentpanelsuppladdning](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Markera den JSON-fil som du sparade. Portalen återskapar instrumentpanelen med samma namn och element som den borttagna instrumentpanelen.

1. Välj **Dela** om du vill publicera instrumentpanelen och återupprätta lämplig åtkomstkontroll.

    ![Skärmbild av instrumentpanelsresurs](media/recover-shared-deleted-dashboard/dashboard-share.png)
