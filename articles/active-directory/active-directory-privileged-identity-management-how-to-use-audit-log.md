---
title: Så här använder du granskningsloggen i Azure AD Privileged Identity Management | Microsoft Docs
description: Lär dig hur du använder du granskningsloggen i Azure Privileged Identity Management-tillägget.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 05a70874367d578beee1dc605510f9370b6abd42
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233000"
---
# <a name="using-the-audit-log-in-pim"></a>Använda granskningsloggen i PIM
Du kan använda granskningsloggen Privileged Identity Management (PIM) om du vill visa alla användare och aktiveringar inom en viss tidsperiod. Om du vill visa fullständig granskningshistorik för aktivitet i din klient, inklusive administratör, slutanvändare och synkroniseringsåtgärden, kan du använda den [rapporter för åtkomst till och användning av Azure Active Directory.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Navigera till granskningslogg
Från den [Azure-portalen](https://portal.azure.com) instrumentpanelen, väljer den **Azure AD Privileged Identity Management** app. Därifrån kan komma åt granskningsloggen genom att klicka på **hantera Privilegierade roller** > **granskningshistorik** PIM-instrumentpanelen.

## <a name="the-audit-log-graph"></a>Granska loggen diagrammet
Du kan använda granskningsloggen för att visa totalt antal aktiveringar, max aktiveringar per dag och genomsnittlig aktiveringar per dag i ett linjediagram.  Du kan filtrera informationen efter roll om det finns fler än en roll i granskningshistoriken.

Använd den **tid**, **åtgärd**, och **rollen** knappar för att sortera loggen.

## <a name="the-audit-log-list"></a>Listan Granska loggen
Kolumner i listan Granska loggen är:

* **Begärande** -användaren som begärde rollaktivering eller ändra.  Kontrollera Azure granskningsloggen för mer information om värdet är ”Azure System”.
* **Användaren** -användaren som är genom att aktivera eller tilldelade till en roll.
* **Rollen** -rollen tilldelas eller aktiveras av användaren.
* **Åtgärden** – de åtgärder som vidtas av förfrågaren. Detta kan inkludera tilldelning vid ej tilldelning, aktivering eller inaktivering.
* **Tid** – när åtgärden utfördes.
* **Skäl till** -om text har angetts i fältet Orsak under aktivering, visas här.
* **Förfallodatum** – endast relevant för aktivering av roller.

## <a name="filter-the-audit-log"></a>Filtrera granskningsloggen
Du kan filtrera den information som visas i granskningsloggen genom att klicka på den **Filter** knappen.  Den **uppdatering diagram parametrar bladet** visas.

När du har angett filtren klickar du på **uppdatering** att filtrera data i loggen.  Om data inte visas direkt, uppdatera sidan.

### <a name="change-the-date-range"></a>Ändra datumintervallet
Använd den **idag**, **gångna veckan**, **senaste månaden**, eller **anpassad** knappar för att ändra tidsintervallet på granskningsloggen.

När du väljer den **anpassad** knappen, får du en **från** datum och en **till** datumfält att ange ett datumintervall för loggen.  Du kan ange datum i formatet ÅÅÅÅ-MM-DD eller klicka på den **kalender** ikonen och Välj datum i en kalender.

### <a name="change-the-roles-included-in-the-log"></a>Ändra de roller som ingår i loggen
Markera eller avmarkera den **rollen** kryssrutan bredvid varje roll för att inkludera eller exkludera från loggen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

