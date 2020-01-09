---
title: Så här skapar du principer för gäst konfiguration
description: Lär dig hur du skapar en Azure Policy princip för gäst konfiguration för virtuella Windows-eller Linux-datorer med Azure PowerShell.
ms.date: 12/16/2019
ms.topic: how-to
ms.openlocfilehash: f2e611998e42510eccde64ff6f945f58133fc4e9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608532"
---
# <a name="how-to-create-guest-configuration-policies"></a>Så här skapar du principer för gäst konfiguration

I gäst konfigurationen används en resurs modell för [önskad tillstånds konfiguration](/powershell/scripting/dsc/overview/overview) (DSC) för att skapa konfigurationen för granskning av Azure-datorer. DSC-konfigurationen definierar det villkor som datorn ska ha. Om utvärderingen av konfigurationen Miss lyckas utlöses **auditIfNotExists** för princip inställningen och datorn betraktas som **icke-kompatibel**.

[Azure policy gäst konfiguration](../concepts/guest-configuration.md) kan bara användas för att granska inställningar i datorer. Reparationen av inställningar i datorer är inte tillgänglig ännu.

Använd följande åtgärder för att skapa en egen konfiguration för att verifiera statusen för en Azure-dator.

> [!IMPORTANT]
> Anpassade principer med gäst konfiguration är en förhands gransknings funktion.

## <a name="add-the-guestconfiguration-resource-module"></a>Lägga till GuestConfiguration Resource module

Om du vill skapa en princip för gäst konfiguration måste du lägga till modulen resurs. Den här modulen kan användas med lokalt installerad PowerShell, med [Azure Cloud Shell](https://shell.azure.com)eller med [Azure PowerShell Core Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> När modulen **GuestConfiguration** fungerar i ovanstående miljöer, måste stegen för att kompilera en DSC-konfiguration slutföras i Windows PowerShell 5,1.

### <a name="base-requirements"></a>Grundläggande krav

Resurs modulen för gäst konfiguration kräver följande program vara:

- PowerShell. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 eller högre. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Installera modulen

I gäst konfigurationen används modulen **GuestConfiguration** för att skapa DSC-konfigurationer och publicera dem på Azure policy:

1. Kör följande kommando från en PowerShell-kommandotolk:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Verifiera att modulen har importer ATS:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Skapa anpassad konfiguration och resurser för gäst konfiguration

Det första steget för att skapa en anpassad princip för gäst konfiguration är att skapa DSC-konfigurationen. En översikt över DSC-begrepp och terminologi finns i [Översikt över POWERSHELL DSC](/powershell/scripting/dsc/overview/overview).

Om konfigurationen bara kräver resurser som är inbyggda med konfigurations agenten för gäst måste du bara redigera en MOF-fil för konfiguration. Om du behöver köra ytterligare skript måste du redigera en anpassad resurspool.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Krav för anpassade gäst konfigurations resurser

När gäst konfigurationen granskar en dator körs den först `Test-TargetResource` för att avgöra om den är i rätt tillstånd. Det booleska värde som returneras av funktionen avgör om Azure Resource Managers status för gäst tilldelningen ska vara kompatibel/inte kompatibel. Om det booleska värdet `$false` för en resurs i konfigurationen körs providern `Get-TargetResource`. Om det booleska värdet `$true`, anropas `Get-TargetResource` inte.

#### <a name="configuration-requirements"></a>Konfigurations krav

Det enda kravet på att gäst konfigurationen ska använda en anpassad konfiguration är att namnet på konfigurationen ska vara konsekvent överallt där den används.  Detta inkluderar namnet på. zip-filen för innehålls paketet, konfigurations namnet i MOF-filen som lagras i innehålls paketet och konfigurations namnet som används i ARM som gäst tilldelnings namn.

#### <a name="get-targetresource-requirements"></a>Get-TargetResource-krav

Funktionen `Get-TargetResource` har särskilda krav för gäst konfiguration som inte behövs för Windows önskad tillstånds konfiguration.

- Den hash-tabellen som returneras måste innehålla en egenskap med namnet **skäl**.
- Egenskapen orsaker måste vara en matris.
- Varje objekt i matrisen ska vara en hash med nycklar med namnet **kod** och **fras**.

Egenskapen orsaker används av tjänsten för att standardisera hur information presenteras när en dator är inkompatibel. Du kan tänka på varje objekt av anledningen som en "orsak" som resursen inte är kompatibel. Egenskapen är en matris eftersom en resurs inte kan vara kompatibel med fler än en orsak.

Egenskaps **koden** och **frasen** förväntas av tjänsten. När du redigerar en anpassad resurs ställer du in texten (vanligt vis STDOUT) som du vill visa som orsak till att resursen inte är kompatibel med **frasens**värde. **Koden** har särskilda krav på formatering så att rapporter tydligt kan visa information om den resurs som användes för att utföra granskningen. Den här lösningen gör att gäst konfigurationen är utöknings bar. Alla kommandon kan köras för att granska en dator så länge utdata kan fångas och returneras som ett sträng värde för egenskapen **fras** .

- **Code** (sträng): namnet på resursen, upprepas och sedan ett kort namn utan blank steg som en identifierare för orsaken. Dessa tre värden ska vara kolon-avgränsade utan blank steg.
  - Ett exempel är `registry:registry:keynotpresent`
- **Fras** (sträng): läslig text som förklarar varför inställningen inte är kompatibel.
  - Ett exempel är `The registry key $key is not present on the machine.`

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

#### <a name="scaffolding-a-guest-configuration-project"></a>Ramverk ett gäst konfigurations projekt

För utvecklare som vill påskynda processen med att komma igång och arbeta med exempel kod, finns det ett community-projekt med namnet **gäst konfigurations projekt** som mall för den [Gipsbaserade](https://github.com/powershell/plaster) PowerShell-modulen. Det här verktyget kan användas för att Autogenerera ett projekt, inklusive en fungerande konfiguration och exempel resurs, och en uppsättning [pester](https://github.com/pester/pester) -tester för att verifiera projektet. Mallen innehåller också uppgifts utlöpare för Visual Studio Code för att automatisera skapandet och valideringen av gäst konfigurations paketet. Mer information finns i projektet GitHub Project [Guest Configuration](https://github.com/microsoft/guestconfigurationproject).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Anpassad konfiguration av gäst konfiguration på Linux

DSC-konfigurationen för gäst konfiguration i Linux använder `ChefInSpecResource` resurs för att tillhandahålla motorn namnet på den [Inspeca](https://www.chef.io/inspec/) definitionen för chef. **Name** är den enda obligatoriska resurs egenskapen.

I följande exempel skapas en konfiguration med namnet **baseline**, importerar **GuestConfiguration** -modulen och använder `ChefInSpecResource` resurs uppsättning namnet på den inspeca definitionen för **linux-patch-bas linje**:

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Mer information finns i [skriva, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Anpassad konfiguration av gäst konfiguration i Windows

DSC-konfigurationen för Azure Policy gäst konfiguration används endast av gäst konfigurations agenten, den inte står i konflikt med önskad tillstånds konfiguration i Windows PowerShell.

I följande exempel skapas en konfiguration med namnet **AuditBitLocker**, importerar modulen **GuestConfiguration** och använder `Service` resurs för att granska en tjänst som körs:

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Mer information finns i [skriva, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Skapa anpassat princip paket för gäst konfiguration

När MOF-filen kompileras måste de stödfiler paketeras tillsammans. Det slutförda paketet används av gäst konfigurationen för att skapa Azure Policy-definitioner. Paketet består av:

- Den kompilerade DSC-konfigurationen som en MOF
- Mappen moduler
  - GuestConfiguration-modul
  - DscNativeResources-modul
  - Linux En mapp med definitionen av chefs inspecen och ytterligare innehåll
  - Aktivitets DSC-resurs-moduler som inte är inbyggda

`New-GuestConfigurationPackage` cmdlet skapar paketet. Följande format används för att skapa ett anpassat paket:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Parametrar för `New-GuestConfigurationPackage`-cmdlet:

- **Namn**: namn på gäst konfigurations paket.
- **Konfiguration**: den fullständiga sökvägen till det kompilerade DSC-konfigurationsobjektet.
- **Sökväg**: sökväg till utmatnings katalog. Den här parametern är valfri. Om det inte anges skapas paketet i den aktuella katalogen.
- **ChefProfilePath**: fullständig sökväg till INSPEC-profil. Den här parametern stöds bara när du skapar innehåll för att granska Linux.

Det färdiga paketet måste lagras på en plats som de hanterade virtuella datorerna kan komma åt. Exempel är GitHub-databaser, Azure-lagrings platsen eller Azure Storage. Om du inte vill att paketet ska vara offentligt kan du ta med en [SAS-token](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) i URL: en.
Du kan också implementera [tjänstens slut punkt](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) för datorer i ett privat nätverk, även om den här konfigurationen endast gäller för åtkomst till paketet och inte kommunicerar med tjänsten.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Arbeta med hemligheter i gäst konfigurations paket

I Azure Policy gäst konfiguration är det bästa sättet att hantera hemligheter som används vid körning att lagra dem i Azure Key Vault. Den här designen implementeras i anpassade DSC-resurser.

1. Börja med att skapa en användardefinierad hanterad identitet i Azure.

   Identiteten används av datorer för att komma åt hemligheter lagrade i Key Vault. Detaljerade anvisningar finns i [skapa, lista eller ta bort en användardefinierad hanterad identitet med hjälp av Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

1. Skapa en Key Vault-instans.

   Detaljerade anvisningar finns i [Ange och hämta en hemlighet – PowerShell](../../../key-vault/quick-create-powershell.md).
   Tilldela behörighet till instansen för att ge den användare som tilldelats åtkomst till hemligheter som lagras i Key Vault. Detaljerade anvisningar finns i [Ange och hämta en hemlighet-.net](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

1. Tilldela den användare som tilldelats identiteten till din dator.

   Detaljerade anvisningar finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
   I skala tilldelar du den här identiteten med Azure Resource Manager via Azure Policy. Detaljerade anvisningar finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av en mall](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

1. I din anpassade resurs använder du slutligen det klient-ID som genererades ovan för att få åtkomst Key Vault med hjälp av den token som är tillgänglig från datorn.

   `client_id` och URL: en till Key Vault-instansen kan skickas till resursen som [Egenskaper](/powershell/scripting/dsc/resources/authoringresourcemof#creating-the-mof-schema) så att resursen inte behöver uppdateras i flera miljöer eller om värdena behöver ändras.

Följande kod exempel kan användas i en anpassad resurs för att hämta hemligheter från Key Vault med hjälp av en användardefinierad identitet. Värdet som returnerades från begäran till Key Vault är oformaterad text. Vi rekommenderar att du lagrar dem i ett Credential-objekt.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Testa ett gäst konfigurations paket

När du har skapat konfigurations paketet, men innan du publicerar det till Azure, kan du testa paketets funktioner från din arbets Station eller CI/CD-miljö. Modulen GuestConfiguration innehåller en cmdlet `Test-GuestConfigurationPackage` som läser in samma agent i utvecklings miljön som används i Azure-datorer. Med den här lösningen kan du utföra integrerings testning lokalt innan du släpper till fakturerade test/frågor/produktions miljöer.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Parametrar för `Test-GuestConfigurationPackage`-cmdlet:

- **Namn**: princip namn för gäst konfiguration.
- **Parameter**: princip parametrar har angetts i hash-format.
- **Sökväg**: fullständig sökväg till gäst konfigurations paketet.

Cmdleten stöder även inmatade från PowerShell-pipeline. Skicka in utdata från `New-GuestConfigurationPackage` cmdlet till `Test-GuestConfigurationPackage`-cmdleten.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Mer information om hur du testar med parametrar finns i avsnittet nedan [använda parametrar i anpassade principer för gäst konfiguration](#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Skapa distributions filerna för Azure Policy definition och initiativ

När ett anpassat princip paket för gäst konfiguration har skapats och laddats upp till en plats som är tillgängligt för datorerna skapar du princip definitionen för gäst konfiguration för Azure Policy. `New-GuestConfigurationPolicy`-cmdleten tar ett offentligt tillgängligt anpassat princip paket för gäst konfiguration och skapar en princip definition för **auditIfNotExists** och **deployIfNotExists** . En definition av princip initiativ som inkluderar båda princip definitionerna skapas också.

I följande exempel skapas princip-och initiativ definitioner i en angiven sökväg från ett anpassat princip paket för gäst konfiguration för Windows och ger ett namn, en beskrivning och en version:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Parametrar för `New-GuestConfigurationPolicy`-cmdlet:

- **ContentUri**: offentlig http (s) URI för innehålls paketet för gäst konfiguration.
- **DisplayName**: principens visnings namn.
- **Beskrivning**: princip beskrivning.
- **Parameter**: princip parametrar har angetts i hash-format.
- **Version**: princip version.
- **Sökväg**: mål Sök väg där princip definitioner skapas.
- **Plattform**: mål plattform (Windows/Linux) för gäst konfigurations princip och innehålls paket.

Följande filer skapas av `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiativ. JSON**

Cmdlet-utdata returnerar ett objekt som innehåller initiativets visnings namn och sökväg.

Om du vill använda det här kommandot för att Autogenerera ett anpassat princip projekt kan du göra ändringar i de här filerna. Ett exempel är att ändra avsnittet IF för att utvärdera om det finns en speciell tagg för datorer. Mer information om hur du skapar principer finns i [program mässigt skapa principer](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Använda parametrar i anpassade gäst konfigurations principer

Gäst konfiguration stöder åsidosättande egenskaper för en konfiguration vid körning. Den här funktionen innebär att värdena i MOF-filen i paketet inte måste betraktas som statiska. Värdena för åsidosättningar tillhandahålls via Azure Policy och påverkar inte hur konfigurationerna skapas eller kompileras.

Cmdletarna `New-GuestConfigurationPolicy` och `Test-GuestConfigurationPolicyPackage` innehåller en parameter med namnet **Parameters**. Den här parametern tar en hash-definition inklusive all information om varje parameter och skapar automatiskt alla nödvändiga avsnitt i de filer som används för att skapa varje Azure Policy definition.

I följande exempel skapas en Azure Policy för granskning av en tjänst där användaren väljer från en lista över tjänster vid tidpunkten för princip tilldelningen.

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

För Linux-principer inkluderar du egenskapen **AttributesYmlContent** i konfigurationen och skriver över värdena enligt dessa. Konfigurations agenten för gäster skapar automatiskt den YaML-fil som används av INSPEC för att lagra attribut. Se exemplet nedan.

```powershell
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Lägg till en hash-tabell för varje ytterligare parameter i matrisen. I principfiler visas egenskaper som lagts till i configurationName för gäst konfiguration och som identifierar resurs typ, namn, egenskap och värde.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Publicera till Azure Policy

I **GuestConfiguration** Resource module kan du skapa båda princip definitionerna och initiativ definitionen i Azure med ett steg genom `Publish-GuestConfigurationPolicy` cmdleten.
Cmdleten har bara **Sök vägs** parametern som pekar på platsen för de tre JSON-filer som skapas av `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` cmdleten accepterar sökvägen från PowerShell-pipeline. Den här funktionen innebär att du kan skapa principfiler och publicera dem i en enda uppsättning skickas-kommandon.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Med de princip-och initiativ definitioner som skapats i Azure är det sista steget att tilldela initiativet. Se hur du tilldelar initiativet till [portalen](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)och [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Principer för gäst konfiguration måste **alltid** tilldelas med det initiativ som kombinerar principerna _AuditIfNotExists_ och _DeployIfNotExists_ . Om endast _AuditIfNotExists_ -principen tilldelas distribueras kraven och principen visar alltid att "0"-servrar är kompatibla.

## <a name="policy-lifecycle"></a>Princip livs cykel

När du har publicerat en anpassad Azure Policy med det anpassade innehålls paketet finns det två fält som måste uppdateras om du vill publicera en ny version.

- **Version**: när du kör `New-GuestConfigurationPolicy`-cmdlet måste du ange ett versions nummer som är större än det som för närvarande är publicerat. Egenskapen uppdaterar versionen av gäst konfigurations tilldelningen i den nya princip filen så att tillägget identifierar att paketet har uppdaterats.
- **contentHash**: den här egenskapen uppdateras automatiskt av `New-GuestConfigurationPolicy`-cmdleten. Det är ett hash-värde för paketet som skapats av `New-GuestConfigurationPackage`. Egenskapen måste vara korrekt för den `.zip` fil som du publicerar. Om endast **contentUri** -egenskapen uppdateras, till exempel i det fall där någon kan göra en manuell ändring i princip definitionen från portalen, accepterar inte tillägget innehålls paketet.

Det enklaste sättet att frigöra ett uppdaterat paket är att upprepa processen som beskrivs i den här artikeln och ange ett uppdaterat versions nummer. Den processen garanterar att alla egenskaper har uppdaterats korrekt.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Konvertera Windows grupprincip-innehåll till Azure Policy gäst konfiguration

Gäst konfiguration, vid granskning av Windows-datorer, är en implementering av PowerShell-syntaxen för önskad tillstånds konfiguration. DSC-communityn har publicerat verktyg för att konvertera exporterade grupprincip mallar till DSC-format. Genom att använda det här verktyget tillsammans med de konfigurations-cmdletar för gäst som beskrivs ovan kan du konvertera Windows grupprincip-innehåll och paket/publicera det för att Azure Policy granska. Mer information om hur du använder verktyget finns i artikeln [snabb start: konvertera Grupprincip till DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
När innehållet har konverterats är stegen ovan för att skapa ett paket och publicera det som Azure Policy är samma som för alla DSC-innehåll.

## <a name="optional-signing-guest-configuration-packages"></a>Valfritt: signering av gäst konfigurations paket

Anpassade principer för gäst konfiguration använder SHA256 hash för att verifiera att princip paketet inte har ändrats från när det publicerades till när det lästes av servern som granskas.
Kunder kan också använda ett certifikat för att signera paket och tvinga gäst konfigurations tillägget att bara tillåta signerat innehåll.

Det finns två steg som du måste utföra för att aktivera det här scenariot. Kör cmdleten för att signera innehålls paketet och Lägg till en tagg till de datorer som kräver att kod signeras.

Om du vill använda funktionen för signaturverifiering kör du `Protect-GuestConfigurationPackage` cmdlet för att signera paketet innan det publiceras. Denna cmdlet kräver ett certifikat för kod signering.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametrar för `Protect-GuestConfigurationPackage`-cmdlet:

- **Sökväg**: fullständig sökväg till gäst konfigurations paketet.
- **Certifikat**: kod signerings certifikat för att signera paketet. Den här parametern stöds bara vid signering av innehåll för Windows.
- **PrivateGpgKeyPath**: sökväg för privat GPG-nyckel. Den här parametern stöds bara när du signerar innehåll för Linux.
- **PublicGpgKeyPath**: offentlig GPG-nyckel Sök väg. Den här parametern stöds bara när du signerar innehåll för Linux.

GuestConfiguration-agenten förväntar sig att certifikatets offentliga nyckel finns i "betrodda rot certifikat utfärdare" på Windows-datorer och i sökvägen `/usr/local/share/ca-certificates/extra` på Linux-datorer. För noden för att verifiera signerat innehåll installerar du certifikatets offentliga nyckel på datorn innan du tillämpar den anpassade principen. Den här processen kan utföras med hjälp av valfri teknik i den virtuella datorn eller med hjälp av Azure Policy. [Här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)finns en exempel mall.
Principen för Key Vault åtkomst måste tillåta att beräknings resurs leverantören får åtkomst till certifikat under distributioner. Detaljerade anvisningar finns i [konfigurera Key Vault för virtuella datorer i Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Följande är ett exempel för att exportera den offentliga nyckeln från ett signerings certifikat som ska importeras till datorn.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

En referens för att skapa GPG-nycklar som ska användas med Linux-datorer finns i en artikel på GitHub, vilket [genererar en ny GPG-nyckel](https://help.github.com/en/articles/generating-a-new-gpg-key).

När innehållet har publicerats lägger du till en tagg med namnet `GuestConfigPolicyCertificateValidation` och värde `enabled` till alla virtuella datorer där kod signering ska krävas. Den här taggen kan levereras i stor skala med hjälp av Azure Policy. Se [Apply-taggen och dess standardvärde](../samples/apply-tag-default-value.md) -exempel. När den här taggen är på plats, aktiverar princip definitionen som genereras med hjälp av `New-GuestConfigurationPolicy` cmdleten krav via gäst konfigurations tillägget.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Fel sökning av princip tilldelningar för gäst konfiguration (för hands version)

Ett verktyg är tillgängligt i för hands versionen för att hjälpa till med fel sökning Azure Policy gäst konfigurations tilldelningar. Verktyget är i för hands version och har publicerats till PowerShell-galleriet som Modulnamn [fel sökning av gäst konfiguration](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Mer information om cmdletarna i det här verktyget får du genom att använda kommandot Get-Help i PowerShell för att visa den inbyggda vägledningen. När verktyget uppdateras ofta är det bästa sättet att hämta den senaste informationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att granska virtuella datorer med [gäst konfiguration](../concepts/guest-configuration.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](get-compliance-data.md).
