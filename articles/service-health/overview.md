---
title: Vad är Azure Service Health?
description: Anpassad information om hur dina Azure-appar påverkas av aktuella och framtida Problem med Azure-tjänster och underhåll.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77654094"
---
# <a name="what-is-azure-service-health"></a>Vad är Azure Service Health?

Azure erbjuder en uppsättning upplevelser för att hålla dig informerad om hälsotillståndet för dina molnresurser. Den här informationen omfattar aktuella och kommande problem som att påverka händelser som påverkar tjänsten, planerat underhåll och andra ändringar som kan påverka din tillgänglighet.

Azure Service Health är en kombination av tre separata mindre tjänster.

[Azure-status](azure-status-overview.md) informerar dig om avbrott i tjänsten i Azure på **[sidan Azure-status](https://status.azure.com)**. Sidan är en global vy över hälsotillståndet för alla Azure-tjänster i alla Azure-regioner. Statussidan är en bra referens för incidenter med omfattande påverkan, men vi rekommenderar starkt att nuvarande Azure-användare utnyttjar Azure-tjänstens hälsotillstånd för att hålla dig informerad om Azure-incidenter och underhåll.

[Azure-tjänstens hälsotillstånd](service-health-overview.md) ger en anpassad vy över hälsotillståndet för Azure-tjänster och regioner som du använder. Det är den bästa platsen att leta efter information rörande avbrott som påverkar tjänster, planerat underhåll och andra hälsoråd eftersom den autentiserade Azure Service Health-tjänsten vet vilka tjänster och resurser du använder. Det bästa sättet att använda Service Health är genom att konfigurera Service Health-aviseringar som informerar dig om problem med tjänsten, planerat underhåll eller andra ändringar som kan påverka dina Azure-tjänster och -regioner via de kommunikationskanaler som du själv valt.

[Azure-resurshälsa](resource-health-overview.md) ger information om hälsotillståndet för dina enskilda molnresurser, till exempel en specifik instans för virtuella datorer. Med hjälp av Azure Monitor kan du även konfigurera aviseringar som informerar dig om förändringar för dina molnresursers tillgänglighet. Genom att använda Azure Resource Health och Azure Monitor-aviseringar kan du hålla dig informerad om dina resursers tillgänglighet minut för minut och snabbt utvärdera om ett problem beror på något i ditt eget system eller om det uppstått på grund av en händelse på Azure-plattformen.

Sammantaget ger dessa upplevelser dig en heltäckande översikt över hälsotillståndet för Azure, med den precision som är mest relevant för dig.

**Titta på en översikt över Azure-Status-sidan, Azure Service Health och Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]