---
title: Startdiagnostik för virtuella datorer i Azure | Microsoft-dokument
description: Översikt över de två felsökningsfunktionerna för virtuella datorer i Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: fe2427d008b49daa6222ca981994f0dc2fbea355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476594"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Så här använder du startdiagnostik för att felsöka virtuella datorer i Azure

Det kan finnas många orsaker till att en virtuell dator går in i ett icke-startbart tillstånd. Om du vill lösa problem med dina virtuella datorer som skapats med hjälp av Resource Manager-distributionsmodellen kan du använda följande felsökningsfunktioner: Stöd för konsolutdata och skärmbild för virtuella Azure-datorer. 

För virtuella Linux-datorer kan du visa utdata från konsolloggen från portalen. För både virtuella Windows- och Linux-datorer kan du med Azure se en skärmbild av den virtuella datorn från hypervisorn. Båda funktionerna stöds för virtuella Azure-datorer i alla regioner. Det kan ta upp till 10 minuter innan skärmbilder och utdata visas i ditt lagringskonto.

Du kan välja alternativet **Starta diagnostik** för att visa loggen och skärmbilden.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Aktivera diagnostik på en virtuell dator som skapats med Azure Portal

Följande procedur gäller för en virtuell dator som skapats med hjälp av resurshanterarens distributionsmodell.

Kontrollera att **startdiagnostik** är aktiverat i avsnittet **Övervakning** på fliken **Hantering.** Välj ett **lagringskonto** där diagnostikfilerna ska lagras i listrutan Diagnostikkonto.
 
![Skapa en virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funktionen Startdiagnostik stöder inte premiumlagringskonto. Om du använder premiumlagringskontot för startdiagnostik kan felet StorageAccountTypeNotSupported visas när du startar den virtuella datorn.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Distribuera från en Azure Resource Manager-mall

Om du distribuerar från en Azure Resource Manager-mall navigerar du till resursen för den virtuella datorn och lägger till avsnittet diagnostikprofil. Ange API-versionshuvudet till "2015-06-15" eller senare. Den senaste versionen är "2018-10-01".

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

Mer information om hur du distribuerar resurser med hjälp av mallar finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivera startdiagnostik på befintlig virtuell dator 

Så här aktiverar du startdiagnostik på en befintlig virtuell dator:

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj sedan den virtuella datorn.
2. I avsnittet **Support + felsökning** väljer du Starta **diagnostik**och väljer sedan fliken **Inställningar.**
3. I **Inställningar för Startdiagnostik** ändrar du status till **På**och välj ett lagringskonto i listrutan **Lagringskonto.** 
4. Spara ändringen.

    ![Uppdatera befintlig virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Du måste starta om den virtuella datorn för att ändringen ska börja gälla.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Aktivera startdiagnostik med Azure CLI

Du kan använda Azure CLI för att aktivera startdiagnostik på en befintlig virtuell Azure-dator. Mer information finns i [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
