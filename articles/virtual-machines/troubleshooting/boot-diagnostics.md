---
title: Startdiagnostik för virtuella datorer i Azure | Microsoft-dokument
description: Översikt över två felsökningsfunktioner för virtuella datorer i Azure
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 64641f8acfe7b58763756e2a0707fa799ee804b2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414680"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Hur du använder startdiagnostik för felsökning av virtuella datorer i Azure

Stöd för två felsökningsfunktioner finns nu i Azure: Konsolutdata och skärmbild stöd för virtuella datorer i Azure Resource Manager-distributionsmodellen. 

När du använder en egen avbildning till Azure eller även starta en av plattformsavbildningar, kan det finnas många orsaker till varför en virtuell dator hamnar i ett icke startbart tillstånd. De här funktionerna kan du enkelt diagnostisera och återställa virtuella datorer vid startfel.

Du kan enkelt visa utdata från konsolloggen på portalen för virtuella Linux-datorer. För både Windows och Linux-datorer kan Azure du också se en skärmbild av den virtuella datorn från hypervisor-programmet. De här båda funktionerna har stöd för virtuella Azure-datorer i alla regioner. Tänk på att det kan ta upp till 10 minuter innan skärmbilder och utdata visas på lagringskontot.

## <a name="common-boot-errors"></a>Vanliga startfel

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Inget operativsystem hittades](https://support.microsoft.com/help/4010142)
- [Startfel eller INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Aktivera diagnostik på en ny virtuell dator
1. När du skapar en ny virtuell dator från Azure Portal väljer den **Azure Resource Manager** från distributionsmodeller:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. I **inställningar**, aktivera den **Startdiagnostik**, och välj sedan ett storage-konto som du vill placera de här diagnostikfilerna.
 
    ![Skapa en virtuell dator](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Funktionen Boot diagnostics har inte stöd för premium storage-konto. Om du använder premium-lagringskonto för startdiagnostik kan hända det StorageAccountTypeNotSupported-fel när du startar den virtuella datorn.
    >
    > 

3. Om du distribuerar en Azure Resource Manager-mall, navigera till den virtuella datorresursen och lägger till diagnostikprofilavsnittet. Kom ihåg att använda API-versionsrubriken 2015-06-15.

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

Om du vill distribuera en virtuell dator i exemplet med aktiverad startdiagnostik, Kolla in vår repo här.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivera startdiagnostik på befintlig virtuell dator 

Följ dessa steg om du vill aktivera startdiagnostik på en befintlig virtuell dator:

1. Logga in på den [Azure-portalen](https://portal.azure.com), och välj sedan den virtuella datorn.
2. I **Support och felsökning**väljer **Startdiagnostik** > **inställningar**, ändrar du statusen till **på**, och sedan Välj ett lagringskonto. 
4. Kontrollera att alternativet för start-diagnostik har valts och sedan spara ändringarna.

    ![Uppdatera befintlig virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Starta om den virtuella datorn så att ändringarna börjar gälla.


