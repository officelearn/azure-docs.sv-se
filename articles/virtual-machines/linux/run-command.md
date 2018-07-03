---
title: Köra shell-skript i en Linux-VM på Azure
description: Det här avsnittet beskriver hur du kör skript i en Azure Linux-dator som kör kommandot
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 850c5ac4df8ff3bd0e35567060b3b90dad7baacc
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342699"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Köra shell-skript i Linux-VM med Kör kommando

Kör kommandot använder VM-agenten för att köra shell-skript i en virtuell Linux-dator. Skripten kan användas för allmän dator eller hantering av program och kan användas för att snabbt diagnostisera och åtgärda problem med åtkomst och nätverk av virtuell dator och få den virtuella datorn till ett fungerande tillstånd.

## <a name="benefits"></a>Fördelar

Det finns flera alternativ som kan användas för att få åtkomst till dina virtuella datorer. Kör kommandot kan köra skript på dina virtuella datorer via fjärranslutning med VM-agenten. Kör kommandot kan användas via Azure-portalen [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), eller [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Den här funktionen är användbar i samtliga scenarier där du vill köra ett skript i en virtuella datorer och är ett av de enda sätten att felsöka och åtgärda en virtuell dator som inte har RDP eller SSH-porten som är öppna på grund av felaktig nätverks- eller administrativ användare konfiguration.

## <a name="restrictions"></a>Begränsningar

Här följer en lista över begränsningar som finns när du använder kör kommandot.

* Utdata är begränsad till den senaste 4096 byte
* Minsta tid att köra ett skript ungefär 20 sekunder
* Skript som körs som standard som förhöjd på Linux
* Köra ett skript i taget
* Skript som efterfrågar information (interaktivt läge) stöds inte.
* Du kan inte avbryta en som kör skript
* Den längsta tid som ett skript kan köras är 90 minuter, når tidsgränsen efter som den
* Utgående anslutning från den virtuella datorn krävs för att returnera resultatet av skriptet.

## <a name="azure-cli"></a>Azure CLI

Följande är ett exempel med hjälp av den [kommandot med az vm kör](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) kommando för att köra ett kommandoskript som kör på en virtuell Linux-dator.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Du kan använda för att köra kommandon som en annan användare, `sudo -u` att ange ett användarkonto som du använder.

## <a name="azure-portal"></a>Azure Portal

Navigera till en virtuell dator i [Azure](https://portal.azure.com) och välj **Körningskommando** under **OPERATIONS**. Visas en lista över tillgängliga kommandon ska köras på den virtuella datorn.

![Kör kommandolista](./media/run-command/run-command-list.png)

Välj ett kommando för att köra. Några av kommandona som kan ha valfritt eller obligatoriska indataparametrar. Parametrarna presenteras för de kommandona som textfält som du kan ange värdena som indata. För varje kommando som du kan visa det skript som körs genom att expandera **Visa skript**. **RunShellScript** skiljer sig från de andra kommandon som du kan ange ett eget skript. 

> [!NOTE]
> De inbyggda kommandona som kan inte redigeras.

När kommandot har valts klickar du på **kör** att köra skriptet. Skriptet körs och när du är färdig returnerar utdata och eventuella fel i utdatafönstret. Följande skärmbild visar ett exempel på utdata från att köras i **ifconfig** kommando.

![Kör skriptet kommandoutdata](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen visas listan över kommandon som är tillgängliga för virtuella Linux-datorer. Den **RunShellScript** kommando kan användas för att köra alla anpassade skript som du vill.

|**Namn**|**Beskrivning**|
|---|---|
|**RunShellScript**|Kör ett Linux-kommandoskript.|
|**ifconfig**| Läsa konfigurationen för alla nätverksgränssnitt.|

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomsten till kommandot Kör

Lista kör kommandon eller som visar information om ett kommando kräver den `Microsoft.Compute/locations/runCommands/read` behörighet, som inbyggt [läsare](../../role-based-access-control/built-in-roles.md#reader) rollen och ha högre.

När du kör ett kommando måste den `Microsoft.Compute/virtualMachines/runCommand/action` behörighet, som den [deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rollen och ha högre.

Du kan använda en av de [inbyggda](../../role-based-access-control/built-in-roles.md) roller eller skapa en [anpassade](../../role-based-access-control/custom-roles.md) roll som ska användas kör kommandot.

## <a name="next-steps"></a>Nästa steg

Se, [köra skript i din Linux-VM](run-scripts-in-vm.md) vill veta mer om andra sätt att köra skript och kommandon via fjärranslutning i den virtuella datorn.
