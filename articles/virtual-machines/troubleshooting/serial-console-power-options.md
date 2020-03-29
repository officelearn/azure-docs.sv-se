---
title: Energialternativ för Azure-seriekonsol | Microsoft-dokument
description: Alternativ för virtuell dator som är tillgängliga i Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451206"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Energialternativ tillgängliga från Azure Serial Console

Azure Serial Console innehåller flera kraftfulla verktyg för energisparfunktioner på din virtuella dator eller virtuella datorskalauppsättning. Dessa energisparalternativ kan vara förvirrande för vissa, så detta är en översikt över varje verktyg och dess avsedda användningsfall.

Seriell konsolfunktion | Beskrivning | Användningsfall
:----------------------|:------------|:---------
Starta om den virtuella datorn | En graciös omstart av din vm eller virtuell dator skalningsuppsättning instans. Den här åtgärden är samma sak som att anropa omstartsfunktionen som är tillgänglig på sidan Översikt. | I de flesta fall bör det här alternativet vara det första verktyget för att försöka starta om den virtuella datorn. Din serialkonsolanslutning kommer att få ett kort avbrott och återupptas automatiskt så snart den virtuella datorn har startats om.
Återställ virtuell dator | En kraftfull kraftcykel för din virtuella dator eller virtuella datorskala som fastställts av Azure-plattformen. | Det här alternativet används för att omedelbart starta om operativsystemet oavsett dess aktuella tillstånd. Eftersom den här åtgärden inte är graciös finns det risk för dataförlust eller korruption. Det finns inget avbrott i Serial Console-anslutningen, vilket kan vara användbart för att skicka kommandon tidigt i starttiden (till exempel att komma till GRUB på en Virtuell Linux- eller felsäkert läge i en Windows-vm).
SysRq - Omstart (b) | En systembegäran om att tvinga en gäst omstart. | Den här funktionen är endast tillämplig på Linux-operativsystem och kräver [att SysRq aktiveras](./serial-console-nmi-sysrq.md#system-request-sysrq) i operativsystemet. Om operativsystemet är korrekt konfigurerat för SysRq kommer det här kommandot att leda till att operativsystemet startas om.
NMI (Icke-maskerbart avbrott) | Ett avbrottskommando som levereras till operativsystemet | Den här åtgärden är tillgänglig för både [Virtuella Datorer](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) i Windows och [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) och kräver att NMI aktiveras. Om du skickar en NMI kraschar det vanligtvis. Du kan konfigurera operativsystemet för att skapa en dumpfil och sedan starta om när du tar emot NMI, vilket kan vara användbart vid felsökning på låg nivå.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [virtuella Azure-konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Läs mer om [virtuella Azure-seriekonsolen för Virtuella Datorer i Windows](./serial-console-windows.md)