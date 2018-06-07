---
title: Kör shell-skript i ett Linux-VM på Azure
description: Det här avsnittet beskriver hur du kan köra skript i ett Azure Linux-dator som kör kommandot
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6f21452ddc6c8a48392d24615e8dbcbba8b996c8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661121"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Kör kommandoskript i Linux-VM med kommandot Kör

Kör kommandot kan du köra shell-skript i ett virtuella Azure Linux-datorn oavsett nätverksanslutning. Dessa skript kan användas för allmän dator eller programhantering och kan användas för att snabbt diagnostisera och åtgärda problem med VM åtkomst och nätverk och hämta den virtuella datorn till ett fungerande tillstånd.

## <a name="benefits"></a>Fördelar

Det finns flera alternativ som kan användas för att få åtkomst till dina virtuella datorer. Kör kommandot kan köra skript på virtuella datorer oavsett nätverksanslutning och finns som standard (Ingen installation krävs). Kör kommandot kan användas via Azure portal [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), eller [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Den här funktionen är användbart i alla scenarier där du vill köra ett skript witin en virtuella datorer och är en av det enda sättet att felsöka och åtgärda en virtuell dator som inte är ansluten till nätverket på grund av felaktig nätverks- eller administrativ användare konfiguration.

## <a name="restrictions"></a>Begränsningar

Här följer en lista över begränsningar som finns när du kör kommandot.

* Utdata är begränsad till den senaste 4096 byte
* Den minsta tid att köra ett skript ungefär 20 sekunder
* Skript körs som förhöjd på Linux
* Ett skript i taget kan köras
* Du kan inte avbryta ett skript som körs
* Den maximala tid som kan köra ett skript som är 90 minuter, där det ska gå innan timeout

## <a name="azure-cli"></a>Azure CLI

Följande är ett exempel med hjälp av den [kommandot med az vm kör](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) kommando för att köra ett kommandoskript på en Azure Linux-dator.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

## <a name="azure-portal"></a>Azure Portal

Navigera till en virtuell dator i [Azure](https://portal.azure.com) och välj **Kör kommando** under **OPERATIONS**. Visas med en lista över tillgängliga kommandon körs på den virtuella datorn.

![Kör kommandolista](./media/run-command/run-command-list.png)

Välj ett kommando ska köras. Vissa kommandon kan ha valfritt eller obligatoriskt indataparametrar. För de kommandona visas parametrarna som textfält att tillhandahålla indatavärdena. För varje kommando som du kan visa det skript som körs genom att expandera **Visa skript**. **RunPowerShellScript** skiljer sig från andra kommandon som du kan ange ett eget skript. 

> [!NOTE]
> Inbyggda kommandon kan inte redigeras.

När kommandot har valts klickar du på **kör** att köra skriptet. Skriptet körs och när du är färdig returnerar utdata och eventuella fel i utdatafönstret. Följande skärmbild visar ett exempel på utdata från att köras i **ifconfig** kommando.

![Kör skriptet kommandoutdata](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen innehåller en lista med kommandon som är tillgängliga för virtuella Linux-datorer. Den **RunShellScript** kommando kan användas för att köra alla anpassade skript som du vill.

|**Namn**|**Beskrivning**|
|---|---|
|**RunShellScript**|Kör ett Linux-kommandoskript.|
|**ifconfig**| Hämta konfigurationen av alla nätverksgränssnitt.|

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomst till kommandot Kör

Visar en lista över kör kommandon eller visar information om ett kommando måste den `Microsoft.Compute/locations/runCommands/read` behörighet, som inbyggt [läsare](../../role-based-access-control/built-in-roles.md#reader) roll och högre.

Köra ett kommando måste den `Microsoft.Compute/virtualMachines/runCommand/action` behörighet, som den [deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) roll och högre.

Du kan använda en av de [inbyggda](../../role-based-access-control/built-in-roles.md) roller eller skapa en [anpassade](../../role-based-access-control/custom-roles.md) roll som ska användas med kommandot Kör.

## <a name="next-steps"></a>Nästa steg

Se, [köra skript i Linux-VM](run-scripts-in-vm.md) vill veta mer om andra sätt att köra skript och kommandon från en fjärrdator i den virtuella datorn.
