---
title: Vad är Azure Service Health?
description: Anpassad information om hur dina Azure-appar påverkas av aktuella och framtida problem och underhåll av Azure-tjänsten.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654094"
---
# <a name="what-is-azure-service-health"></a>Vad är Azure Service Health?

Azure erbjuder en serie upplevelser för att hålla dig informerad om hälso tillståndet för dina moln resurser. Den här informationen omfattar aktuella och kommande problem som tjänst påverkan på händelser, planerat underhåll och andra ändringar som kan påverka tillgängligheten.

Azure Service Health är en kombination av tre separata mindre tjänster.

[Azure-status](azure-status-overview.md) informerar dig om service avbrott i Azure på **[sidan Azure-status](https://status.azure.com)** . Sidan är en global vy över hälsan för alla Azure-tjänster i alla Azure-regioner. Sidan status är en referens för incidenter med omfattande påverkan, men vi rekommenderar starkt att aktuella Azure-användare utnyttjar Azures hälso tillstånd för att hålla dig informerad om Azure-incidenter och underhåll.

[Azure Service Health](service-health-overview.md) ger en anpassad vy över hälso tillståndet för de Azure-tjänster och regioner som du använder. Det är den bästa platsen att leta efter information rörande avbrott som påverkar tjänster, planerat underhåll och andra hälsoråd eftersom den autentiserade Azure Service Health-tjänsten vet vilka tjänster och resurser du använder. Det bästa sättet att använda Service Health är genom att konfigurera Service Health-aviseringar som informerar dig om problem med tjänsten, planerat underhåll eller andra ändringar som kan påverka dina Azure-tjänster och -regioner via de kommunikationskanaler som du själv valt.

[Azure Resource Health](resource-health-overview.md) innehåller information om hälso tillståndet för dina enskilda moln resurser, till exempel en viss virtuell dator instans. Med hjälp av Azure Monitor kan du även konfigurera aviseringar som informerar dig om förändringar för dina molnresursers tillgänglighet. Genom att använda Azure Resource Health och Azure Monitor-aviseringar kan du hålla dig informerad om dina resursers tillgänglighet minut för minut och snabbt utvärdera om ett problem beror på något i ditt eget system eller om det uppstått på grund av en händelse på Azure-plattformen.

Sammantaget ger dessa upplevelser dig en heltäckande översikt över hälsotillståndet för Azure, med den precision som är mest relevant för dig.

**Titta på en översikt över sidan Azure-status, Azure Service Health och Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]