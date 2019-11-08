---
title: Reparera en virtuell Windows-dator med hjälp av reparations kommandon för virtuell Azure-dator | Microsoft Docs
description: Den här artikeln beskriver hur du använder reparations kommandon för virtuella Azure-datorer för att ansluta disken till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 6bda8cb831e84a56c889ed40109954551a34c113
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796164"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparera en virtuell Windows-dator med hjälp av reparationskommandon för virtuella Azure-datorer

Om din virtuella Windows-dator (VM) i Azure påträffar ett start-eller diskfel kan du behöva åtgärda problemet på själva disken. Ett vanligt exempel är en misslyckad program uppdatering som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder reparations kommandon för virtuella Azure-datorer för att ansluta disken till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.

> [!IMPORTANT]
> Skripten i den här artikeln gäller bara för de virtuella datorer som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Översikt över reparations processen

Nu kan du använda Azures reparations kommandon för virtuella datorer för att ändra OS-disk för en virtuell dator, och du behöver inte längre ta bort och återskapa den virtuella datorn.

Följ dessa steg för att felsöka problemet med den virtuella datorn:

1. Starta Azure Cloud Shell
2. Kör AZ-tillägget Lägg till/uppdatera.
3. Kör AZ VM Repair Create.
4. Kör AZ VM Repair Run.
5. Kör AZ VM Repair Restore.

Mer dokumentation och instruktioner finns i [AZ VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exempel på reparations processen

> [!NOTE]
> * Utgående anslutningar från den virtuella datorn (port 443) krävs för att skriptet ska kunna köras.
> * Det går bara att köra ett skript i taget.
> * Ett skript som körs kan inte avbrytas.
> * Den längsta tid som ett skript kan köras är 90 minuter, och det kommer att ta slut.

1. Starta Azure Cloud Shell

   Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den innehåller vanliga Azure-verktyg förinstallerade och konfigurerade för användning med ditt konto.

   Om du vill öppna Cloud Shell väljer du **testa den** i det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell på en separat flik i webbläsaren genom att besöka [https://shell.azure.com](https://shell.azure.com).

   Välj **Kopiera** för att kopiera kod blocken, klistra in koden i Cloud Shell och välj **RETUR** för att köra den.

   Om du föredrar att installera och använda detta CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare. Kör ``az --version`` för att hitta versionen. Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Om detta är första gången du har använt `az vm repair`-kommandon, lägger du till tillägget VM-Repair CLI.

   ```azurepowershell-interactive
   az extension add -n vm-repair
   ```

   Om du tidigare har använt de `az vm repair`-kommandona, installerar du eventuella uppdateringar i tillägget VM-Repair.

   ```azurepowershell-interactive
   az extension update -n vm-repair
   ```

3. Kör `az vm repair create`. Det här kommandot skapar en kopia av OS-disken för den icke-funktionella virtuella datorn, skapar en virtuell reparations dator och ansluter disken.

   ```azurepowershell-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Kör `az vm repair run`. Det här kommandot kommer att köra det angivna reparations skriptet på den anslutna disken via den virtuella reparations datorn.

   ```azurepowershell-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Kör `az vm repair restore`. Det här kommandot byter ut den reparerade OS-disken mot den ursprungliga OS-disken för den virtuella datorn.

   ```azurepowershell-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verifiera och aktivera startdiagnostik

I följande exempel aktive ras Diagnostic-tillägget på den virtuella datorn med namnet ``myVMDeployed`` i resurs gruppen med namnet ``myResourceGroup``:

Azure CLI

```azurepowershell-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Nästa steg

* Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
