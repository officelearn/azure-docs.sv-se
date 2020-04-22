---
title: Köra skalskript i en Virtuell Linux-dator på Azure
description: I det här avsnittet beskrivs hur du kör skript i en virtuell Azure Linux-dator med hjälp av funktionen Kör kommando
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758598"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Kör skalskript i din Virtuella Linux-dator med kommandot Kör

Funktionen Kör kommando använder den virtuella datorns (VM) agent för att köra skalskript i en Virtuell Azure Linux-dator. Du kan använda dessa skript för allmän maskin- eller programhantering. De kan hjälpa dig att snabbt diagnostisera och åtgärda VM-åtkomst och nätverksproblem och få den virtuella datorn tillbaka till ett bra tillstånd.

## <a name="benefits"></a>Fördelar

Du kan komma åt dina virtuella datorer på flera sätt. Kör kommando kan köra skript på dina virtuella datorer på distans med hjälp av VM-agenten. Du använder Kör kommando via Azure-portalen, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)eller [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) för Linux virtuella datorer.

Den här funktionen är användbar i alla scenarier där du vill köra ett skript i en virtuell dator. Det är ett av de enda sätten att felsöka och åtgärda en virtuell dator som inte har RDP- eller SSH-porten öppen på grund av felaktig nätverkskonfiguration eller administrativ användarkonfiguration.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du använder Kör kommando:

* Utdata är begränsad till de senaste 4 096 bytena.
* Den minsta tiden för att köra ett skript är cirka 20 sekunder.
* Skript körs som standard som en förhöjd användare på Linux.
* Du kan köra ett skript i taget.
* Skript som frågar efter information (interaktivt läge) stöds inte.
* Du kan inte avbryta ett skript som körs.
* Den maximala tiden ett skript kan köras är 90 minuter. Därefter tar skriptet time out.
* Utgående anslutning från den virtuella datorn krävs för att returnera resultaten av skriptet.

> [!NOTE]
> För att fungera korrekt kräver Run Command anslutning (port 443) till offentliga Azure-IP-adresser. Om tillägget inte har åtkomst till dessa slutpunkter kan skripten köras men inte returnera resultaten. Om du blockerar trafik på den virtuella datorn kan du använda [tjänsttaggar](../../virtual-network/security-overview.md#service-tags) för `AzureCloud` att tillåta trafik till offentliga Azure-IP-adresser med hjälp av taggen.

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen visar listan över kommandon som är tillgängliga för virtuella Linux-datorer. Du kan använda kommandot **RunShellScript** för att köra alla anpassade skript som du vill ha. När du använder Azure CLI eller PowerShell för att köra ett `--command-id` kommando `-CommandId` måste värdet som du anger för parametern eller vara ett av följande angivna värden. När du anger ett värde som inte är ett tillgängligt kommando visas följande felmeddelande:

```error
The entity was not found in this Azure location
```

|**Namn**|**Beskrivning**|
|---|---|
|**RunShellScript (RunShellScript)**|Kör ett Linux-skalskript.|
|**Ifconfig**| Hämtar konfigurationen av alla nätverksgränssnitt.|

## <a name="azure-cli"></a>Azure CLI

I följande exempel används kommandot [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) för att köra ett skalskript på en Virtuell Azure Linux-dator.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Om du vill köra kommandon `sudo -u` som en annan användare anger du för att ange ett användarkonto.

## <a name="azure-portal"></a>Azure Portal

Gå till en virtuell dator i [Azure-portalen](https://portal.azure.com) och välj **Kommandot Kör** under **OPERATIONER**. Du ser en lista över tillgängliga kommandon som ska köras på den virtuella datorn.

![Lista över kommandon](./media/run-command/run-command-list.png)

Välj ett kommando som ska köras. Vissa kommandon kan ha valfria eller nödvändiga indataparametrar. För dessa kommandon visas parametrarna som textfält för att du ska kunna ange indatavärdena. För varje kommando kan du visa skriptet som körs genom att expandera **Visa skript**. **RunShellScript** skiljer sig från de andra kommandona, eftersom du kan ange ett eget anpassat skript.

> [!NOTE]
> De inbyggda kommandona kan inte redigeras.

När du har valt kommandot väljer du **Kör** för att köra skriptet. När skriptet är klart returneras utdata och eventuella fel i utdatafönstret. Följande skärmbild visar ett exempel på utdata från att köra **ifconfig-kommandot.**

![Kör kommandoskriptutdata](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

I följande exempel används cmdleten Invoke-AzVMRunCommand för att köra ett PowerShell-skript på en Virtuell Azure.The following example uses the [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet to run a PowerShell script on an Azure VM. Cmdlet förväntar sig att skriptet som refereras i parametern `-ScriptPath` ska vara lokalt där cmdleten körs.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomsten till kommandot Kör

Om du anger körningskommandona eller visar `Microsoft.Compute/locations/runCommands/read` information om ett kommando krävs behörighet på prenumerationsnivå. Rollen inbyggd [läsare](../../role-based-access-control/built-in-roles.md#reader) och högre nivåer har den här behörigheten.

Att köra ett `Microsoft.Compute/virtualMachines/runCommand/action` kommando kräver behörighet på prenumerationsnivå. Rollen [Deltagare i virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och högre nivåer har den här behörigheten.

Du kan använda en av de [inbyggda rollerna](../../role-based-access-control/built-in-roles.md) eller skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) för att använda Kör kommando.

## <a name="next-steps"></a>Nästa steg

Mer information om andra sätt att fjärrköra skript och kommandon i den virtuella datorn finns [i Kör skript i din Virtuella Linux-dator](run-scripts-in-vm.md).
