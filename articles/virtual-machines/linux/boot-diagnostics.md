---
title: Starta diagnostik för virtuella Linux-datorer i Azure | Microsoft-dokument
description: Översikt över de två felsökning funktionerna för Linux virtuella datorer i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 0183da348a515787d9382df6db3df8524d584d93
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Hur du använder startdiagnostikinställningar för att felsöka Linux virtuella datorer i Azure

Nu finns stöd för två felsökningsfunktioner i Azure: konsolutdata och skärmbild för Azure Virtual Machines Resource Manager-distributionsmodellen. 

När du använder en egen avbildning i Azure eller till och med startar en av plattformsavbildningarna, kan det finnas många orsaker till att en virtuell dator övergår i tillståndet Ej startbar. Med de här funktionerna kan du enkelt diagnostisera och återställa virtuella datorer vid startfel.

För virtuella Linux-datorer kan du lätt visa utdata från konsolloggen på portalen:

![Azure Portal](./media/boot-diagnostics/screenshot1.png)
 
För både virtuella Windows- och Linux-datorer kan du dock även se en skärmbild av den virtuella datorn från hypervisor-program:

![Fel](./media/boot-diagnostics/screenshot2.png)

De här båda funktionerna finns för Azure Virtual Machines i alla regioner. Tänk på att det kan ta upp till 10 minuter innan skärmbilder och utdata visas på lagringskontot.

## <a name="common-boot-errors"></a>Vanliga startfel

- [Problem med filens system](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Kernel-problem](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB fel](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Aktivera diagnostik på en ny virtuell dator
1. När du skapar en ny virtuell dator från förhandsversionsportalen, väljer du **Azure Resource Manager** från listrutan med distributionsmodeller:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. Konfigurera övervakningsalternativet och välj det lagringskonto där du vill placera de här diagnostikfilerna.
 
    ![Skapa en virtuell dator](./media/boot-diagnostics/screenshot4.jpg)

3. Om du distribuerar från en Azure Resource Manager-mall går du till den virtuella datorresursen och lägger till diagnostikprofilavsnittet. Kom ihåg att använda API-versionsrubriken 2015-06-15.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Via diagnostikprofilen kan du välja det lagringskonto där loggarna ska placeras.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

## <a name="update-an-existing-virtual-machine"></a>Uppdatera en befintlig virtuell dator

Du kan uppdatera en befintlig virtuell dator via portalen om du vill aktivera startdiagnostikinställningar via portalen. Välj alternativet Startdiagnostik och Spara. Starta om den virtuella datorn så att ändringarna börjar gälla.

![Uppdatera befintlig virtuell dator](./media/boot-diagnostics/screenshot5.png)

## <a name="next-steps"></a>Nästa steg

Om du ser felet ”Det gick inte att hämta innehållet i loggen” när du använder VM Startdiagnostikinställningar finns [det gick inte att hämta innehållet i logga fel i VM-Startdiagnostikinställningar](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).