---
title: Så här använder du granskningsloggen i Azure AD Privileged Identity Management | Microsoft Docs
description: Lär dig hur du använder du granskningsloggen i Azure Privileged Identity Management-tillägget.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: af3ba66bbc0be3aca3999abc6a0de5ec0e0aae95
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621444"
---
# <a name="using-the-audit-log-in-pim"></a>Använda granskningsloggen i PIM
Du kan använda granskningsloggen Privileged Identity Management (PIM) för att se alla användartilldelningar och aktiveringar inom en viss tidsperiod. Om du vill visa fullständig granskningshistorik för aktiviteten i din klient, inklusive administratören, slutanvändare och synkroniseringsåtgärden, kan du använda den [rapporter för åtkomst och användning av Azure Active Directory.](../active-directory-reporting-azure-portal.md)

## <a name="navigate-to-the-audit-log"></a>Gå in i spårningsloggen
Från den [Azure-portalen](https://portal.azure.com) instrumentpanelen, väljer den **Azure AD Privileged Identity Management** app. Därifrån kan du komma åt granskningsloggen genom att klicka på **hantera Privilegierade roller** > **granskningshistorik** i PIM-instrumentpanelen.

## <a name="the-audit-log-graph"></a>Granska loggen graph
Du kan använda granskningsloggen för att visa totalt antal aktiveringar, max aktiveringar per dag och genomsnittlig aktiveringar per dag i ett linjediagram.  Du kan också filtrera data efter roll om det finns mer än en roll i granskningshistoriken.

Använd den **tid**, **åtgärd**, och **rollen** knappar för att sortera loggen.

## <a name="the-audit-log-list"></a>Listan Granska loggen
Kolumnerna i listan Granska loggen är:

* **Begärande** – den användare som begärde rollaktivering eller ändra.  Om värdet är ”Azure System”, kontrollera Azure granskningsloggen för mer information.
* **Användaren** -användare som är genom att aktivera eller tilldelade till en roll.
* **Rollen** -rollen tilldelning eller aktivering av användaren.
* **Åtgärden** – de åtgärder som vidtas av förfrågaren. Detta kan inkludera tilldelning, vid ej tilldelning, aktivering eller inaktivering.
* **Tid** – när åtgärden inträffade.
* **Skäl till** – om text har angetts i fältet Orsak under aktivering, visas den här.
* **Upphör att gälla** – gäller endast för aktivering av roller.

## <a name="filter-the-audit-log"></a>Filtrera granskningsloggen
Du kan filtrera den information som visas i granskningsloggen genom att klicka på den **Filter** knappen.  Den **uppdatering diagrammet parametrar bladet** visas.

När du har angett filtren klickar du på **uppdatering** att filtrera data i loggen.  Uppdatera sidan om data inte visas direkt.

### <a name="change-the-date-range"></a>Ändra datumintervallet
Använd den **idag**, **senaste veckan**, **senaste månaden**, eller **anpassad** knappar för att ändra tidsintervallet för granskningsloggen.

När du väljer den **anpassade** knapp, du får en **från** datumfält och en **till** datumfält så att du anger ett datumintervall för loggen.  Du kan ange datum i formatet ÅÅÅÅ-MM-DD eller klicka på den **kalender** ikonen och Välj datum från en kalender.

### <a name="change-the-roles-included-in-the-log"></a>Ändra de roller som ingår i loggen
Markera eller avmarkera den **rollen** kryssrutan bredvid varje roll ska tas med eller undantas från loggen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

