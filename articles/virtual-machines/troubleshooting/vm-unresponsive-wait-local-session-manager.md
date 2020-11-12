---
title: Den virtuella datorn svarar inte på den lokala sessionshanteraren
description: Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet fastnar i väntan på att den lokala sessionshanteraren ska slutföra bearbetningen när en virtuell Azure-dator startas.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536242"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>Den virtuella datorn svarar inte på den lokala sessionshanteraren

Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet fastnar i väntan på att den lokala sessionshanteraren ska slutföra bearbetningen när en virtuell Azure-dator startas.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar en fråga med meddelandet: " *vänta på den lokala sessionshanteraren* "

![Skärm bilden visar gäst operativ systemet som fastnat i meddelandet "vänta på den lokala sessionshanteraren" i Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Orsak

Det kan finnas flera orsaker till att en virtuell dator fastnar i väntan på den lokala sessionshanteraren. Om du väntar på att den lokala sessionshanteraren är ett beständigt problem måste du samla in en minnesdump för analys.

## <a name="solution"></a>Lösning

I vissa fall behöver du bara vänta tillräckligt länge för att processen ska slutföras för att lösa problemet. Om din virtuella dator inte svarar och är kvar på skärmen vänta i mer än en timme, ska du samla in en minnesdump och sedan kontakta Microsoft support.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

För att lösa det här problemet måste du först samla in minnesdumpen för kraschen och sedan kontakta supporten med minnesdumpen. Samla in dumpfilen genom att följa dessa steg:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Förbered en virtuell reparations dator genom att följa [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) .
2. Anslut till den reparerade virtuella datorn med Anslutning till fjärrskrivbord.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1. På den virtuella reparations datorn går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som tilldelas till den anslutna OS-disken är märkt som *F* måste du gå till `F:\Windows` .
2. Leta upp filen **Memory. dmp** och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.
3. Om du har problem med att hitta **Memory. dmp** -filen följer du guiden för att [Generera en kraschdumpfil med hjälp av NMI-anrop (non-maskbart Interrupt)](/windows/client-management/generate-kernel-or-complete-crash-dump).

För ytterligare information om NMI-anrop, se [anropen för icke-maskerade avbrott (NMI) i serie konsolens](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) Användar handbok.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsöka start fel för virtuella Azure-datorer](boot-error-troubleshoot.md)