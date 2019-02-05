---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d14cfb82ae74f85425dbd3e8a365e8b99969641d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735909"
---
## <a name="using-vm-extensions"></a>Med VM-tillägg
Azure VM-tillägg implementera beteenden eller funktioner som antingen underlättar för andra program som fungerar på virtuella Azure-datorer (till exempel den **WebDeployForVSDevTest** tillägget kan Visual Studio Web Deploy lösningar på Azure-VM) eller ange den möjlighet att interagera med den virtuella datorn för att hantera vissa andra beteenden (du kan till exempel använda tillägg för åtkomst till virtuell dator från PowerShell, Azure CLI och REST-klienter att återställa eller ändra värden för fjärråtkomst på din Azure-VM).

> [!IMPORTANT]
> En fullständig lista över tillägg av funktioner som de stöder finns i [Azure VM-tillägg och funktioner](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Eftersom varje VM-tillägget har stöd för en specifik funktion, beroende exakt vad du kan se och inte kan göra med ett tillägg av tillägget. Kontrollera därför att du har läst dokumentationen för VM-tillägget som du vill använda innan du ändrar den virtuella datorn. Det går inte att ta bort vissa VM-tillägg. andra har egenskaper som kan anges som gör det otroligt mycket ändrar beteendet för virtuell dator.
> 
> 

De vanligaste uppgifterna är:

1. Hitta tillgängliga tillägg
2. Uppdatera inlästa tillägg
3. Lägga till tillägg
4. Ta bort tillägg

## <a name="find-available-extensions"></a>Hitta tillgängliga tillägg
Du kan hitta tillägget och utökad information med hjälp av:

* PowerShell
* Azure plattformsoberoende Command Line Interface (Azure CLI)
* REST API för Service Management

### <a name="azure-powershell"></a>Azure PowerShell
Vissa tillägg har PowerShell-cmdletar som är specifika för dem, vilket kan göra det enklare; att deras konfiguration från PowerShell men följande cmdlets fungerar för alla VM-tillägg.

Du kan använda följande cmdletar för att hämta information om tillgängliga tillägg:

* För instanser av web-roller eller worker-roller som du kan använda den [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet.
* För instanser av virtuella datorer som du kan använda den [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) cmdlet.
  
   Till exempel i följande kodexempel visar hur du vill visa information för den **IaaSDiagnostics** tillägget med hjälp av PowerShell.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Azure Command Line Interface (Azure CLI)
Vissa tillägg har Azure CLI-kommandon som är specifika för dem (Docker VM-tillägget är ett exempel), som kan underlätta deras konfiguration; men följande kommandon för att fungera för alla VM-tillägg.

Du kan använda den **azure vm tilläggslistan** att få information om tillgängliga tillägg och använda den **–-json** alternativet för att visa all tillgänglig information om ett eller flera tillägg. Om du inte använder ett Tilläggsnamn returnerar kommandot en JSON-beskrivning av alla tillgängliga tillägg.

Till exempel i följande kodexempel visar hur du vill visa information för den **IaaSDiagnostics** tillägget med hjälp av Azure CLI **azure vm tilläggslistan** kommandot och använder den **–-json**  alternativet att returnera fullständig information.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>REST API:er för tjänsthantering
Du kan använda följande REST API: er för att hämta information om tillgängliga tillägg:

* För instanser av web-roller eller worker-roller som du kan använda den [listan över tillgängliga tillägg](https://msdn.microsoft.com/library/dn169559.aspx) igen. Om du vill visa versionerna av tillgängliga tillägg, kan du använda [lista över tillägg versionerna](https://msdn.microsoft.com/library/dn495437.aspx).
* För instanser av virtuella datorer som du kan använda den [lista Resurstillägg](https://msdn.microsoft.com/library/dn495441.aspx) igen. Om du vill visa versionerna av tillgängliga tillägg, kan du använda [lista Resource Extension Versions](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Lägga till, uppdatera, eller inaktivera tillägg
Tillägg kan läggas till när en instans skapas eller de kan läggas till en instans som körs. Tillägg kan uppdateras, inaktiveras eller tas bort. Du kan utföra dessa åtgärder med hjälp av Azure PowerShell-cmdletar eller med hjälp av Service Management REST API-åtgärder. Parametrar krävs för att installera och konfigurera vissa tillägg. Offentliga och privata parametrar har stöd för tillägg.

### <a name="azure-powershell"></a>Azure PowerShell
Med Azure PowerShell-cmdletar är det enklaste sättet att lägga till och uppdatera tillägg. När du använder cmdletarna tillägget görs merparten av konfigurationen av tillägget för dig. Ibland kan behöva du lägga till ett tillägg. När du behöver göra detta måste du ange konfigurationen av tillägget.

Du kan använda följande cmdletar för att veta om ett tillägg kräver en konfiguration av offentliga och privata parametrar:

* För instanser av web-roller eller worker-roller som du kan använda den **Get-AzureServiceAvailableExtension** cmdlet.
* För instanser av virtuella datorer som du kan använda den **Get-AzureVMAvailableExtension** cmdlet.

### <a name="service-management-rest-apis"></a>REST API:er för tjänsthantering
När du hämtar en lista över tillgängliga tillägg med hjälp av REST-API: er, får du information om hur tillägget ska konfigureras. Den information som returneras kan visa parameterinformation som representeras av en offentlig schema och privata schema. Offentliga parametervärden returneras i frågor om instanserna. Privata parametervärden returneras inte.

Du kan använda följande REST API: er du behöver veta om ett tillägg kräver en konfiguration av offentliga och privata parametrar:

* För instanser av web-roller eller worker-roller, den **PublicConfigurationSchema** och **PrivateConfigurationSchema** element innehåller informationen som svar från den [lista tillgängliga Tillägg](https://msdn.microsoft.com/library/dn169559.aspx) igen.
* För instanser av virtuella datorer, den **PublicConfigurationSchema** och **PrivateConfigurationSchema** element innehåller informationen som svar från den [lista resurs Tillägg](https://msdn.microsoft.com/library/dn495441.aspx) igen.

> [!NOTE]
> Tillägg kan också använda konfigurationer som har definierats med JSON. När dessa typer av tillägg används bara den **SampleConfig** elementet används.
> 
> 

