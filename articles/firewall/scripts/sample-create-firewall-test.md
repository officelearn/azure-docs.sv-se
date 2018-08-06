---
title: Skriptexempel för Azure PowerShell – Skapa en testmiljö för Azure Firewall
description: Skriptexempel för Azure PowerShell – Skapa en testmiljö för Azure Firewall.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: daf847ea9a6b6310afbc8eca7f662065fe75ade6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397190"
---
# <a name="create-an-azure-firewall-test-environment"></a>Skapa en testmiljö för Azure Firewall

[!INCLUDE [firewall-preview-notice](../../../includes/firewall-preview-notice.md)]

Exemplen i Azure Firewall-artikeln förutsätter att du redan har aktiverat den offentliga förhandsversionen av Azure Firewall. Mer information finns i [Aktivera den offentliga förhandsversionen av Azure Firewall](../public-preview.md).

Det här Skriptexemplet skapar en brandvägg och en testmiljö för nätverket. Nätverket innehåller ett virtuellt nätverk med tre undernät: *AzureFirewallSubnet*, *ServersSubnet* och *JumpboxSubnet*. ServersSubnet och JumpboxSubnet har var sin Windows Server med två kärnor.

Brandväggen ligger i AzureFirewallSubnet och är konfigurerad med en programregelsamling som har en enda regel för att tillåta åtkomst till www.microsoft.com.

Dessutom skapas en användardefinierad väg som skickar nätverkstrafik från ServersSubnet via brandväggen där brandväggsreglerna tillämpas.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/powershell) eller från en lokal PowerShell-installation. 

Om du kör PowerShell lokalt kräver det här skriptet version 6.4.0 eller senare av AzureRM PowerShell-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. 

Du kan använda `PowerShellGet` om du behöver uppgradera, och det är inbyggt i Windows 10 och Windows Server 2016.

> [!NOTE]
>I andra Windows-versionen måste du installera `PowerShellGet` innan du kan använda det. Du kan köra `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` för att avgöra om det är installerat på datorn. Om du inte ser några utdata måste du installera den senaste versionen av [Windows Management-ramverket](https://www.microsoft.com/download/details.aspx?id=54616).

Mer information finns i [Installera Azure PowerShell i Windows med PowerShellGet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0)

Befintliga Azure PowerShell-installationer som gjorts med installationsprogrammet för Web Platform kommer att så i konflikt med installationen av PowerShellGet och måste tas bort.

Dessutom måste du installera förhandsversionen av AzureRM.Network (version 6.4.0). Om du har en äldre modul kör du `Uninstall-Module AzureRM.Network -Force` för att ta bort den. Kör sedan:

 `Install-Module -Name AzureRM.Network -Repository PSGallery -RequiredVersion 6.4.0-preview -AllowPrerelease -Force`

för att installera version 6.4.0.

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
|New-AzureRmFirewall| Skapar en ny Azure Firewall-brandvägg.|
|Get-AzureRmFirewall|Hämtar ett Azure Firewall-objekt.|
|New-AzureRmFirewallApplicationRule|Skapar en ny Azure Firewall-programregel.|
|Set-AzureRmFirewall|Sparar ändringarna i Azure Firewall-objektet.|


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

