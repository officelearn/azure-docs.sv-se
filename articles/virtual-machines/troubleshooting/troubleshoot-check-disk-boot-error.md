---
title: Kontrollera filsystemet vid start av en virtuell Azure-dator| Microsoft-dokument
description: Lär dig hur du löser problemet som vm visar Kontrollera filsystemet vid uppstart| Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921495"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows visar "kontrollera filsystem" vid start av en Virtuell Azure-dator

I den här artikeln beskrivs felet "Kontrollera filsystem" som kan uppstå när du startar en virtuell dator (Virtuell Dator) i Microsoft Azure.


## <a name="symptom"></a>Symptom 

En Virtuell Windows-dator startar inte. När du kontrollerar startskärmbilderna i [Startdiagnostiken](boot-diagnostics.md)ser du att checkdiskprocessen (chkdsk.exe) körs med något av följande meddelanden:

- Scanning och reparationsenhet (C:)
- Kontrollera filsystemet på C:

## <a name="cause"></a>Orsak

Om ett NTFS-fel hittas i filsystemet kontrollerar och repareras diskens konsekvens vid nästa omstart. Vanligtvis händer detta om den virtuella datorn hade någon oväntad omstart, eller om vm-avstängningen avbröts plötsligt.

## <a name="solution"></a>Lösning 

Windows startar normalt när checkdisken har slutförts. Om den virtuella datorn har fastnat i processen Kontrollera disken kan du försöka köra checkdisken på den virtuella datorn offline:
1.  Ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).
2.  [Koppla OS-disken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).  
3.  På återställningsdatorn kör du Check Disk på den anslutna OS-disken. I följande exempel är drivrutinsbrevet för den bifogade OS-disken E: 
        
        chkdsk E: /f
4.  När checkdisken är klar kopplar du bort disken från återställningsdatorn och ansluter sedan disken igen till den berörda virtuella datorn som en OS-disk. Mer information finns i [Felsöka en Virtuell Windows-dator genom att koppla OS-disken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).
