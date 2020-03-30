---
title: Köra PowerShell-skript i en Windows VM i Azure
description: I det här avsnittet beskrivs hur du kör PowerShell-skript i en virtuell Azure Windows-dator med hjälp av funktionen Kör kommando
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749220"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Kör PowerShell-skript i windows-datorn med kommandot Kör

The Run Command feature uses the virtual machine (VM) agent to run PowerShell scripts within an Azure Windows VM. Du kan använda dessa skript för allmän maskin- eller programhantering. De kan hjälpa dig att snabbt diagnostisera och åtgärda VM-åtkomst och nätverksproblem och få den virtuella datorn tillbaka till ett bra tillstånd.

 

## <a name="benefits"></a>Fördelar

Du kan komma åt dina virtuella datorer på flera sätt. Kör kommando kan köra skript på dina virtuella datorer på distans med hjälp av VM-agenten. Du använder Kör kommando via Azure-portalen, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)eller [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) för Windows virtuella datorer.

Den här funktionen är användbar i alla scenarier där du vill köra ett skript i en virtuell dator. Det är ett av de enda sätten att felsöka och åtgärda en virtuell dator som inte har RDP- eller SSH-porten öppen på grund av felaktig nätverkskonfiguration eller administrativ användarkonfiguration.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du använder Kör kommando:

* Utdata är begränsad till de senaste 4 096 bytena.
* Den minsta tiden för att köra ett skript är cirka 20 sekunder.
* Skript körs som System i Windows.
* Ett skript i taget kan köras.
* Skript som frågar efter information (interaktivt läge) stöds inte.
* Du kan inte avbryta ett skript som körs.
* Den maximala tiden ett skript kan köras är 90 minuter. Efter det tar det time out.
* Utgående anslutning från den virtuella datorn krävs för att returnera resultaten av skriptet.

> [!NOTE]
> För att fungera korrekt kräver Run Command anslutning (port 443) till offentliga Azure-IP-adresser. Om tillägget inte har åtkomst till dessa slutpunkter kan skripten köras men inte returnera resultaten. Om du blockerar trafik på den virtuella datorn kan du använda [tjänsttaggar](../../virtual-network/security-overview.md#service-tags) för `AzureCloud` att tillåta trafik till offentliga Azure-IP-adresser med hjälp av taggen.

## <a name="available-commands"></a>Tillgängliga kommandon

I den här tabellen visas listan över kommandon som är tillgängliga för virtuella datorer i Windows. Du kan använda kommandot **RunPowerShellScript** för att köra alla anpassade skript som du vill ha. När du använder Azure CLI eller PowerShell för att köra ett `--command-id` kommando `-CommandId` måste värdet som du anger för parametern eller vara ett av följande angivna värden. När du anger ett värde som inte är ett tillgängligt kommando visas följande felmeddelande:

```error
The entity was not found in this Azure location
```

|**Namn**|**Beskrivning**|
|---|---|
|**RunPowerShellScript (RunPowerShellScript)**|Kör ett PowerShell-skript.|
|**AktiveraRemotePS**|Konfigurerar datorn så att fjärr-PowerShell aktiveras.|
|**AktiveraAdminAccount**|Kontrollerar om det lokala administratörskontot är inaktiverat och aktiveras i så fall.|
|**Ipconfig**| Visar detaljerad information för IP-adressen, nätmasken och standardgatewayen för varje kort som är bundet till TCP/IP.|
|**RDPSettings**|Kontrollerar registerinställningar och domänprincipinställningar. Föreslår principåtgärder om datorn är en del av en domän eller ändrar inställningarna till standardvärden.|
|**ResetRDPCert**|Tar bort SSL-certifikatet som är knutet till RDP-lyssnaren och återställer RDP-lyssnarens säkerhet till standard. Använd det här skriptet om du ser några problem med certifikatet.|
|**SetRDPPort**|Anger standard- eller användarspecificerat portnummer för fjärrskrivbordsanslutningar. Aktiverar brandväggsregler för inkommande åtkomst till porten.|

## <a name="azure-cli"></a>Azure CLI

I följande exempel används kommandot az vm run-command för att köra ett skalskript på en Virtuell Azure Windows.The following example uses the [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) command command to run a shell script on an Azure Windows VM.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

Gå till en virtuell dator i [Azure-portalen](https://portal.azure.com) och välj **Kommandot Kör** under **OPERATIONER**. Du ser en lista över tillgängliga kommandon som ska köras på den virtuella datorn.

![Lista över kommandon](./media/run-command/run-command-list.png)

Välj ett kommando som ska köras. Vissa kommandon kan ha valfria eller nödvändiga indataparametrar. För dessa kommandon visas parametrarna som textfält för att du ska kunna ange indatavärdena. För varje kommando kan du visa skriptet som körs genom att expandera **Visa skript**. **RunPowerShellScript** skiljer sig från de andra kommandona, eftersom du kan ange ett eget anpassat skript.

> [!NOTE]
> De inbyggda kommandona kan inte redigeras.

När du har valt kommandot väljer du **Kör** för att köra skriptet. När skriptet är klart returneras utdata och eventuella fel i utdatafönstret. Följande skärmbild visar ett exempel på utdata från att köra kommandot **RDPSettings.**

![Kör kommandoskriptutdata](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

I följande exempel används cmdleten Invoke-AzVMRunCommand för att köra ett PowerShell-skript på en Virtuell Azure.The following example uses the [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet to run a PowerShell script on an Azure VM. Cmdlet förväntar sig att skriptet som refereras i parametern `-ScriptPath` ska vara lokalt där cmdleten körs.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomsten till kommandot Kör

Om du anger körningskommandona eller visar `Microsoft.Compute/locations/runCommands/read` information om ett kommando krävs behörighet på prenumerationsnivå. Rollen inbyggd [läsare](../../role-based-access-control/built-in-roles.md#reader) och högre nivåer har den här behörigheten.

Att köra ett `Microsoft.Compute/virtualMachines/runCommand/action` kommando kräver behörighet på prenumerationsnivå. Rollen [Deltagare i virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och högre nivåer har den här behörigheten.

Du kan använda en av de [inbyggda rollerna](../../role-based-access-control/built-in-roles.md) eller skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) för att använda Kör kommando.

## <a name="next-steps"></a>Nästa steg

Mer information om andra sätt att fjärrköra skript och kommandon i den virtuella datorn finns [i Kör skript i din Virtuella Dator Windows](run-scripts-in-vm.md).
