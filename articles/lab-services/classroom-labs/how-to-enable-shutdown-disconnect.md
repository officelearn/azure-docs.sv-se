---
title: Aktivera avstängning av virtuella datorer vid frånkoppling av Azure Lab Services | Microsoft-dokument
description: Lär dig hur du aktiverar eller inaktiverar automatisk avstängning av virtuella datorer när en fjärrskrivbordsanslutning kopplas från.
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
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117130"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Aktivera automatisk avstängning av virtuella datorer vid frånkoppling
Den här artikeln visar hur du kan aktivera eller inaktivera automatisk avstängning av virtuella datorer med **Windows 10-labb** (mall eller student) när en fjärrskrivbordsanslutning har kopplats från. Du kan också ange hur länge de virtuella datorerna ska vänta på att användaren ska återansluta innan den stängs av automatiskt.

En labbkontoadministratör kan konfigurera den här inställningen för labbkontot där du skapar labb. Mer information finns i [Konfigurera automatisk avstängning av virtuella datorer vid frånkoppling för ett labbkonto](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Som labbägare kan du åsidosätta inställningen när du skapar ett labb eller efter att labbet har skapats. 

## <a name="configure-when-creating-a-lab"></a>Konfigurera när du skapar ett labb
På steg 3-sidan i guiden skapa labb kan du aktivera eller inaktivera den här funktionen och även ange hur länge den virtuella datorn ska vänta på att användaren ska återansluta innan den stängs av automatiskt. 

![Konfigurera vid tidpunkten för skapandet av labbet](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats
Du kan konfigurera den här inställningen på sidan **Inställningar** enligt följande bild: 

![Konfigurera när labbet har skapats](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Om du stänger av Operativsystemet Windows (OS) på en virtuell dator innan du kopplar från en RDP-session till den virtuella datorn, fungerar inte funktionen för automatisk nedskalning korrekt.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Instrumentpanel för klassrumslabb](use-dashboard.md)