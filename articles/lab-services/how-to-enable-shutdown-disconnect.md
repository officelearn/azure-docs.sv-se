---
title: Konfigurera automatisk avstängning av virtuella datorer för ett labb i Azure Lab Services
description: Lär dig hur du aktiverar eller inaktiverar automatisk avstängning av virtuella datorer när en anslutning till fjärr skrivbord är frånkopplad.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 67b51941207fdd4913df9a92362959bbd468d336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649889"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurera automatisk avstängning av virtuella datorer för ett labb

Den här artikeln visar hur du kan konfigurera automatisk avstängning av virtuella datorer för ett labb.

Du kan aktivera flera kostnads styrnings funktioner för automatisk avstängning för att proaktivt förhindra ytterligare kostnader när de virtuella datorerna inte används aktivt. Kombinationen av följande tre funktioner för automatisk avstängning och från koppling fångar upp de flesta fall där användare av misstag lämnar sina virtuella datorer som kör:
 
* Koppla automatiskt bort användare från virtuella datorer som operativ systemet bedömer inaktivt.
* Stäng virtuella datorer automatiskt när användarna kopplar från.
* Stäng av virtuella datorer som har startats automatiskt, men som inte ansluter till dem.

Granska mer information om funktionen för automatisk avstängning i avsnittet [maximera kostnads kontroll med inställningar för automatisk avstängning](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

En labb konto administratör kan konfigurera den här inställningen för labb kontot där du skapar labb. Mer information finns i [Konfigurera automatisk avstängning av virtuella datorer för ett labb konto](how-to-configure-lab-accounts.md). Som labb ägare kan du åsidosätta inställningen när du skapar ett labb eller när labbet har skapats. 

## <a name="configure-for-the-lab-level"></a>Konfigurera för labb nivån

Du kan konfigurera inställningen för automatisk avstängning i [Azure Lab Services](https://labs.azure.com/).

* När du skapar ett labb (i **labb principer**) eller
* När labbet har skapats (i **Inställningar**)

> [!div class="mx-imgBorder"]
> ![Konfigurera vid tidpunkten för att skapa labbet](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Se till att gå igenom informationen om automatisk avstängning i avsnittet [maximera kostnads kontroll med inställningar för automatisk avstängning](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

> [!WARNING]
> Om du stänger av Linux-eller Windows-operativsystemet på en virtuell dator innan du kopplar från en RDP-session till den virtuella datorn kommer funktionen för automatisk avstängning inte att fungera korrekt.  
## <a name="next-steps"></a>Nästa steg

[Instrument panel för klass rums labb](use-dashboard.md)
