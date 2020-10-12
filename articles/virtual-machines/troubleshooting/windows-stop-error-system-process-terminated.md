---
title: System processen har avslut ATS för Windows Stop Error-status
description: Den här artikeln innehåller steg för att lösa problem där operativ systemet påträffar stopp felet 0xC000021A, vilket förhindrar start av en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347769"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows Stop-fel-0xC000021A status system process avslutad

Den här artikeln innehåller steg för att lösa problem där operativ systemet (OS) påträffar stopp felet 0xC000021A, vilket gör att en virtuell Azure-dator (VM) startas.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att Visa skärm bilden för den virtuella datorn, visar skärm bilden meddelandet att operativ systemet påträffade ett fel under starten, med följande meddelande:

**Datorn stötte på ett problem och måste startas om. Vi har bara samlat in fel information och sedan kan du starta om. (# #% Complete) Om du vill veta mer kan du söka online senare efter det här felet: 0xC000021a**.

  ![Bild 1 visar felkoden #0xC000021A med meddelandet "datorn stötte på ett problem och måste startas om. Vi har bara samlat in fel information och sedan kan du starta om. ".](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Orsak

Fel 0xC000021A betyder **STATUS_SYSTEM_PROCESS_TERMINATED**.

Felet uppstår när en kritisk process, till exempel WinLogon (winlogon.exe) eller klient servern Run-Time under system (csrss.exe) Miss lyckas. När kärnan känner av att någon av dessa tjänster har stoppats genererar den **Stop 0xc000021a** -felet. Det här felet kan ha flera orsaker, inklusive:

- Felmatchade systemfiler har installerats.
- Det gick inte att installera en Service Pack-eller KB-uppdatering.
- Ett säkerhets kopierings program som används för att återställa en hård disk återställde inte korrekt filer som kanske används.
- Ett inkompatibelt program från tredje part har installerats.

## <a name="solution"></a>Lösning

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

För att lösa det här problemet måste krasch dumpning analyseras. Samla in minnesdumpen för krasch-och kontakta supporten. Samla in dumpfilen genom att följa dessa steg:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1.  Använd steg 1-3 i [reparations kommandona för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) för att förbereda en reparations-VM.
2.  Anslut till den virtuella reparations datorn med hjälp av **anslutning till fjärrskrivbord**.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1.  På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som är kopplad till den anslutna OS-disken är F går du till F:\Windows.
2.  Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.
3.  Om du har problem med att hitta Memory. dmp-filen kanske du vill använda [icke-maskbart avbrott (NMI) i serie konsolen i](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) stället. Du kan följa guiden för att [generera en kraschdump-fil med hjälp av NMI-anrop här](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Nästa steg

- Mer felsöknings information finns i [Felsöka vanliga start fel](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) eller [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator för återställning](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). Du bör också bekanta dig med [hur du använder startdiagnostik för att felsöka en virtuell dator](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
- Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).
- Om du inte kan ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
