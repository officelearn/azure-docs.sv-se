---
title: Energi alternativ för Azures serie konsol | Microsoft Docs
description: Alternativ för virtuella dator energi tillgängliga i Azures serie konsol
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451206"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Energi alternativ som är tillgängliga från Azures serie konsol

Azures serie konsol innehåller flera kraftfulla verktyg för energispar funktioner på den virtuella datorn eller skalnings uppsättningen för virtuella datorer. Dessa alternativ för energispar funktioner kan vara förvirrande av vissa, så det här är en översikt över varje verktyg och dess avsedda användnings fall.

Funktion för serie konsol | Beskrivning | Användnings fall
:----------------------|:------------|:---------
Starta om virtuell dator | En korrekt omstart av den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans. Den här åtgärden är samma som när du anropar restart-funktionen som är tillgänglig på översikts sidan. | I de flesta fall bör det här alternativet vara ditt första verktyg för att försöka starta om den virtuella datorn. Din serie konsol anslutning får ett kort avbrott och återupptas automatiskt så fort den virtuella datorn har startats om.
Återställ virtuell dator | En Tvingad ström cykel för din virtuella dator eller skalnings uppsättning för virtuella datorer från Azure-plattformen. | Det här alternativet används för att omedelbart starta om operativ systemet oavsett dess aktuella tillstånd. Eftersom den här åtgärden inte är korrekt, finns det en risk för data förlust eller skada. Det finns inget avbrott i serie konsolens anslutning, vilket kan vara användbart för att skicka kommandon tidigt i Start tiden (till exempel att komma till GRUB på en virtuell Linux-dator eller fel säkert läge i en virtuell Windows-dator).
SysRq – omstart (b) | En systembegäran om att framtvinga en omstart av gästen. | Den här funktionen kan bara användas på Linux-operativsystem och kräver att [SysRq är aktiverat](./serial-console-nmi-sysrq.md#system-request-sysrq) i operativ systemet. Om operativ systemet är korrekt konfigurerat för SysRq kommer det här kommandot att starta om operativ systemet.
NMI (icke-maskerat avbrott) | Ett avbrotts kommando som skickas till operativ systemet | Den här åtgärden är tillgänglig för virtuella [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) -och [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) -datorer och kräver att NMI är aktiverat. Om du skickar en NMI kommer det vanligt vis orsaka att operativ systemet kraschar. Du kan konfigurera operativ systemet för att skapa en dumpfil och sedan starta om när du tar emot NMI, vilket kan vara användbart vid fel sökning på låg nivå.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azures serie konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Lär dig mer om [Azures serie konsol för virtuella Windows-datorer](./serial-console-windows.md)