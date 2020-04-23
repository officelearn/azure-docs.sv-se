---
title: Så här skapar du principer för gästkonfiguration för Linux
description: Lär dig hur du skapar en Azure Policy Guest Configuration policy för Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024990"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Så här skapar du principer för gästkonfiguration för Linux

Innan du skapar anpassade principer kan du läsa översiktsinformationen på [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Mer information om hur du skapar principer för gästkonfiguration för Windows finns på sidan [Så här skapar du principer för gästkonfiguration för Windows](./guest-configuration-create.md)

Vid granskning av Linux använder Gästkonfiguration [Chef InSpec](https://www.inspec.io/). InSpec-profilen definierar det villkor som maskinen ska vara i. Om utvärderingen av konfigurationen misslyckas utlöses **principeffektgranskningenIfNotExists** och datorn anses **vara icke-kompatibel**.

[Azure Policy Guest Configuration](../concepts/guest-configuration.md) kan endast användas för att granska inställningar inuti datorer. Reparation av inställningar inuti datorer är ännu inte tillgänglig.

Använd följande åtgärder för att skapa din egen konfiguration för att validera tillståndet för en Azure- eller icke-Azure-dator.

> [!IMPORTANT]
> Anpassade principer med gästkonfiguration är en förhandsgranskningsfunktion.
>
> Tillägget Gästkonfiguration krävs för att utföra granskningar i virtuella Azure-datorer.
> Om du vill distribuera tillägget i stor skala över alla Linux-datorer tilldelar du följande principdefinition:
>   - [Distribuera förutsättningar för att aktivera princip för gästkonfiguration på virtuella Linux-datorer.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Att skapa en gästkonfigurationsartefakt, automatiserad testning av artefakten, skapa en principdefinition och publicera principen kan automatiseras helt med hjälp av modulen Gästkonfiguration i PowerShell. Modulen kan installeras på en dator som kör Windows, macOS eller Linux med PowerShell 6.2 eller senare körs lokalt, eller med [Azure Cloud Shell](https://shell.azure.com), eller med Azure [PowerShell Core Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilering av konfigurationer stöds inte på Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Gästkonfigurationsartefakter och princip för Linux

Även i Linux-miljöer använder gästkonfiguration önskad tillståndskonfiguration som språkabstraktion. Implementeringen är baserad i inbyggd kod (C++) så att den inte kräver att PowerShell läses in. Det kräver dock en konfiguration MOF som beskriver information om miljön. DSC fungerar som ett omslag för InSpec för att standardisera hur det körs, hur parametrar tillhandahålls och hur utdata returneras till tjänsten. Lite kunskap om DSC krävs när man arbetar med anpassat InSpec-innehåll.

#### <a name="configuration-requirements"></a>Konfigurationskrav

Namnet på den anpassade konfigurationen måste vara konsekvent överallt. Namnet på ZIP-filen för innehållspaketet, konfigurationsnamnet i MOF-filen och gästtilldelningsnamnet i Resource Manager-mallen måste vara detsamma.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Anpassad konfiguration av gästkonfiguration på Linux

Gästkonfiguration på Linux `ChefInSpecResource` använder resursen för att förse motorn med namnet på [InSpec-profilen](https://www.inspec.io/docs/reference/profiles/). **Namn** är den enda obligatoriska resursegenskapen. Skapa en YaML-fil och en Ruby-skriptfil, enligt beskrivningen nedan.

Skapa först YaML-filen som används av InSpec. Filen innehåller grundläggande information om miljön. Ett exempel ges nedan:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Spara filen med `inspec.yml` namnet i `linux-path` en mapp som heter i projektkatalogen.

Skapa sedan Ruby-filen med inSpec-språkabstraktionen som används för att granska datorn.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Spara filen med `linux-path.rb` namnet i `controls` en `linux-path` ny mapp som heter i katalogen.

Skapa slutligen en konfiguration, importera **resursmodulen PSDesiredStateConfiguration** och kompilera konfigurationen.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Spara filen med `config.ps1` namnet i projektmappen. Kör den i PowerShell `./config.ps1` genom att köra i terminalen. En ny mof-fil kommer att skapas.

Kommandot `Node AuditFilePathExists` är inte tekniskt krävs, men `AuditFilePathExists.mof` det ger en `localhost.mof`fil som heter snarare än standard, . Med .mof filnamnet följa konfigurationen gör det enkelt att organisera många filer när du arbetar i stor skala.



Du bör nu ha en projektstruktur enligt nedan:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Stödfilerna måste paketeras tillsammans. Det slutförda paketet används av Gästkonfiguration för att skapa Azure-principdefinitioner.

Cmdlet `New-GuestConfigurationPackage` skapar paketet. Parametrar för `New-GuestConfigurationPackage` cmdlet när du skapar Linux-innehåll:

- **Namn**: Paketnamn för gästkonfiguration.
- **Konfiguration**: Kompilerat konfigurationsdokument fullständig sökväg.
- **Sökväg**: Sökväg för utdatamappen. Den här parametern är valfri. Om inget anges skapas paketet i aktuell katalog.
- **ChefProfilePath**: Fullständig sökväg till InSpec-profil. Den här parametern stöds endast när du skapar innehåll för att granska Linux.

Kör följande kommando för att skapa ett paket med hjälp av konfigurationen som angavs i föregående steg:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

När du har skapat konfigurationspaketet men innan du publicerar det på Azure kan du testa paketet från din arbetsstation eller CI/CD-miljö. Cmdlet `Test-GuestConfigurationPackage` för GuestConfiguration innehåller samma agent i utvecklingsmiljön som används i Azure-datorer. Med den här lösningen kan du utföra integrationstestning lokalt innan du släpper till fakturerade molnmiljöer.

Eftersom agenten faktiskt utvärderar den lokala miljön måste du i de flesta fall köra test-cmdlet på samma OS-plattform som du planerar att granska.

Parametrar för `Test-GuestConfigurationPackage` cmdlet:

- **Namn**: Principnamn för gästkonfiguration.
- **Parameter**: Principparametrar som anges i hashtable-format.
- **Sökväg**: Fullständig sökväg för gästkonfigurationspaketet.

Kör följande kommando för att testa paketet som skapats av föregående steg:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Cmdlet stöder också indata från PowerShell-pipelinen. Rör utgången `New-GuestConfigurationPackage` av cmdlet till `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Följande filer skapas `New-GuestConfigurationPolicy`av:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiativ.json**

Cmdlet-utdata returnerar ett objekt som innehåller initiativets visningsnamn och sökväg för principfilerna.

Publicera slutligen principdefinitionerna med `Publish-GuestConfigurationPolicy` cmdleten.
Cmdleten har bara parametern **Path** som pekar på platsen `New-GuestConfigurationPolicy`för JSON-filerna som skapats av .

Om du vill köra kommandot Publicera behöver du åtkomst för att skapa principer i Azure. De specifika auktoriseringskraven dokumenteras på sidan [Översikt över Azure-princip.](../overview.md) Den bästa inbyggda rollen är **Resursprincipdeltagare**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Cmdlet `Publish-GuestConfigurationPolicy` accepterar sökvägen från PowerShell-pipelinen. Den här funktionen innebär att du kan skapa principfilerna och publicera dem i en enda uppsättning kommandon med pipade.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Med InSpec hanteras parametrar vanligtvis som indata antingen vid körning eller som kod med hjälp av attribut. Gästkonfiguration fördunklar den här processen så att indata kan tillhandahållas när principen har tilldelats. En attributfil skapas automatiskt i datorn. Du behöver inte skapa och lägga till en fil i projektet. Det finns två steg för att lägga till parametrar i ditt Linux-granskningsprojekt.

Definiera indata i Ruby-filen där du skript vad du ska granska på datorn. Ett exempel ges nedan.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdlets `New-GuestConfigurationPolicy` och `Test-GuestConfigurationPolicyPackage` innehåller en parameter med namnet **Parametrar**. Den här parametern tar en hashtable inklusive all information om varje parameter och skapar automatiskt alla nödvändiga avsnitt av filerna som används för att skapa varje Azure Princip definition.

I följande exempel skapas en principdefinition för att granska en filsökväg, där användaren tillhandahåller sökvägen vid tidpunkten för principtilldelningen.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

För Linux-principer, inkludera egenskapen **AttributYmlContent** i din konfiguration och skriva över värdena efter behov. Gästkonfigurationsagenten skapar automatiskt YAML-filen som används av InSpec för att lagra attribut. Se exemplet nedan.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Livscykel för principen

Om du vill släppa en uppdatering av principdefinitionen finns det två fält som kräver uppmärksamhet.

- **Version**: När `New-GuestConfigurationPolicy` du kör cmdleten måste du ange ett versionsnummer som är större än det som för närvarande publiceras. Egenskapen uppdaterar versionen av gästkonfigurationstilldelningen så att agenten känner igen det uppdaterade paketet.
- **contentHash**: Den här egenskapen `New-GuestConfigurationPolicy` uppdateras automatiskt av cmdleten. Det är ett hash-värde för `New-GuestConfigurationPackage`paketet som skapats av . Egenskapen måste vara `.zip` korrekt för filen du publicerar. Om bara **egenskapen contentUri** uppdateras accepterar tillägget inte innehållspaketet.

Det enklaste sättet att släppa ett uppdaterat paket är att upprepa processen som beskrivs i den här artikeln och ange ett uppdaterat versionsnummer. Den processen garanterar att alla egenskaper har uppdaterats korrekt.

## <a name="optional-signing-guest-configuration-packages"></a>Valfritt: Signering gästkonfigurationspaket

Anpassade principer för gästkonfiguration använder SHA256-hash för att verifiera att princippaketet inte har ändrats.
Alternativt kan kunder också använda ett certifikat för att signera paket och tvinga tillägget Gästkonfiguration att endast tillåta signerat innehåll.

Om du vill aktivera det här scenariot finns det två steg som du måste slutföra. Kör cmdleten för att signera innehållspaketet och lägg till en tagg till de datorer som ska kräva att kod signeras.

Om du vill använda funktionen `Protect-GuestConfigurationPackage` Signaturverifiering kör du cmdleten för att signera paketet innan det publiceras. Den här cmdleten kräver ett code signing-certifikat.

Parametrar för `Protect-GuestConfigurationPackage` cmdlet:

- **Sökväg**: Fullständig sökväg för gästkonfigurationspaketet.
- **PublicGpgKeyPath**: Offentlig GPG-nyckelsökväg. Den här parametern stöds bara när du signerar innehåll för Linux.

En bra referens för att skapa GPG nycklar att använda med Linux-maskiner tillhandahålls av en artikel om GitHub, [Generera en ny GPG-nyckel](https://help.github.com/en/articles/generating-a-new-gpg-key).

GuestConfiguration agent förväntar sig att certifikatet `/usr/local/share/ca-certificates/extra` offentlig nyckel ska finnas i sökvägen på Linux-datorer. Installera den offentliga certifikatnyckeln på datorn innan den anpassade principen används för att verifiera signerat innehåll. Den här processen kan göras med valfri teknik i den virtuella datorn eller med hjälp av Azure Policy. Här finns [en](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)exempelmall .
Åtkomstprincipen för Key Vault måste göra det möjligt för beräkningsresursprovidern att komma åt certifikat under distributioner. Detaljerade steg finns i [Konfigurera Nyckelvalv för virtuella datorer i Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

När innehållet har publicerats lägger `GuestConfigPolicyCertificateValidation` du `enabled` till en tagg med namn och värde på alla virtuella datorer där kodsignering ska krävas. Se [taggexempel](../samples/built-in-policies.md#tags) för hur taggar kan levereras i stor skala med Azure Policy. När den här taggen `New-GuestConfigurationPolicy` är på plats aktiveras kravet via tillägget Gästkonfiguration.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Felsöka tilldelningar av gästkonfigurationsprinciper (förhandsversion)

Ett verktyg finns i förhandsversionen för att hjälpa till att felsöka Azure Policy Guest Configuration-tilldelningar. Verktyget är i förhandsgranskning och har publicerats i PowerShell Gallery som modulnamn [Gästkonfiguration Felsökare](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Om du vill ha mer information om cmdlets i det här verktyget kan du använda kommandot Hämta hjälp i PowerShell för att visa den inbyggda vägledningen. Eftersom verktyget får frekventa uppdateringar, det är det bästa sättet att få senaste informationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om granskning av virtuella datorer med [gästkonfiguration](../concepts/guest-configuration.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](get-compliance-data.md).
