---
title: Aktivera avstängning av virtuella datorer vid från koppling Azure Lab Services | Microsoft Docs
description: Lär dig hur du aktiverar eller inaktiverar automatisk avstängning av virtuella datorer när en anslutning till fjärr skrivbord är frånkopplad.
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: e615170952ea2987639a0bfc269ad5a1692e1e59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480806"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Aktivera automatisk avstängning av virtuella datorer vid från koppling
Den här artikeln visar hur du kan aktivera eller inaktivera automatisk avstängning av **Windows 10** Lab-VM: ar (mall eller student) när en anslutning till fjärr skrivbord är frånkopplad. Du kan också ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt.

En labb konto administratör kan konfigurera den här inställningen för labb kontot där du skapar labb. Mer information finns i [Konfigurera automatisk avstängning av virtuella datorer vid från koppling för ett labb konto](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Som labb ägare kan du åsidosätta inställningen när du skapar ett labb eller när labbet har skapats. 

## <a name="configure-when-creating-a-lab"></a>Konfigurera när du skapar ett labb
På sidan steg 3 i guiden Skapa labb kan du aktivera eller inaktivera den här funktionen och även ange hur länge den virtuella datorn ska vänta tills användaren ansluter igen innan den stängs av automatiskt. 

![Konfigurera vid tidpunkten för att skapa labbet](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats
Du kan konfigurera den här inställningen på sidan **Inställningar** , som du ser i följande bild: 

![Konfigurera när labbet har skapats](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Instrument panel för klass rums labb](use-dashboard.md)