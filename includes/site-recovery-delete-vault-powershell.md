## <a name="delete-a-recovery-services-vault-powershell"></a>Ta bort ett Recovery Services-valv (PowerShell)

1. Hämta Recovery Services-valvet

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Ta bort valvet

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Använd ovanstående kommando med största försiktighet eftersom om du förlorar alla data om du tar bort alla valv av misstag. Detta är en permanent åtgärd och det finns inget sätt att göra den ogjord.  




<!--HONumber=Feb17_HO3-->


