---
title: Åtkomstkontroll för Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med fördefinierade och anpassade parameteriserade arbetsböcker med rollbaserad åtkomstkontroll
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658429"
---
# <a name="access-control"></a>Åtkomstkontroll

Åtkomstkontroll i arbetsböcker refererar till två saker:

* Åtkomst krävs för att läsa data i en arbetsbok. Den här åtkomsten styrs av [standardiserade Azure-roller](https://docs.microsoft.com/azure/role-based-access-control/overview) på de resurser som används i arbetsboken. Arbetsböcker anger eller konfigurerar inte åtkomst till dessa resurser. Användare skulle vanligtvis få denna tillgång till dessa resurser med hjälp av [monitoring reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) roll på dessa resurser.

* Åtkomst krävs för att spara arbetsböcker

    - För `("My")` att spara privata arbetsböcker krävs inga ytterligare privilegier. Alla användare kan spara privata arbetsböcker och bara de kan se dessa arbetsböcker.
    - För att spara delade arbetsböcker krävs skrivbehörighet i en resursgrupp för att arbetsboken ska kunna sparas. Dessa privilegier anges vanligtvis av rollen [Övervakningsdeltagare,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) men kan också ställas in via rollen *Arbetsböcker deltagare.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standardroller med arbetsboksrelaterade privilegier

[Övervakningsläsaren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) innehåller standard-/läsbehörighet som skulle användas av övervakningsverktyg (inklusive arbetsböcker) för att läsa data från resurser.

[Övervakningsbidragsgivaren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) innehåller allmänna `/write` privilegier som `workbooks/write` används av olika övervakningsverktyg för att spara objekt (inklusive behörighet att spara delade arbetsböcker).
"Arbetsböcker Deltagare" lägger till behörigheter för arbetsböcker/skriv i ett objekt för att spara delade arbetsböcker.
Det krävs inga särskilda privilegier för att användarna ska kunna spara privata arbetsböcker som bara de kan se.

För anpassad rollbaserad åtkomstkontroll:

Lägg `microsoft.insights/workbooks/write` till för att spara delade arbetsböcker. Mer information finns i rollen [Arbetsboksdeltagare.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
