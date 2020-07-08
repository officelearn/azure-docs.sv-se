---
title: Felsöka start fel i Azure Virtuella Linux-datorer | Microsoft Docs
description: Den här artikeln hjälper dig att länka till artiklar för att felsöka start fel i Azure Virtuella Linux-datorer.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74408747"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Felsöka fel vid start av Azure Virtuella Linux-datorer

Den här artikeln innehåller vanliga startfel som du kan få när du startar en virtuell Linux-dator (VM) i Microsoft Azure. Mer information om felen finns i artiklarna i avsnittet **om start fel och lösningar** .

## <a name="boot-errors-and-solutions"></a>Start fel och lösningar

* [GRUB räddning](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Nästa steg

- [Serie konsol för virtuell dator](serial-console-linux.md)

Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell återställnings dator med Azure:

- [Reparation av virtuella Azure-datorer](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk växling – detta kan automatiseras med hjälp av något av följande:
- [Skript för återställning av energi gränssnitt](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash-återställnings skript](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video om disk växling:

Om du inte har åtkomst till GRUB titta på [den här](https://youtu.be/m5t0GZ5oGAc) videon och se hur du enkelt kan automatisera disk växlings proceduren för att återställa den virtuella datorn

## <a name="unofficial-solution"></a>Inofficiell lösning

Återställningen av en virtuell dator kan också provas med BETA skriptet som inte stöds [ALAR](https://github.com/malachma/azure-auto-recover)