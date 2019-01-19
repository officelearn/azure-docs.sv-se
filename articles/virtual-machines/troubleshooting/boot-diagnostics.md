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
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 55feef336ade461d4e3936d2f76122e5b4b4c00e
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401307"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Hur du använder startdiagnostik för felsökning av virtuella datorer i Azure

Det kan finnas många orsaker till att en virtuell dator försätts i ett icke startbart tillstånd. Att åtgärda problem med med dina virtuella datorer som skapats med hjälp av Resource Manager-distributionsmodell som du kan använda följande felsökningsfunktioner: Konsolutdata och skärmbild stöd för virtuella Azure-datorer. 

Du kan visa utdata från konsolloggen på portalen för virtuella Linux-datorer. För både Windows och Linux-datorer kan Azure du se en skärmbild av den virtuella datorn från hypervisor-programmet. Båda funktionerna har stöd för virtuella Azure-datorer i alla regioner. Tänk på att det kan ta upp till 10 minuter innan skärmbilder och utdata visas på lagringskontot.

Du kan välja den **Startdiagnostik** alternativ för att visa loggen och skärmbilden.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Aktivera diagnostik på en virtuell dator som skapats med hjälp av Azure Portal

Följande procedur är för en virtuell dator som skapats med hjälp av Resource Manager-distributionsmodellen.

På den **Management** fliken **övervakning** avsnittet, se till att **Startdiagnostik** är påslagen. Från den **diagnostiklagringskonto** listrutan väljer du ett lagringskonto där du vill placera diagnostikfilerna.
 
![Skapa en virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funktionen Boot diagnostics har inte stöd för premium storage-konto. Om du använder premium-lagringskonto för startdiagnostik kan hända det StorageAccountTypeNotSupported-fel när du startar den virtuella datorn.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Distribuera från en Azure Resource Manager-mall

Om du distribuerar en Azure Resource Manager-mall, navigera till den virtuella datorresursen och lägger till diagnostikprofilavsnittet. Ange API-versionsrubriken till ”2015-06-15” eller senare. Den senaste versionen är ”2018-10-01”.

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Via diagnostikprofilen kan du välja det lagringskonto där loggarna ska placeras.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Mer information om hur du distribuerar resurser med hjälp av mallar finns i [snabbstarten: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivera startdiagnostik på befintlig virtuell dator 

Följ dessa steg om du vill aktivera startdiagnostik på en befintlig virtuell dator:

1. Logga in på den [Azure-portalen](https://portal.azure.com), och välj sedan den virtuella datorn.
2. I den **Support och felsökning** väljer **Startdiagnostik**och välj sedan den **inställningar** fliken.
3. I **Startdiagnostik** inställningar, ändrar du statusen till **på**, och från den **lagringskonto** listrutan Välj ett lagringskonto. 
4. Spara ändringen.

    ![Uppdatera befintlig virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Du måste starta om den virtuella datorn för att ändringen ska börja gälla.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Aktivera startdiagnostik med Azure CLI

Du kan använda Azure CLI för att aktivera startdiagnostik på befintliga Azure-datorer. Mer information finns i [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
