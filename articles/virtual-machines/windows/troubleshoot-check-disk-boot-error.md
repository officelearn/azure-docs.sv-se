---
title: Kontrollerar filsystemet när du startar en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du löser problemet att visa VM kontrollerar filsystemet vid start | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 71619bed63d5d53e6d24eac434ce5a696a445165
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345214"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows visar ”kontroll filsystem” när du startar en virtuell Azure-dator
Den här artikeln beskriver felet ”kontroll filsystem” du kan stöta på när du startar en Windows virtuell dator (VM) i Microsoft Azure.

> [!NOTE] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen.

## <a name="symptom"></a>Symtom 

En virtuell Windows-dator startar inte. När du checkar Start skärmbilderna [Startdiagnostik](./boot-diagnostics.md), du ser att kontrollera Disk-processen (chkdsk.exe) körs med något av följande meddelanden:

- Genomsöka och reparera enhet (C:)
- Filsystemet på C:

## <a name="cause"></a>Orsak

Om ett NTFS-fel hittas i filsystemet, kommer Windows kontrollera och reparera konsekvenskontroll av disken vid nästa omstart. Detta inträffar vanligtvis om den virtuella datorn har en oväntad omstart eller om processen för VM-avstängning avbröts tvärt.

## <a name="solution"></a>Lösning 

Windows startas normalt efter Kontrollera Disk-processen har slutförts. Om den virtuella datorn har fastnat i Kontrollera Disk-processen, försöker du köra Kontrollera-Disk på den virtuella datorn offline:
1.  Ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](snapshot-copy-managed-disk.md).
2.  [Koppla OS-disk till virtuell återställningsdator](.\troubleshoot-recovery-disks-portal.md).  
3.  På den Virtuella återställningsdatorn, kör du kontrollera Disk på den anslutna OS-disken. I följande exempel och är enhetsbokstaven för den anslutna OS-disken E: 
        
        chkdsk E: /f
4.  När det är klar att kontrollera-Disk, ta bort disken från den Virtuella återställningsdatorn och ansluta disken till den berörda virtuella datorn som en OS-disk på nytt. Mer information finns i [felsöka en virtuell Windows-dator genom att koppla OS-disk till virtuell återställningsdator](.\troubleshoot-recovery-disks-portal.md).
