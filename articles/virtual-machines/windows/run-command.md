---
title: Kör PowerShell-skript i en Windows-dator i Azure
description: Det här avsnittet beskriver hur du kör PowerShell-skript i en Windows Azure-dator som kör kommandot
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ecf19da59e91edd08953296d96ca68ca62f008e1
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195361"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Kör PowerShell-skript i din virtuella Windows-dator med kommandot Kör

Kör kommandot använder VM-agenten för att köra PowerShell-skript i en Windows Azure-dator. Skripten kan användas för allmän dator eller hantering av program och kan användas för att snabbt diagnostisera och åtgärda problem med åtkomst och nätverk av virtuell dator och få den virtuella datorn till ett fungerande tillstånd.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="benefits"></a>Fördelar

Det finns flera alternativ som kan användas för att få åtkomst till dina virtuella datorer. Kör kommandot kan köra skript på dina virtuella datorer via fjärranslutning med VM-agenten. Kör kommandot kan användas via Azure-portalen [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), eller [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) för virtuella Windows-datorer.

Den här funktionen är användbar i samtliga scenarier där du vill köra ett skript i en virtuella datorer och är ett av de enda sätten att felsöka och åtgärda en virtuell dator som inte har RDP eller SSH-porten som är öppna på grund av felaktig nätverks- eller administrativ användare konfiguration.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du kör kommandot:

* Utdata är begränsad till senaste 4096 byte
* Den minsta tid att köra ett skript är ungefär 20 sekunder
* Skript som körs som System på Windows
* Köra ett skript i taget
* Skript som efterfrågar information (interaktivt läge) stöds inte.
* Du kan inte avbryta en som kör skript
* Den längsta tid som ett skript kan köras är 90 minuter, når tidsgränsen efter som den
* Utgående anslutning från den virtuella datorn krävs för att returnera resultatet av skriptet.

> [!NOTE]
> Ska fungera korrekt måste kräver köra kommandot anslutning (port 443) till Azure offentliga IP-adresser. Om tillägget inte har åtkomst till dessa slutpunkter, kan skripten köras men inte returnera resultat. Om du blockerar trafik på den virtuella datorn, kan du använda [tjänsttaggar](../../virtual-network/security-overview.md#service-tags) att tillåta trafik till Azure offentliga IP-adresser med hjälp av den `AzureCloud` tagg.

## <a name="run-a-command"></a>Köra ett kommando

Navigera till en virtuell dator i [Azure](https://portal.azure.com) och välj **Körningskommando** under **OPERATIONS**. Visas en lista över tillgängliga kommandon ska köras på den virtuella datorn.

![Kör kommandolista](./media/run-command/run-command-list.png)

Välj ett kommando för att köra. Några av kommandona som kan ha valfritt eller obligatoriska indataparametrar. Parametrarna presenteras för de kommandona som textfält som du kan ange värdena som indata. För varje kommando som du kan visa det skript som körs genom att expandera **Visa skript**. **RunPowerShellScript** skiljer sig från de andra kommandon som du kan ange ett eget skript.

> [!NOTE]
> De inbyggda kommandona som kan inte redigeras.

När kommandot har valts klickar du på **kör** att köra skriptet. Skriptet körs och när du är färdig returnerar utdata och eventuella fel i utdatafönstret. Följande skärmbild visar ett exempel på utdata från att köras i **RDPSettings** kommando.

![Kör skriptet kommandoutdata](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Kommandon

Den här tabellen visas listan över kommandon som är tillgängliga för virtuella Windows-datorer. Den **RunPowerShellScript** kommando kan användas för att köra alla anpassade skript som du vill.

|**Namn**|**Beskrivning**|
|---|---|
|**RunPowerShellScript**|Kör ett PowerShell.skript|
|**EnableRemotePS**|Konfigurerar datorn om du vill aktivera fjärr-PowerShell.|
|**EnableAdminAccount**|Kontrollerar om det lokala administratörskontot är inaktiverat och fall i så gör det möjligt.|
|**IPConfig**| Visar detaljerad information om den IP-adress, undernät och standard-gateway för varje nätverkskort som är bundet till TCP/IP.|
|**RDPSettings**|Kontrollerar registerinställningar och inställningar för domänen. Föreslår åtgärder om datorn tillhör en domän eller ändrar inställningar till standardvärdena.|
|**ResetRDPCert**|Tar bort SSL-certifikatet som är kopplad till RDP-lyssnaren och återställer RDP listerner säkerhet till standard. Använd det här skriptet om du ser några problem med certifikatet.|
|**SetRDPPort**|Mängder standard eller användare anges portnummer för anslutning till fjärrskrivbord. Gör det möjligt för brandväggsregel för ingående åtkomst till porten.|

## <a name="powershell"></a>PowerShell

Följande är ett exempel med hjälp av den [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet för att köra ett PowerShell-skript på en virtuell Azure-dator. Cmdleten förväntar sig skriptet som refereras till i den `-ScriptPath` parameter ska vara lokalt där cmdleten körs.


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomsten till kommandot Kör

Lista kör kommandon eller som visar information om ett kommando kräver den `Microsoft.Compute/locations/runCommands/read` behörighet, som inbyggt [läsare](../../role-based-access-control/built-in-roles.md#reader) rollen och ha högre.

När du kör ett kommando måste den `Microsoft.Compute/virtualMachines/runCommand/action` behörighet, som den [virtuell Datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rollen och ha högre.

Du kan använda en av de [inbyggda](../../role-based-access-control/built-in-roles.md) roller eller skapa en [anpassade](../../role-based-access-control/custom-roles.md) roll som ska användas kör kommandot.

## <a name="next-steps"></a>Nästa steg

Se, [köra skript i Windows-VM](run-scripts-in-vm.md) vill veta mer om andra sätt att köra skript och kommandon via fjärranslutning i den virtuella datorn.
