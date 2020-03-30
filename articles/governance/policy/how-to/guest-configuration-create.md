---
title: Så här skapar du principer för gästkonfiguration för Windows
description: Lär dig hur du skapar en azure-princip gästkonfigurationsprincip för Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24069ff6518c4244026378e48216d4568fffeb8a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365468"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Så här skapar du principer för gästkonfiguration för Windows

Innan du skapar anpassade principer är det en bra idé att läsa den konceptuella översiktsinformationen på sidan [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Mer information om hur du skapar principer för gästkonfiguration för Linux finns på sidan [Så här skapar du principer för gästkonfiguration för Linux](./guest-configuration-create-linux.md)

Vid granskning av Windows använder gästkonfiguration en DSC-resursmodul [(Desired State Configuration)](/powershell/scripting/dsc/overview/overview) till och konfigurationsfilen. DSC-konfigurationen definierar villkoret som datorn ska vara i.
Om utvärderingen av konfigurationen misslyckas utlöses **principeffektgranskningenIfNotExists** och datorn anses **vara icke-kompatibel**.

[Azure Policy Guest Configuration](../concepts/guest-configuration.md) kan endast användas för att granska inställningar inuti datorer. Reparation av inställningar inuti datorer är ännu inte tillgänglig.

Använd följande åtgärder för att skapa din egen konfiguration för att validera tillståndet för en Azure- eller icke-Azure-dator.

> [!IMPORTANT]
> Anpassade principer med gästkonfiguration är en förhandsgranskningsfunktion.

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Att skapa en gästkonfigurationsartefakt, automatiserad testning av artefakten, skapa en principdefinition och publicera principen kan automatiseras helt med hjälp av modulen Gästkonfiguration i PowerShell. Modulen kan installeras på en dator som kör Windows, macOS eller Linux med PowerShell 6.2 eller senare körs lokalt, eller med [Azure Cloud Shell](https://shell.azure.com), eller med Azure [PowerShell Core Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilering av konfigurationer stöds ännu inte på Linux.

### <a name="base-requirements"></a>Grundläggande krav

Operativsystem där modulen kan installeras:

- Linux
- macOS
- Windows

Resursmodulen Gästkonfiguration kräver följande programvara:

- PowerShell 6.2 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).
  - Endast AZ-modulerna "Az.Accounts" och "Az.Resources" krävs.

### <a name="install-the-module"></a>Installera modulen

Så här installerar du **GuestConfiguration-modulen** i PowerShell:

1. Kör följande kommando från en PowerShell-prompt:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Verifiera att modulen har importerats:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Gästkonfigurationsartefakter och princip för Windows

Gästkonfiguration använder PowerShell Desired State Configuration som språkabstraktion för att skriva vad som ska granskas i Windows. Agenten läser in en fristående instans av PowerShell 6.2, så det står inte i konflikt med användningen av PowerShell DSC i Windows PowerShell 5.1, och det finns inget krav på att förinstallera PowerShell 6.2 eller senare.

En översikt över DSC-begrepp och terminologi finns i [PowerShell DSC Översikt](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Så här skiljer sig gästkonfigurationsmoduler från Windows PowerShell DSC-moduler

När Gästkonfiguration granskar en dator `Test-TargetResource` körs den först för att avgöra om den är i rätt tillstånd. Det booleska värdet som returneras av funktionen avgör om Azure Resource Manager-status för gästtilldelningen ska vara kompatibel/inte-kompatibel. Nästa providern `Get-TargetResource` körs för att returnera det aktuella tillståndet för varje inställning så att information är tillgänglig både om varför en dator inte är kompatibel eller för att bekräfta att det aktuella tillståndet är kompatibelt.

### <a name="get-targetresource-requirements"></a>Krav på get-targetresource

Funktionen `Get-TargetResource` har särskilda krav för gästkonfiguration som inte har behövts för Windows Önskad tillståndskonfiguration.

- Den hashtable som returneras måste innehålla en egenskap med namnet **Reasons**.
- Egenskapen Reasons måste vara en matris.
- Varje objekt i matrisen ska vara en hashtable med nycklar med namnet **Kod** och **Fras**.

Egenskapen Reasons används av tjänsten för att standardisera hur information presenteras när en dator inte uppfyller kraven. Du kan se varje objekt i Reasons som en "orsak" att resursen inte är kompatibel. Egenskapen är en matris eftersom en resurs kan vara ur överensstämmelse av mer än en anledning.

Egenskaperna **Kod** och **fras** förväntas av tjänsten. När du skapar en anpassad resurs anger du den text (vanligtvis stdout) som du vill visa som orsaken till att resursen inte är kompatibel som värdet för **Fras**. **Koden** har specifika formateringskrav så att rapporteringen tydligt kan visa information om den resurs som används för granskningen. Den här lösningen gör gästkonfiguration utökningsbar. Alla kommandon kan köras så länge utdata kan returneras som ett strängvärde för **egenskapen Fras.**

- **Kod** (sträng): Namnet på resursen, upprepas och sedan ett kort namn utan blanksteg som identifierare av orsaken. Dessa tre värden bör kolon-avgränsas utan mellanslag.
  - Ett exempel skulle vara`registry:registry:keynotpresent`
- **Fras** (sträng): Läsbar text som kan läsas av människor för att förklara varför inställningen inte är kompatibel.
  - Ett exempel skulle vara`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```
### <a name="configuration-requirements"></a>Konfigurationskrav

Namnet på den anpassade konfigurationen måste vara konsekvent överallt. Namnet på ZIP-filen för innehållspaketet, konfigurationsnamnet i MOF-filen och gästtilldelningsnamnet i Resource Manager-mallen måste vara detsamma.

### <a name="scaffolding-a-guest-configuration-project"></a>Byggnadsställningar av ett gästkonfigurationsprojekt

Utvecklare som vill påskynda processen att komma igång och arbeta från exempelkod kan installera ett community-projekt med namnet **Guest Configuration Project**. Projektet installerar en mall för [Modulen Gips](https://github.com/powershell/plaster) PowerShell. Det här verktyget kan användas för att byggnadsställning ett projekt, inklusive en fungerande konfiguration och prov resurs, och en uppsättning [Pester](https://github.com/pester/pester) tester för att validera projektet. Mallen innehåller även uppgiftslöpare för Visual Studio Code för att automatisera byggandet och valideringen av gästkonfigurationspaketet. Mer information finns i GitHub-projektet [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

Mer information om hur du arbetar med konfigurationer i allmänhet finns i [Skriva, kompilera och Använda en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Förväntat innehåll i en gästkonfigurationsartefakt

Det slutförda paketet används av Gästkonfiguration för att skapa Azure-principdefinitioner. Paketet består av:

- Den kompilerade DSC-konfigurationen som en MOF
- Mappen Moduler
  - Modul för gästkonfigurering
  - Modulen DscNativeResources
  - (Windows) DSC-resursmoduler som krävs av MOF

PowerShell-cmdlets hjälper till att skapa paketet.
Ingen rotnivåmapp eller versionsmapp krävs.
Paketformatet måste vara en ZIP-fil.

### <a name="storing-guest-configuration-artifacts"></a>Lagra artefakter för gästkonfiguration

Zip-paketet måste lagras på en plats som är tillgänglig för hanterade virtuella datorer.
Exempel är GitHub-databaser, en Azure Repo eller Azure-lagring. Om du föredrar att inte göra paketet offentligt kan du inkludera en [SAS-token](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) i WEBBADRESSEN.
Du kan också implementera [tjänstslutpunkt](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) för datorer i ett privat nätverk, även om den här konfigurationen endast gäller åtkomst till paketet och inte kommunicera med tjänsten.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Steg för steg skapa en anpassad granskningsprincip för gästkonfiguration för Windows

Skapa en DSC-konfiguration. I följande PowerShell-skriptexempel skapas en konfiguration med namnet **AuditBitLocker**, **importerar psDscResources-resursmodulen** och resursen `Service` används för granskning för en tjänst som körs. Konfigurationsskriptet kan köras från en Windows- eller macOS-dator.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker -out ./Config
```

Kommandot `Node AuditBitlocker` är inte tekniskt krävs, men `AuditBitlocker.mof` det ger en `localhost.mof`fil som heter snarare än standard, . Med .mof filnamnet följa konfigurationen gör det enkelt att organisera många filer när du arbetar i stor skala.

När MOF har kompilerats måste stödfilerna paketeras tillsammans. Det slutförda paketet används av Gästkonfiguration för att skapa Azure-principdefinitioner.

Cmdlet `New-GuestConfigurationPackage` skapar paketet. Parametrar för `New-GuestConfigurationPackage` cmdlet när du skapar Windows-innehåll:

- **Namn**: Paketnamn för gästkonfiguration.
- **Konfiguration**: Kompilerad DSC-konfigurationsdokument fullständig sökväg.
- **Sökväg**: Sökväg för utdatamappen. Den här parametern är valfri. Om inget anges skapas paketet i aktuell katalog.

Kör följande kommando för att skapa ett paket med hjälp av konfigurationen som angavs i föregående steg:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

När du har skapat konfigurationspaketet men innan du publicerar det på Azure kan du testa paketet från din arbetsstation eller CI/CD-miljö. Cmdlet `Test-GuestConfigurationPackage` för GuestConfiguration innehåller samma agent i utvecklingsmiljön som används i Azure-datorer. Med den här lösningen kan du göra integrationstestning lokalt innan du släpper till fakturerade molnmiljöer.

Eftersom agenten faktiskt utvärderar den lokala miljön måste du i de flesta fall köra test-cmdlet på samma OS-plattform som du planerar att granska.

Parametrar för `Test-GuestConfigurationPackage` cmdlet:

- **Namn**: Principnamn för gästkonfiguration.
- **Parameter**: Principparametrar som anges i hashtable-format.
- **Sökväg**: Fullständig sökväg för gästkonfigurationspaketet.

Kör följande kommando för att testa paketet som skapats av föregående steg:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Cmdlet stöder också indata från PowerShell-pipelinen. Rör utgången `New-GuestConfigurationPackage` av cmdlet till `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Nästa steg är att publicera filen till blob storage. Skriptet nedan innehåller en funktion som du kan använda för att automatisera den här uppgiften. De kommandon som `publish` används i `Az.Storage` funktionen kräver modulen.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

När ett anpassad princippaket för gästkonfiguration har skapats och överförts skapar du principdefinitionen för gästkonfiguration. Cmdleten `New-GuestConfigurationPolicy` tar ett anpassat princippaket och skapar en principdefinition.

Parametrar för `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: Offentlig http(s) uri av gästkonfiguration innehållspaket.
- **DisplayName**: Principvisningsnamn.
- **Beskrivning**: Policybeskrivning.
- **Parameter**: Principparametrar som anges i hashtable-format.
- **Version**: Policyversion.
- **Sökväg**: Målsökväg där principdefinitioner skapas.
- **Plattform**: Målplattform (Windows/Linux) för gästkonfigurationspolicy och innehållspaket.

I följande exempel skapas principdefinitionerna i en angiven sökväg från ett anpassat princippaket:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Följande filer skapas `New-GuestConfigurationPolicy`av:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiativ.json**

Cmdlet-utdata returnerar ett objekt som innehåller initiativets visningsnamn och sökväg för principfilerna.

Publicera slutligen principdefinitionerna med `Publish-GuestConfigurationPolicy` cmdleten. Cmdleten har bara parametern **Path** som pekar på platsen `New-GuestConfigurationPolicy`för JSON-filerna som skapats av .

Om du vill köra kommandot Publicera behöver du åtkomst för att skapa principer i Azure. De specifika auktoriseringskraven dokumenteras på sidan [Översikt över Azure-princip.](../overview.md) Den bästa inbyggda rollen är **Resursprincipdeltagare**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Cmdlet `Publish-GuestConfigurationPolicy` accepterar sökvägen från PowerShell-pipelinen. Den här funktionen innebär att du kan skapa principfilerna och publicera dem i en enda uppsättning kommandon med pipade.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Med principen som skapats i Azure är det sista steget att tilldela initiativet. Se hur du tilldelar initiativet med [Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)och [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Principer för gästkonfiguration måste **alltid** tilldelas med hjälp av det initiativ som kombinerar _principerna AuditIfNotExists_ och _DeployIfNotExists._ Om endast _AuditIfNotExists-principen_ har tilldelats distribueras inte förutsättningarna och principen visar alltid att 0-servrar är kompatibla.

Tilldela en principdefinition med _DeployIfNotExists_ effekt kräver ytterligare en åtkomstnivå. Om du vill ge minst privilegium kan du skapa en anpassad rolldefinition som utökar **resursprincipdeltagaren**. Exemplet nedan skapar en roll med namnet **Resource Policy Contributor DINE** med ytterligare behörighet _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Använda parametrar i anpassade principer för gästkonfiguration

Gästkonfiguration stöder tvingande egenskaper för en konfiguration vid körning. Den här funktionen innebär att värdena i MOF-filen i paketet inte behöver betraktas som statiska. Åsidosättningsvärdena tillhandahålls via Azure Policy och påverkar inte hur konfigurationerna skapas eller kompileras.

Cmdlets `New-GuestConfigurationPolicy` och `Test-GuestConfigurationPolicyPackage` innehåller en parameter med namnet **Parametrar**. Den här parametern tar en hashtable definition som innehåller all information om varje parameter och skapar de nödvändiga avsnitten i varje fil som används för Azure Policy-definitionen.

I följande exempel skapas en principdefinition för att granska en tjänst, där användaren väljer från en lista vid tidpunkten för principtilldelningen.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Livscykel för principen

Om du vill släppa en uppdatering av principen finns det två fält som kräver uppmärksamhet.

- **Version**: När `New-GuestConfigurationPolicy` du kör cmdleten måste du ange ett versionsnummer som är större än det som för närvarande publiceras. Egenskapen uppdaterar versionen av gästkonfigurationstilldelningen så att agenten känner igen det uppdaterade paketet.
- **contentHash**: Den här egenskapen `New-GuestConfigurationPolicy` uppdateras automatiskt av cmdleten. Det är ett hash-värde för `New-GuestConfigurationPackage`paketet som skapats av . Egenskapen måste vara `.zip` korrekt för filen du publicerar. Om bara **egenskapen contentUri** uppdateras accepterar tillägget inte innehållspaketet.

Det enklaste sättet att släppa ett uppdaterat paket är att upprepa processen som beskrivs i den här artikeln och ange ett uppdaterat versionsnummer. Den processen garanterar att alla egenskaper har uppdaterats korrekt.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Konvertera Windows Grupprincipinnehåll till Azure Policy Guest Configuration

Gästkonfiguration, när du granskar Windows-datorer, är en implementering av syntaxen för PowerShell Desired State Configuration. DSC-communityn har publicerat verktyg för att konvertera exporterade grupprincipmallar till DSC-format. Genom att använda det här verktyget tillsammans med cmdletsen Gästkonfiguration som beskrivs ovan kan du konvertera Windows Grupprincipinnehåll och paketera/publicera det för Azure-princip för granskning. Mer information om hur du använder verktyget finns i artikeln [Snabbstart: Konvertera grupprincip till DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
När innehållet har konverterats är stegen ovan för att skapa ett paket och publicera det som Azure Policy desamma som för alla DSC-innehåll.

## <a name="optional-signing-guest-configuration-packages"></a>Valfritt: Signering gästkonfigurationspaket

Anpassade principer för gästkonfiguration använder SHA256-hash för att verifiera att princippaketet inte har ändrats.
Alternativt kan kunder också använda ett certifikat för att signera paket och tvinga tillägget Gästkonfiguration att endast tillåta signerat innehåll.

Om du vill aktivera det här scenariot finns det två steg som du måste slutföra. Kör cmdleten för att signera innehållspaketet och lägg till en tagg till de datorer som ska kräva att kod signeras.

Om du vill använda funktionen `Protect-GuestConfigurationPackage` Signaturverifiering kör du cmdleten för att signera paketet innan det publiceras. Den här cmdleten kräver ett code signing-certifikat.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametrar för `Protect-GuestConfigurationPackage` cmdlet:

- **Sökväg**: Fullständig sökväg för gästkonfigurationspaketet.
- **Certifikat**: Kodsigneringscertifikat för att signera paketet. Den här parametern stöds bara när du signerar innehåll för Windows.

GuestConfiguration agent förväntar sig att certifikatet offentlig nyckel ska finnas i "Trusted Root Certificate Authorities" på Windows-datorer och i sökvägen `/usr/local/share/ca-certificates/extra` på Linux-datorer. Installera den offentliga certifikatnyckeln på datorn innan den anpassade principen används för att verifiera signerat innehåll. Den här processen kan göras med valfri teknik i den virtuella datorn eller med hjälp av Azure Policy. Här finns [en](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)exempelmall .
Åtkomstprincipen för Key Vault måste göra det möjligt för beräkningsresursprovidern att komma åt certifikat under distributioner. Detaljerade steg finns i [Konfigurera Nyckelvalv för virtuella datorer i Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Följande är ett exempel för att exportera den offentliga nyckeln från ett signeringscertifikat, för att importera till datorn.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

När innehållet har publicerats lägger `GuestConfigPolicyCertificateValidation` du `enabled` till en tagg med namn och värde på alla virtuella datorer där kodsignering ska krävas. Se [taggexempel](../samples/built-in-policies.md#tags) för hur taggar kan levereras i stor skala med Azure Policy. När den här taggen `New-GuestConfigurationPolicy` är på plats aktiveras kravet via tillägget Gästkonfiguration.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Felsöka tilldelningar av gästkonfigurationsprinciper (förhandsversion)

Ett verktyg finns i förhandsversionen för att hjälpa till att felsöka Azure Policy Guest Configuration-tilldelningar. Verktyget är i förhandsgranskning och har publicerats i PowerShell Gallery som modulnamn [Gästkonfiguration Felsökare](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Om du vill ha mer information om cmdlets i det här verktyget kan du använda kommandot Hämta hjälp i PowerShell för att visa den inbyggda vägledningen. Eftersom verktyget får frekventa uppdateringar, det är det bästa sättet att få senaste informationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om granskning av virtuella datorer med [gästkonfiguration](../concepts/guest-configuration.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](get-compliance-data.md).
