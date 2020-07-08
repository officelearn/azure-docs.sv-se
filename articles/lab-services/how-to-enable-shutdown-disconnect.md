---
title: Aktivera avstängning av virtuella datorer vid från koppling Azure Lab Services | Microsoft Docs
description: Lär dig hur du aktiverar eller inaktiverar automatisk avstängning av virtuella datorer när en anslutning till fjärr skrivbord är frånkopplad.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445720"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Aktivera automatisk avstängning av virtuella datorer vid från koppling
Den här artikeln visar hur du kan aktivera eller inaktivera automatisk avstängning av **Windows 10** Lab-VM: ar (mall eller student) när en anslutning till fjärr skrivbord är frånkopplad. Du kan också ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt.

En labb konto administratör kan konfigurera den här inställningen för labb kontot där du skapar labb. Mer information finns i [Konfigurera automatisk avstängning av virtuella datorer vid från koppling för ett labb konto](how-to-configure-lab-accounts.md). Som labb ägare kan du åsidosätta inställningen när du skapar ett labb eller när labbet har skapats. 

## <a name="configure-when-creating-a-lab"></a>Konfigurera när du skapar ett labb
På sidan steg 3 i guiden Skapa labb kan du aktivera eller inaktivera den här funktionen och även ange hur länge den virtuella datorn ska vänta tills användaren ansluter igen innan den stängs av automatiskt. 

![Konfigurera vid tidpunkten för att skapa labbet](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats
Du kan konfigurera den här inställningen på sidan **Inställningar** , som du ser i följande bild: 

![Konfigurera när labbet har skapats](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Om du stänger av Windows operativ system (OS) på en virtuell dator innan du kopplar från en RDP-session till den virtuella datorn kommer funktionen för automatisk avstängning inte att fungera korrekt.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Instrument panel för klass rums labb](use-dashboard.md)