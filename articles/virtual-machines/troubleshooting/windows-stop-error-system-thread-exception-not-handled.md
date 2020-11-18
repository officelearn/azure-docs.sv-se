---
title: Windows Stop-fel – 0x0000007E system tråd undantag hanterades inte
description: Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet påträffar ett problem och vill starta om den virtuella Azure-datorn. Meddelandet kommer att ange att det inte gick att hantera ett undantags fel i system tråden.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681898"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows Stop-fel – 0x0000007E system tråd undantag hanterades inte

Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet (gäst operativ system) påträffar ett problem och försöker starta om din virtuella Azure-dator (VM). Det fel meddelande som visas säger "ett undantag för system tråden hanterades inte."

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att visa en skärm bild av den virtuella datorns utdata, ser du att Windows måste startas om med antingen stopp kod för "system TRÅDens undantags fel" eller "0x0000007E".

![Skärm bild som visar Windows som fastnat under start med en "din dator stötte på ett problem och måste startas om. Vi kommer att starta om åt dig. " fel meddelandet och stopp koden "SYSTEM tråd undantag HANTERAdes inte".](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Orsak

Det går inte att fastställa orsaken till att en minnesdumpfil analyseras. Fortsätt att samla in minnesdumpen.

## <a name="solution"></a>Lösning

För att lösa det här problemet måste du först samla in minnesdumpen för kraschen och sedan skicka filen till Microsoft support. Samla in dumpfilen genom att följa anvisningarna i följande två avsnitt.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Om du vill förbereda en virtuell reparations dator följer du steg 1-3 i [reparations kommandon för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Anslut till den virtuella reparations datorn med hjälp av Anslutning till fjärrskrivbord.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1. På den virtuella reparations datorn går du till Windows-mappen på den anslutna OS-disken. Till exempel, om enhets beteckningen som är tilldelad till den anslutna OS-disken heter *F*, går du till `F:\Windows` .
1. Leta efter *Memory. dmp* -filen och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnes dumpnings filen ansluten.
1. Om du har problem med att hitta *Memory. dmp* -filen följer du guiden för att [Generera en kraschdumpfil med ett icke-maskbart avbrotts anrop (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Mer information om NMI-anrop finns i NMI-anrop i användar handboken för [Azures serie konsol](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsöka start fel för virtuella Azure-datorer](./boot-error-troubleshoot.md)
