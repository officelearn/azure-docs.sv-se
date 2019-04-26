---
title: Visa granskningshistorik för Azure AD-roller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du visar granskningshistorik för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c080173af8ddd31b077bb820ea19d82eb2b29300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440805"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visa granskningshistorik för Azure AD-roller i PIM
Du kan använda granskningshistorik för Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att se alla användartilldelningar och aktiveringar inom en viss tidsperiod för alla Privilegierade roller. Om du vill visa fullständig granskningshistorik för aktiviteten i din klient, inklusive administratören, slutanvändare och synkroniseringsåtgärden, kan du använda den [rapporter för åtkomst och användning av Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Navigera och granskningshistorik
Från den [Azure-portalen](https://portal.azure.com) instrumentpanelen, väljer den **Azure AD Privileged Identity Management** app. Därifrån kan du komma åt granskningshistoriken genom att klicka på **hantera Privilegierade roller** > **granskningshistorik** i PIM-instrumentpanelen.

![Granskningshistorik](media/azure-ad-pim-approval-workflow/image021.png)

> [!NOTE]
> Du kan sortera data av åtgärden och leta efter ”aktivering Approved”


## <a name="audit-history-graph"></a>Granska historiken graph
Du kan använda granskningshistoriken för att visa totalt antal aktiveringar, max aktiveringar per dag och genomsnittlig aktiveringar per dag i ett linjediagram.  Du kan också filtrera data efter roll om det finns mer än en roll i granskningshistoriken.

Använd den **tid**, **åtgärd**, och **rollen** knappar för att sortera historiken.

## <a name="audit-history-list"></a>Granska listan med kommandohistorik
Kolumnerna i listan Granska historiken är:

* **Begärande** – den användare som begärde rollaktivering eller ändra.  Om värdet är ”Azure System”, kontrollera Azure granskningshistoriken för mer information.
* **Användaren** -användare som är genom att aktivera eller tilldelade till en roll.
* **Rollen** -rollen tilldelning eller aktivering av användaren.
* **Åtgärden** – de åtgärder som vidtas av förfrågaren. Detta kan inkludera tilldelning, vid ej tilldelning, aktivering eller inaktivering.
* **Tid** – när åtgärden inträffade.
* **Skäl till** – om text har angetts i fältet Orsak under aktivering, visas den här.
* **Upphör att gälla** – gäller endast för aktivering av roller.

## <a name="filter-audit-history"></a>Filtrera granskningshistorik
Du kan filtrera den information som visas i granskningshistoriken genom att klicka på den **Filter** knappen.  Den **uppdatering diagrammet parametrar bladet** visas.

När du har angett filtren klickar du på **uppdatering** att filtrera data i historiken.  Uppdatera sidan om data inte visas direkt.

### <a name="change-the-date-range"></a>Ändra datumintervallet
Använd den **idag**, **senaste veckan**, **senaste månaden**, eller **anpassad** knappar för att ändra tidsintervallet för granskningshistoriken.

När du väljer den **anpassade** knapp, du får en **från** datumfält och en **till** datumfält så att du anger ett datumintervall för historiken.  Du kan ange datum i formatet ÅÅÅÅ-MM-DD eller klicka på den **kalender** ikonen och Välj datum från en kalender.

### <a name="change-the-roles-included-in-the-history"></a>Ändra de roller som ingår i historiken
Markera eller avmarkera den **rollen** kryssrutan bredvid varje roll ska tas med eller undantas från historiken.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

- [Visa aktivitet och granskningshistorik för Azure-resursroller i PIM](azure-pim-resource-rbac.md)
