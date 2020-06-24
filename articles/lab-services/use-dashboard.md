---
title: Använd instrument panelen för ett klass rums labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du använder instrument panelen för ett klass rums labb i Azure Lab Services.
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
ms.openlocfilehash: 7a4c4adcd83e4a4781767cd3c34216dca2b4d510
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897998"
---
# <a name="dashboard-for-classroom-labs"></a>Instrument panel för klass rums labb
I den här artikeln beskrivs vyn instrument panel för ett klass rums labb i Azure Lab Services. 

![Instrumentpanel](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Kostnads-och fakturerings panel
Den här panelen innehåller följande information om kostnads uppskattning:

| Inställningen | Värde | 
| ------- | ----- | 
| Kvot timmar | Det maximala antalet timmar som en användare kan använda den virtuella datorn utanför de schemalagda timmarna. |
| Schemalagda timmar | Timmar som kommer att skapas baserat på schemat som angetts i labbet. Det här värdet är bara tillgängligt om det finns ett från/till-datum som anges för alla schema händelser. |
| Timmar/användare | Summan av kvot timmar och schemalagda timmar. |
| Maximalt antal användare | Maximalt antal användare i labbet som baseras på alla virtuella datorer som ska begäras. |
| Timmar x användare | Timmar/användare multiplicerat med antalet användare. |
| Justerad kvot | Summan av de kvot timmar som har lagts till för vissa användare. |
| Totalt antal timmar * $/timme | Kostnaden per timme baserat på den virtuella dator storleken som valts. Detta baseras på det normala priset för att betala per användning. |
| Total uppskattad kostnad | Detta är det maximala priset för det här labbet baserat på de aktuella inställningarna. |

## <a name="template-tile"></a>Mall-panelen
Följande information visas på den här panelen:

- Det datum då mallen skapades 
- Det datum då mallen senast publicerades 

Det finns också en länk för att navigera till **mallsidan** där du kan [Hantera mallen VM](how-to-create-manage-template.md) för klassen. 

## <a name="virtual-machine-pool-tile"></a>Panel för virtuell dator pool

Följande information visas på den här panelen:

- Antal virtuella datorer som har tilldelats studenter (användare)
- Antal virtuella datorer som ännu inte har tilldelats till studenter

Det finns också en länk för att navigera till sidan för **poolen för virtuella** datorer där du kan [Hantera poolen med virtuella datorer](how-to-set-virtual-machine-passwords.md) i labbet. 

## <a name="users-tile"></a>Panelen användare

Följande information visas på den här panelen:

- Antal användare som har registrerats för klassen
- Antal användare som har lagts till i labbet men inte registrerats för klassen 

Det finns också en länk för att navigera till sidan **användare** där du kan [Hantera användare](how-to-configure-student-usage.md) för labbet. 

## <a name="schedules-tile"></a>Panelen scheman
Du ser de aktuella schemalagda händelserna för labbet i panelen. Det finns också en länk för att navigera till sidan **schema** där du kan [skapa och hantera scheman](how-to-create-schedules.md). Panelen visar bara information om två schemalagda händelser och antalet återstående schemalagda händelser för labbet. 

![Schemalagda händelser](./media/use-dashboard/scheduled-events.png)

