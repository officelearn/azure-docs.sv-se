---
title: Felsöka startfel i Virtuella Azure Linux-datorer | Microsoft-dokument
description: Den här artikeln hjälper dig att länka till artiklar för att felsöka startfel i Virtuella Azure Linux-datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408747"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Felsöka startfel för Virtuella Azure-datorer

I den här artikeln visas de vanliga startfel som kan uppstå när du startar en virtuell Linux-dator (VM) i Microsoft Azure. Mer information om felen finns i artiklarna i avsnittet **Startfel och lösningar.**

## <a name="boot-errors-and-solutions"></a>Startfel och lösningar

* [GRUB räddning](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Nästa steg

- [Seriell konsol för virtuella datorer](serial-console-linux.md)

Felsöka en virtuell Linux-dator genom att koppla OS-disken till en återställnings-VM med Azure:

- [Azure VM-reparation](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk Swap - detta kan automatiseras med antingen:
- [Power Shell-återställningsskript](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash Recovery Skript](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure-portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video för diskbyte:

Om du inte har tillgång till GRUB titta på [den här](https://youtu.be/m5t0GZ5oGAc) videon och se, hur du enkelt kan automatisera disken swap förfarande för att återställa din VM

## <a name="unofficial-solution"></a>Inofficiell lösning

Återställning av en virtuell dator kan också göras med beta-skriptet som inte stöds [ALAR](https://github.com/malachma/azure-auto-recover)