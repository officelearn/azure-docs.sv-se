


## <a name="using-vm-extensions"></a>Med hjälp av VM-tillägg
Azure VM-tillägg implementerar beteenden eller funktioner som antingen hjälper andra program som fungerar på virtuella Azure-datorer (till exempel den **WebDeployForVSDevTest** tillägget kan Visual Studio Web Deploy Solutions på Azure VM) eller ange den möjlighet att interagera med den virtuella datorn att stödja vissa andra uppträdande (du kan till exempel använda VM-Access-tillägg från PowerShell, Azure CLI och REST-klienter att återställa eller ändra värden för fjärråtkomst på Azure VM).

> [!IMPORTANT]
> En fullständig lista över tillägg av funktioner som de stöder finns i [Azure VM-tillägg och funktioner](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Eftersom varje VM-tillägget har stöd för en specifik funktion, beroende exakt vad du kan och inte kan göra med ett tillägg av tillägget. Kontrollera därför att du har läs i dokumentationen för VM-tillägget som du vill använda innan du ändrar den virtuella datorn. Det går inte att ta bort vissa VM-tillägg. andra har egenskaper som kan anges som ändrar VM beteende radikalt.
> 
> 

De vanligaste uppgifterna är:

1. Söka efter tillgängliga tillägg
2. Uppdatering av inlästa tillägg
3. Lägga till tillägg
4. Ta bort tillägg

## <a name="find-available-extensions"></a>Hitta tillgängliga tillägg
Du kan hitta tillägget och utökad information med hjälp av:

* PowerShell
* Kommandoraden för Azure plattformsoberoende gränssnitt (Azure CLI)
* REST API för Service Management

### <a name="azure-powershell"></a>Azure PowerShell
Vissa tillägg har PowerShell-cmdlets som är specifika för dem, som kan underlätta konfigurationen från PowerShell; men följande cmdlets fungerar för alla VM-tillägg.

Du kan använda följande cmdletar för att få information om tillgängliga tillägg:

* För instanser av webbroller eller arbetsroller som du kan använda den [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet.
* För instanser av virtuella datorer som du kan använda den [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) cmdlet.
  
   Till exempel följande kodexempel visar hur du vill visa information för den **IaaSDiagnostics** tillägget med hjälp av PowerShell.
  
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
Vissa tillägg har Azure CLI-kommandon som är specifika för dem (Docker VM-tillägget är ett exempel), som kan underlätta konfigurationen; men följande kommandon som fungerar för alla VM-tillägg.

Du kan använda den **azure vm tilläggslistan** kommando för att hämta information om tillgängliga tillägg och använda den **–-json** alternativet för att visa all tillgänglig information om en eller flera filnamnstillägg. Om du inte använder en Tilläggsnamn returnerar kommandot en JSON-beskrivning av alla tillgängliga tillägg.

Till exempel följande kodexempel visar hur du vill visa information för den **IaaSDiagnostics** tillägget med hjälp av Azure CLI **azure vm tilläggslistan** kommandot och använder den **–-json**  alternativet för att returnera fullständig information.

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
Du kan använda följande REST API: er för att få information om tillgängliga tillägg:

* För instanser av webbroller eller arbetsroller som du kan använda den [lista över tillgängliga tillägg](https://msdn.microsoft.com/library/dn169559.aspx) igen. Om du vill visa en lista med versionerna av tillgängliga tillägg, kan du använda [lista tillägget versioner](https://msdn.microsoft.com/library/dn495437.aspx).
* För instanser av virtuella datorer som du kan använda den [lista Resurstillägg](https://msdn.microsoft.com/library/dn495441.aspx) igen. Om du vill visa en lista med versionerna av tillgängliga tillägg, kan du använda [lista resurs tillägget versioner](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Lägga till, uppdatera, eller inaktivera tillägg
Tillägg kan läggas till när en instans skapas eller de kan läggas till en instans som körs. Tillägg kan uppdateras, inaktiveras eller tas bort. Du kan utföra dessa åtgärder med hjälp av Azure PowerShell-cmdlets eller med hjälp av Service Management REST API-åtgärder. Parametrar som krävs för att installera och konfigurera vissa tillägg. Offentliga och privata parametrar stöds för tillägg.

### <a name="azure-powershell"></a>Azure PowerShell
Med hjälp av Azure PowerShell-cmdlets är det enklaste sättet att lägga till och uppdatera tillägg. När du använder cmdlets tillägg görs merparten av konfigurationen av tillägget för dig. Ibland kan behöva du lägga till ett tillägg. När du behöver göra detta måste du ange konfigurationen för tillägget.

Du kan använda följande cmdletar för att veta om ett tillägg kräver en konfiguration av offentliga och privata parametrar:

* För instanser av webbroller eller arbetsroller som du kan använda den **Get-AzureServiceAvailableExtension** cmdlet.
* För instanser av virtuella datorer som du kan använda den **Get-AzureVMAvailableExtension** cmdlet.

### <a name="service-management-rest-apis"></a>REST API:er för tjänsthantering
När du hämtar en lista över tillgängliga tillägg med hjälp av REST-API: er, får du information om hur tillägget ska konfigureras. Den information som returneras kan visa parameterinformation som representeras av ett schema med offentliga och privata schemat. Gemensamma parametervärden returneras i frågor om instanserna. Privata parametervärden returneras inte.

Du kan använda följande REST API: er för att veta om ett tillägg kräver en konfiguration av offentliga och privata parametrar:

* För instanser av webbroller eller arbetsroller, den **PublicConfigurationSchema** och **PrivateConfigurationSchema** elementen innehåller informationen i svaret från den [lista tillgängliga Tillägg](https://msdn.microsoft.com/library/dn169559.aspx) igen.
* För instanser av virtuella datorer i **PublicConfigurationSchema** och **PrivateConfigurationSchema** elementen innehåller informationen i svaret från den [lista resurs Tillägg](https://msdn.microsoft.com/library/dn495441.aspx) igen.

> [!NOTE]
> Tillägg kan också använda konfigurationer som har definierats med JSON. När dessa typer av tillägg används endast den **SampleConfig** elementet används.
> 
> 

