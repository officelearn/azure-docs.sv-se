---
title: Vad är Azure Service Health?
description: Anpassad information om hur dina Azure-appar som påverkas av problem med aktuella och kommande Azure-tjänsten och underhåll.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 058a171766680f09eaf4de14ddd25235020b1ba4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079579"
---
# <a name="what-is-azure-service-health"></a>Vad är Azure Service Health?

Azure erbjuder en uppsättning upplevelser för att hålla dig informerad om hälsotillståndet för dina molnresurser. Informationen omfattar aktuella och kommande problem som påverkar händelser, planerat underhåll och andra ändringar som kan påverka tillgängligheten till din tjänst.

Azure Service Health är en kombination av tre separata mindre tjänster.

[Azure-status](azure-status-overview.md) informerar dig om tjänstavbrott i Azure på den  **[Azures statussida](https://status.azure.com)** . Sidan är en global vy över hälsotillståndet för alla Azure-tjänster i alla Azure-regioner. På statussidan är en bra referens för incidenter med omfattande påverkan, men vi rekommenderar starkt att aktuella Azure-användare utnyttjar Azure service health för att hålla dig informerad om ärenden i Azure och underhåll.

[Azure service fabric](service-health-overview.md) innehåller en anpassad vy över hälsotillståndet för Azure-tjänster och regioner som du använder. Det är den bästa platsen att leta efter information rörande avbrott som påverkar tjänster, planerat underhåll och andra hälsoråd eftersom den autentiserade Azure Service Health-tjänsten vet vilka tjänster och resurser du använder. Det bästa sättet att använda Service Health är genom att konfigurera Service Health-aviseringar som informerar dig om problem med tjänsten, planerat underhåll eller andra ändringar som kan påverka dina Azure-tjänster och -regioner via de kommunikationskanaler som du själv valt.

[Azure resource health](resource-health-overview.md) innehåller information om hälsotillståndet för enskilda molnresurser, till exempel en specifik virtuell datorinstans. Med hjälp av Azure Monitor kan du även konfigurera aviseringar som informerar dig om förändringar för dina molnresursers tillgänglighet. Genom att använda Azure Resource Health och Azure Monitor-aviseringar kan du hålla dig informerad om dina resursers tillgänglighet minut för minut och snabbt utvärdera om ett problem beror på något i ditt eget system eller om det uppstått på grund av en händelse på Azure-plattformen.

Sammantaget ger dessa upplevelser dig en heltäckande översikt över hälsotillståndet för Azure, med den precision som är mest relevant för dig.

**Titta på en översikt över Azure-Status-sidan, Azure Service Health och Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]
