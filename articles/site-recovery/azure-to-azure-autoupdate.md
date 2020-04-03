---
title: Automatisk uppdatering av mobilitetstjänsten i Azure Site Recovery
description: Översikt över automatisk uppdatering av mobilitetstjänsten när Azure-virtuella datorer replikeras med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618967"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatisk uppdatering av mobilitetstjänsten i Azure-to-Azure-replikering

Azure Site Recovery använder en månatlig version kadens för att åtgärda eventuella problem och förbättra befintliga funktioner eller lägga till nya. Om du vill behålla tjänsten måste du planera för korrigeringsdistribution varje månad. Om du vill undvika de omkostnader som är associerade med varje uppgradering kan du tillåta att webbplatsåterställning hanterar komponentuppdateringar.

Som nämnts i [Azure-to-Azure-arkitektur för haveriberedskap](azure-to-azure-architecture.md)installeras mobilitetstjänsten på alla virtuella Azure-datorer (VMs) som har replikering aktiverat från en Azure-region till en annan. När du använder automatiska uppdateringar uppdaterar varje ny utgåva tillägget mobilitetstjänst.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Så här fungerar automatiska uppdateringar

När du använder Site Recovery för att hantera uppdateringar distribuerar den en global runbook (som används av Azure-tjänster) via ett automatiseringskonto som skapats i samma prenumeration som valvet. Varje valv använder ett automationskonto. För varje virtuell dator i ett valv söker runbooken efter aktiva automatiska uppdateringar. Om det finns en nyare version av tillägget mobilitetstjänsten är tillgänglig installeras uppdateringen.

Standardschemat för runbook inträffar dagligen klockan 12:00 i tidszonen för den replikerade virtuella datorns geografi. Du kan också ändra runbook-schemat via automationskontot.

> [!NOTE]
> Från och med [samlad uppdatering 35](site-recovery-whats-new.md#updates-march-2019)kan du välja ett befintligt automatiseringskonto som ska användas för uppdateringar. Före samlad uppdatering 35 skapade Site Recovery automationskontot som standard. Du kan bara välja det här alternativet när du aktiverar replikering för en virtuell dator. Den är inte tillgänglig för en virtuell dator som redan har replikering aktiverad. Den inställning du väljer gäller för alla virtuella Azure-datorer som skyddas i samma valv.

Aktivera automatiska uppdateringar kräver inte en omstart av dina virtuella Azure-datorer eller påverkar pågående replikering.

Jobbfakturering i automatiseringskontot baseras på antalet jobbkörningsminuter som använts under en månad. Jobbkörning tar några sekunder till ungefär en minut varje dag och täcks som fria enheter. Som standard ingår 500 minuter som kostnadsfria enheter för ett automationskonto, vilket visas i följande tabell:

| Gratis enheter ingår (varje månad) | Pris |
|---|---|
| Jobbkörning 500 minuter | 0,14/minut

## <a name="enable-automatic-updates"></a>Aktivera automatiska uppdateringar

Det finns flera sätt att hantera tilläggsuppdateringar för webbplatsåterställning:

- [Hantera som en del av aktivera replikeringssteget](#manage-as-part-of-the-enable-replication-step)
- [Växla inställningarna för tilläggsuppdatering i valvet](#toggle-the-extension-update-settings-inside-the-vault)
- [Hantera uppdateringar manuellt](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Hantera som en del av aktivera replikeringssteget

När du aktiverar replikering för en virtuell dator, antingen [från vm-vyn](azure-to-azure-quickstart.md) eller [från återställningstjänstvalvet,](azure-to-azure-how-to-enable-replication.md)kan du antingen tillåta platsåterställning att hantera uppdateringar för site recovery-tillägget eller hantera det manuellt.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Inställningar för tillägg":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Växla inställningarna för tilläggsuppdatering i valvet

1. Gå till **Hantera** > **infrastruktur för återställningstjänster från**valvet för återställningstjänster .
1. Under För**uppdateringsinställningar** >  >  **för Azure-tillägg**Tillåt**webbplatsåterställning att hantera**väljer du **På**.

   Om du vill hantera tillägget manuellt väljer du **Av**.

1. Välj **Spara**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Inställningar för tilläggsuppdatering":::

> [!IMPORTANT]
> När du väljer **Tillåt platsåterställning att hantera**tillämpas inställningen på alla virtuella datorer i valvet.

> [!NOTE]
> Båda alternativen meddelar dig om det automatiseringskonto som används för att hantera uppdateringar. Om du använder den här funktionen i ett valv för första gången skapas ett nytt automationskonto som standard. Alternativt kan du anpassa inställningen och välja ett befintligt automatiseringskonto. Alla efterföljande tak för att aktivera replikering i samma valv kommer att använda det tidigare skapade automationskontot. För närvarande listar den nedrullningsvänliga menyn endast automatiseringskonton som finns i samma resursgrupp som valvet.

> [!IMPORTANT]
> Följande skript måste köras i samband med ett automatiseringskonto.
För ett anpassat automatiseringskonto använder du följande skript:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

            [Switch]
        $DisplayMessageToUser
        )

    Write-Output $Message

}

function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }

                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion

        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Hantera uppdateringar manuellt

1. Om det finns nya uppdateringar för mobilitetstjänsten installerad på dina virtuella datorer visas följande meddelande: **Uppdatering av replikeringsagenten för ny webbplatsåterställning är tillgänglig. Klicka här om du vill installera.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Fönstret Replikerade objekt":::

1. Välj meddelandet om du vill öppna urvalssidan för den virtuella datorn.
1. Välj de virtuella datorer som du vill uppgradera och välj sedan **OK**. Tjänsten Uppdatera mobilitet startar för varje vald virtuell dator.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="VM-lista för replikerade objekt":::

## <a name="common-issues-and-troubleshooting"></a>Vanliga problem och felsökning

Om det finns ett problem med de automatiska uppdateringarna visas ett felmeddelande under **Konfigurationsproblem** på instrumentpanelen för valvet.

Om du inte kan aktivera automatiska uppdateringar läser du följande vanliga fel och rekommenderade åtgärder:

- **Fel:** Du har inte behörighet att skapa ett Azure Run As-konto (tjänstens huvudnamn) och bevilja rollen Deltagare till tjänstens huvudnamn.

  **Rekommenderad åtgärd**: Kontrollera att det inloggade kontot tilldelas som deltagare och försök igen. Mer information om hur du tilldelar behörigheter finns i avsnittet behörigheter som krävs i [How to: Use the portal för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Om du vill åtgärda de flesta problem när du har aktiverat automatiska uppdateringar väljer du **Reparera**. Om reparationsknappen inte är tillgänglig läser du felmeddelandet som visas i inställningsfönstret för tilläggsuppdatering.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Reparationsknappen för site recovery-tjänsten i inställningar för uppdatering av tillägg":::

- **Fel:** Kontot Kör som har inte behörighet att komma åt återställningstjänstresursen.

  **Rekommenderad åtgärd**: Ta bort och återskapa sedan [kontot Kör som](/azure/automation/automation-create-runas-account). Eller kontrollera att Automation Run As-kontots Azure Active Directory-program kan komma åt återställningstjänstresursen.

- **Fel:** Kör som konto hittades inte. Antingen togs något av dessa bort eller har inte skapats - Azure Active Directory Application, Service Principal, Role, Automation Certificate Asset, Automation Connection Asset - eller så är tumavtrycket inte identiskt mellan certifikat och anslutning.

  **Rekommenderad åtgärd**: Ta bort och återskapa sedan [kontot Kör som](/azure/automation/automation-create-runas-account).

- **Fel:** Azure Run som certifikat som används av automationskontot håller på att upphöra att gälla.

  Det självsignerade certifikat som skapas för kontot Kör som upphör att gälla ett år från det datum då det skapades. Du kan förnya det när som helst innan det upphör att gälla. Om du har registrerat dig för e-postmeddelanden får du också e-postmeddelanden när en åtgärd krävs från din sida. Det här felet visas två månader före utgångsdatumet och ändras till ett kritiskt fel om certifikatet har upphört att gälla. När certifikatet har upphört att gälla fungerar inte automatisk uppdatering förrän du förnyar samma.

  **Rekommenderad åtgärd**: Lös problemet genom att välja **Reparera** och sedan förnya **certifikat .**

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="förnya-cert":::

  > [!NOTE]
  > När du har förnyat certifikatet uppdaterar du sidan så att den aktuella statusen visas.
