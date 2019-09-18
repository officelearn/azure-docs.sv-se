---
title: Kontrollerar fil systemet vid start av en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du löser problemet som den virtuella datorn visar kontrollerar fil systemet vid start av | Microsoft Docs
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
ms.openlocfilehash: 839803c8cf54a85946e7134fdb6fe6be2da3dc4c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058085"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows visar "kontrollerar fil system" när en virtuell Azure-dator startas

I den här artikeln beskrivs fel meddelandet "kontrollerar fil systemet" som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure.

> [!NOTE] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder distributions modellen för Resource Manager, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributions modellen.

## <a name="symptom"></a>Symtom 

En virtuell Windows-dator startar inte. När du kontrollerar start skärmarna i [startdiagnostik](boot-diagnostics.md), ser du att processen för att kontrol lera disken (chkdsk. exe) körs med något av följande meddelanden:

- Genomsöker och reparerar enhet (C:)
- Kontrollerar fil system på C:

## <a name="cause"></a>Orsak

Om ett NTFS-fel påträffas i fil systemet kontrollerar Windows och reparerar konsekvensen för disken vid nästa omstart. Detta inträffar vanligt vis om den virtuella datorn hade oväntad omstart eller om den virtuella dator stängnings processen avbröts plötsligt.

## <a name="solution"></a>Lösning 

Windows startar normalt när processen för att kontrol lera disken har slutförts. Om den virtuella datorn har fastnat i kontrol lera disk-processen försöker du köra check disken på den virtuella datorn offline:
1.  Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).
2.  [Koppla OS-disk till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).  
3.  Kör check disk på den anslutna OS-disken på den virtuella återställnings datorn. I följande exempel är driv rutins beteckningen för den anslutna OS-disken E: 
        
        chkdsk E: /f
4.  När kontrollen är klar kopplar du bort disken från den virtuella återställnings datorn och kopplar sedan disken till den berörda virtuella datorn som en operativ system disk. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator för återställning](troubleshoot-recovery-disks-portal-windows.md).
