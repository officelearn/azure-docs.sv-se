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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424438"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows Stop-fel – 0x0000007E system tråd undantag hanterades inte

Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet påträffar ett problem och vill starta om den virtuella Azure-datorn. Meddelandet kommer att ange att det inte gick att hantera ett undantags fel i system tråden.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar att Windows som behöver startas om med antingen stopp kod **systemets tråd undantag inte hanteras** eller felkoden **0x0000007E**.

![Skärm bilden visar Windows som fastnat under start med meddelandet: "datorn stötte på ett problem och måste startas om. Vi kommer att starta om åt dig. " Stoppkod: "SYSTEM tråd undantag HANTERAdes inte"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Orsak

Det går inte att fastställa orsaken till att en minnesdumpfil analyseras. Fortsätt att samla in minnesdumpen.

## <a name="solution"></a>Lösning

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
> [Felsöka start fel för virtuella Azure-datorer](./boot-error-troubleshoot.md)