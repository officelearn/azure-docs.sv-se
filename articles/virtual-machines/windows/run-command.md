---
title: Kör PowerShell-skript i en virtuell Windows-dator i Azure
description: I det här avsnittet beskrivs hur du kör PowerShell-skript i en virtuell Azure Windows-dator med kommandot kör
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0a9a5e465e160da34a21f66fd7176a8fea5d1aac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376254"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Kör PowerShell-skript i din virtuella Windows-dator med kommandot kör

Kör kommando använder den virtuella dator agenten för att köra PowerShell-skript i en virtuell Azure Windows-dator. De här skripten kan användas för allmän hantering av datorer och program och kan användas för att snabbt diagnostisera och åtgärda åtkomst till virtuella datorer och nätverks problem och återställa den virtuella datorn till ett lyckat tillstånd.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Erbjudande

Det finns flera alternativ som kan användas för att få åtkomst till dina virtuella datorer. Kommandot kör kan köra skript på dina virtuella datorer via fjärr anslutning med den virtuella dator agenten. Kommandot kör kan användas via Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)eller [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) för virtuella Windows-datorer.

Den här funktionen är användbar i alla scenarier där du vill köra ett skript på en virtuell dator och är ett av de enda sätt som du kan använda för att felsöka och reparera en virtuell dator som inte har RDP-eller SSH-porten öppen på grund av en felaktig nätverks-eller administrativ användare inställningarna.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du använder Kör kommando:

* Utdata är begränsade till de senaste 4096 byten
* Minimi tiden att köra ett skript är ungefär 20 sekunder
* Skript som körs som system i Windows
* Ett skript i taget kan köras
* Skript som efterfrågar information (interaktivt läge) stöds inte.
* Det går inte att avbryta körning av skript
* Den längsta tid som ett skript kan köras är 90 minuter, och tids gränsen uppnåddes
* Utgående anslutningar från den virtuella datorn krävs för att returnera resultatet av skriptet.

> [!NOTE]
> För att funktionen ska fungera korrekt måste körnings kommandot ansluta (port 443) till offentliga Azure-IP-adresser. Om tillägget inte har åtkomst till dessa slut punkter kan skripten köras, men inga resultat returneras. Om du blockerar trafik på den virtuella datorn kan du använda [service märken](../../virtual-network/security-overview.md#service-tags) för att tillåta trafik till offentliga Azure-IP-adresser med hjälp av taggen `AzureCloud`.

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen visar en lista över kommandon som är tillgängliga för virtuella Windows-datorer. Kommandot **RunPowerShellScript** kan användas för att köra anpassade skript som du vill använda. När du använder Azure CLI eller PowerShell för att köra ett kommando måste värdet som du anger för parametern `--command-id` eller `-CommandId` vara ett av värdena i listan nedan. När du anger ett värde som inte är ett tillgängligt kommando får du ett fel meddelande.

```error
The entity was not found in this Azure location
```

|**Namn**|**Beskrivning**|
|---|---|
|**RunPowerShellScript**|Kör ett PowerShell-skript|
|**EnableRemotePS**|Konfigurerar datorn för att aktivera fjärr-PowerShell.|
|**EnableAdminAccount**|Kontrollerar om det lokala administratörs kontot är inaktiverat och om så är möjligt.|
|**Config**| Visar detaljerad information om IP-adressen, nät masken och standard-gatewayen för varje nätverkskort som är kopplat till TCP/IP.|
|**RDPSettings**|Kontrollerar register inställningar och domän princip inställningar. Föreslår princip åtgärder om datorn tillhör en domän eller ändrar inställningarna till standardvärdena.|
|**ResetRDPCert**|Tar bort SSL-certifikatet som är kopplat till RDP-lyssnaren och återställer RDP-lyssnings säkerheten till standard. Använd det här skriptet om du ser några problem med certifikatet.|
|**SetRDPPort**|Ställer in standardvärdet eller det angivna port numret för fjärr skrivbords anslutningar. Aktiverar brand Väggs regel för inkommande åtkomst till porten.|

## <a name="azure-cli"></a>Azure CLI

Följande är ett exempel som använder kommandot [AZ VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) för att köra ett gränssnitts skript på en virtuell Azure Linux-dator.

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

## <a name="azure-portal"></a>Azure portal

Navigera till en virtuell dator i [Azure](https://portal.azure.com) och välj **Kör kommando** under **åtgärder**. En lista över tillgängliga kommandon som kan köras på den virtuella datorn visas.

![Kör kommando lista](./media/run-command/run-command-list.png)

Välj ett kommando som ska köras. Några av kommandona kan ha valfria eller obligatoriska indataparametrar. För dessa kommandon visas parametrarna som textfält där du kan ange indatavärden. För varje kommando kan du Visa skriptet som körs genom att expandera **visnings skript**. **RunPowerShellScript** skiljer sig från de andra kommandona så att du kan ange ett eget anpassat skript.

> [!NOTE]
> De inbyggda kommandona kan inte redige ras.

När kommandot har valts klickar du på **Kör** för att köra skriptet. Skriptet körs och när det är klart returnerar utdata och eventuella fel i fönstret utdata. Följande skärm bild visar ett exempel på utdata från att köra kommandot **RDPSettings** .

![Kör utdata från kommando skript](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Följande är ett exempel som använder cmdleten [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) för att köra ett PowerShell-skript på en virtuell Azure-dator. Cmdleten förväntar sig att skriptet som refereras till i parametern `-ScriptPath` ska vara lokalt till den plats där cmdleten körs.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomst till Kör kommando

Att ange körnings kommandon eller Visa information om ett kommando kräver behörigheten `Microsoft.Compute/locations/runCommands/read` på prenumerations nivån, som är den inbyggda [läsar](../../role-based-access-control/built-in-roles.md#reader) rollen och högre.

Körning av ett kommando kräver behörigheten `Microsoft.Compute/virtualMachines/runCommand/action` på prenumerations nivån, som rollen för den [virtuella datorn](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och högre har.

Du kan använda en av de [inbyggda](../../role-based-access-control/built-in-roles.md) rollerna eller skapa en [anpassad](../../role-based-access-control/custom-roles.md) roll för att använda kör-kommandot.

## <a name="next-steps"></a>Nästa steg

Se [Kör skript i din virtuella Windows-dator](run-scripts-in-vm.md) för att lära dig mer om andra sätt att köra skript och kommandon via fjärr anslutning i den virtuella datorn.
