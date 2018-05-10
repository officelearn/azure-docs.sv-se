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
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
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
1. När du skapar en ny virtuell dator från Azure portal väljer den **Azure Resource Manager** distribution modellen listrutan:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. I **inställningar**, aktivera den **starta diagnostik**, och välj sedan ett lagringskonto som du vill att dessa diagnostiska filerna.
 
    ![Skapa en virtuell dator](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Start-diagnostik har inte stöd för premium-lagringskontot. Om du använder premium storage-konto för startdiagnostikinställningar kan du får felmeddelandet StorageAccountTypeNotSupported när du startar den virtuella datorn. 
    >
    > 

3. Om du distribuerar en Azure Resource Manager-mall, navigera till din virtuella datorresurser och Lägg till avsnittet diagnostics profil. Kom ihåg att använda API-versionsrubriken 2015-06-15.

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

Kolla våra lagringsplatsen här om du vill distribuera en virtuell dator i exemplet med startdiagnostikinställningar aktiverad.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivera startdiagnostikinställningar på befintlig virtuell dator 

Följ dessa steg om du vill aktivera startdiagnostikinställningar på en befintlig virtuell dator:

1. Logga in på den [Azure-portalen](https://portal.azure.com), och välj sedan den virtuella datorn.
2. I **stöd + felsökning**väljer **starta diagnostik** > **inställningar**, ändra status till **på**, och sedan Välj ett lagringskonto. 
4. Kontrollera att alternativet Start diagnostik är markerad och spara ändringen.

    ![Uppdatera befintlig virtuell dator](./media/boot-diagnostics/enable-for-existing-vm.png)

3. Starta om den virtuella datorn så att ändringarna börjar gälla.

## <a name="next-steps"></a>Nästa steg

Om du ser felet ”Det gick inte att hämta innehållet i loggen” när du använder VM Startdiagnostikinställningar finns [det gick inte att hämta innehållet i logga fel i VM-Startdiagnostikinställningar](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).