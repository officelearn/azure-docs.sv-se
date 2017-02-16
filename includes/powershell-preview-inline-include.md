Azure PowerShell finns för nuvarande i två versioner, 1.0 och 0.9.8. Om du har befintliga skript och inte vill ändra dem just nu, kan du fortsätta använda 0.9.8-versionen. När du använder version 1.0, bör du noggrant testa skripten i testmiljöer innan du använder dem i produktion för att undvika oväntade konsekvenser.

1.0-cmdletar följer namngivningsmönstret {verb}-AzureRm {substantiv}, medan 0.9.8-namn inte inkluderar **Rm** (till exempel New-AzureRmResourceGroup istället för New-AzureResourceGroup). När du använder Azure PowerShell 0.9.8, måste du först aktivera Resource Manager-läget genom att köra kommandot **Switch-AzureMode AzureResourceManager**. Det här kommandot behövs inte i 1.0.

Nya funktioner kommer bara läggas till i 1.0-versionen. Information om version 1.0, inklusive installation och avinstallation, finns i [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).



<!--HONumber=Jan17_HO3-->


