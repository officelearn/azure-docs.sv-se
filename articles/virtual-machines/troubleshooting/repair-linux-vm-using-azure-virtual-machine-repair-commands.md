---
title: Reparera en virtuell Linux-dator med hjälp av azure virtual machine-reparationskommandona | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Azure Virtual Machine-reparationskommandon för att ansluta disken till en annan Virtuell Linux-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 49fdfde402938ce8d0ee1b141a47e68c99c502e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796203"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparera en virtuell Linux-dator med hjälp av reparationskommandon för virtuella Azure-datorer

Om din virtuella Linux-dator (VM) i Azure stöter på ett start- eller diskfel kan du behöva utföra begränsning på själva disken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar att den virtuella datorn kan starta. I den här artikeln beskrivs hur du använder Azure Virtual Machine-reparationskommandon för att ansluta disken till en annan Virtuell Linux-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.

> [!IMPORTANT]
> Skripten i den här artikeln gäller endast för de virtuella datorer som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Översikt över reparationsprocessen

Du kan nu använda Azure Virtual Machine-reparationskommandon för att ändra OS-disken för en virtuell dator, och du behöver inte längre ta bort och återskapa den virtuella datorn.

Så här felsöker du problemet med den virtuella datorn:

1. Starta Azure Cloud Shell
2. Kör az-tilläggstillägg
3. Kör az vm reparation skapa
4. Utföra begränsningssteg
5. Kör återställning av az vm-reparation

Mer dokumentation och instruktioner finns i [az vm-reparation](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exempel på reparationsprocess

1. Starta Azure Cloud Shell

   Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den innehåller vanliga Azure-verktyg förinstallerade och konfigurerade att använda med ditt konto.

   Om du vill öppna Cloud Shell väljer du **Prova det** i det övre högra hörnet av ett kodblock. Du kan också öppna Cloud Shell i [https://shell.azure.com](https://shell.azure.com)en separat webbläsarflik genom att gå till .

   Välj **Kopiera** om du vill kopiera kodblocken, klistra sedan in koden i molnskalet och välj **Retur** för att köra den.

   Om du föredrar att installera och använda detta CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare. Kör ``az --version`` för att hitta versionen. Om du behöver installera eller uppgradera din Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Om det är första gången `az vm repair` du använder kommandona lägger du till CLI-tillägget för VM-reparation.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Om du tidigare `az vm repair` har använt kommandona installerar du eventuella uppdateringar på tillägget för vm-reparation.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Kör `az vm repair create`. Det här kommandot skapar en kopia av OS-disken för den icke-funktionella virtuella datorn, skapar en virtuell reparations-VM och ansluter disken.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Utför alla nödvändiga begränsningssteg på den skapade reparationsdatorn och fortsätt sedan till steg 5.

5. Kör `az vm repair restore`. Det här kommandot byter den reparerade OS-disken mot den ursprungliga OS-disken på den virtuella datorn.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verifiera och aktivera startdiagnostik

I följande exempel kan diagnostiktillägget ``myVMDeployed`` på den virtuella ``myResourceGroup``datorn som nämns i resursgruppen :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Nästa steg

* Om du har problem med att ansluta till den virtuella datorn läser [du Felsöka RDP-anslutningar till en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Problem med åtkomst till program som körs på den virtuella datorn finns i [Felsöka problem med programanslutning på virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Mer information om hur du använder Resource Manager finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
