---
title: Skriptexempel för Azure PowerShell – Skapa en testmiljö för Azure Firewall
description: Skriptexempel för Azure PowerShell – Skapa en testmiljö för Azure Firewall.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 3b55767a4375d41b1dc9c4357ca25e562a3cfabe
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438260"
---
# <a name="create-an-azure-firewall-test-environment"></a>Skapa en testmiljö för Azure Firewall

Det här Skriptexemplet skapar en brandvägg och en testmiljö för nätverket. Nätverket innehåller ett virtuellt nätverk med tre undernät: *AzureFirewallSubnet*, *ServersSubnet* och *JumpboxSubnet*. ServersSubnet och JumpboxSubnet har var sin Windows Server med två kärnor.

Brandväggen ligger i AzureFirewallSubnet och är konfigurerad med en programregelsamling som har en enda regel för att tillåta åtkomst till www.microsoft.com.

Dessutom skapas en användardefinierad väg som skickar nätverkstrafik från ServersSubnet via brandväggen där brandväggsreglerna tillämpas.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/powershell) eller från en lokal PowerShell-installation. 

Om du kör PowerShell lokalt kräver det här skriptet den senaste versionen av AzureRM PowerShell-modulen (6.9.0 eller senare). Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. 

Du kan använda `PowerShellGet` om du behöver uppgradera, och det är inbyggt i Windows 10 och Windows Server 2016.

> [!NOTE]
>I andra Windows-versionen måste du installera `PowerShellGet` innan du kan använda det. Du kan köra `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` för att avgöra om det är installerat på datorn. Om du inte ser några utdata måste du installera den senaste versionen av [Windows Management-ramverket](https://www.microsoft.com/download/details.aspx?id=54616).

Mer information finns i [Installera Azure PowerShell i Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.4.0)

Befintliga Azure PowerShell-installationer som gjorts med installationsprogrammet för Web Platform kommer att så i konflikt med installationen av PowerShellGet och måste tas bort.

Om du kör PowerShell lokalt måste du även komma ihåg att köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar ett konfigurationsobjekt för undernät. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Skapar säkerhetsregler som ska tilldelas till en nätverkssäkerhetsgrupp. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Associerar NSG:er med undernät. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en offentlig IP-adress för åtkomst till den virtuella datorn från Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Skapa en virtuell dator. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
|[New-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewall?view=azurermps-6.9.0)| Skapar en ny Azure Firewall-brandvägg.|
|[Get-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermfirewall?view=azurermps-6.9.0)|Hämtar ett Azure Firewall-objekt.|
|[New-AzureRmFirewallApplicationRule](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewallapplicationrule?view=azurermps-6.9.0)|Skapar en ny Azure Firewall-programregel.|
|[Set-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermfirewall?view=azurermps-6.9.0)|Sparar ändringarna i Azure Firewall-objektet.|


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

