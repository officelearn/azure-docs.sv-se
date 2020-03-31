---
title: Använda instrumentpanelen för ett klassrumslabb i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du använder instrumentpanelen för ett klassrumslabb i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538948"
---
# <a name="dashboard-for-classroom-labs"></a>Instrumentpanel för klassrumslabb
I den här artikeln beskrivs instrumentpanelsvyn för ett klassrumslabb i Azure Lab Services. 

![Instrumentpanel](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Panel för kostnader och fakturering
Den här panelen innehåller följande information om kostnadsuppskattning:

| Inställning | Värde | 
| ------- | ----- | 
| Kvottimmar | Det maximala antalet timmar en användare kan använda den virtuella datorn utanför de schemalagda timmarna. |
| Schemalagda timmar | Timmar som kommer att uppstå baserat på schemat som anges i labbet. Det här värdet är endast tillgängligt om det finns ett från/till-datum som anges på alla schemahändelser. |
| Timmar/användare | Summan av kvottimmar och schemalagda timmar. |
| Maximalt antal användare | Maximalt antal användare i labbet baserat på alla virtuella datorer som ska begäras. |
| Timmar x användare | Timmar/användare multiplicerat med antalet användare. |
| Justerad kvot | Summan av kvottimmarna som läggs till för specifika användare. |
| Totalt antal timmar * $/timme | Kostnaden per timme baserat på den valda vm-storleken. Detta är baserat på den vanliga lönen som du går pris. |
| Total beräknad kostnad | Detta är det högsta priset för det här labbet baserat på aktuella inställningar. |

## <a name="template-tile"></a>Mallpanel
Följande information om den här panelen visas:

- Det datum då mallen skapades 
- Det datum då mallen senast publicerades 

Den har också en länk för att navigera till **mallsidan** där du kan [hantera mallen VM](how-to-create-manage-template.md) för klassen. 

## <a name="virtual-machine-pool-tile"></a>Poolpanel för virtuell dator

Följande information om den här panelen visas:

- Antal virtuella datorer som har tilldelats deltagare (användare)
- Antal virtuella datorer som inte har tilldelats deltagare ännu

Den har också en länk för att navigera till **den virtuella datorn pool** sida där du kan hantera poolen av virtuella [datorer](how-to-set-virtual-machine-passwords.md) i labbet. 

## <a name="users-tile"></a>Panel för användare

Följande information om den här panelen visas:

- Antal användare som är registrerade i klassen
- Antal användare som läggs till i labbet men inte är registrerade i klassen 

Den har också en länk för att navigera till **sidan Användare** där du kan [hantera användare](how-to-configure-student-usage.md) för labbet. 

## <a name="schedules-tile"></a>Scheman kakel
Du ser de aktuella schemalagda händelserna för labbet på panelen. Den har också en länk för att navigera till **sidan Schema där** du kan skapa och hantera [scheman](how-to-create-schedules.md). Panelen visar information om endast två schemalagda händelser och antalet återstående schemalagda händelser för labbet. 

![Schemalagda händelser](../media/use-dashboard/scheduled-events.png)

