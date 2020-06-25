---
title: Använda distributions skript i mallar | Microsoft Docs
description: Använd distributions skript i Azure Resource Manager mallar.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: jgao
ms.openlocfilehash: 6cf10ea9693f922ee8edfb6bab5d1a7cde681fcb
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339426"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Använda distributions skript i mallar (förhands granskning)

Lär dig hur du använder distributions skript i Azure Resource templates. Med en ny resurs typ som kallas kan `Microsoft.Resources/deploymentScripts` användare köra distributions skript i mallar distributioner och granska körnings resultat. Dessa skript kan användas för att utföra anpassade steg som:

- lägga till användare i en katalog
- utför data Plans åtgärder, till exempel kopiera blobbar eller Dirigerings databas
- Leta upp och validera en licens nyckel
- skapa ett självsignerat certifikat
- skapa ett objekt i Azure AD
- Leta upp IP-adressblock från det anpassade systemet

Fördelarna med distributions skript:

- Enkelt att koda, använda och felsöka. Du kan utveckla distributions skript i dina favorit utvecklings miljöer. Skripten kan bäddas in i mallar eller i externa skriptfiler.
- Du kan ange språk och plattform för skript. För närvarande stöds Azure PowerShell-och Azure CLI-distributions skript i Linux-miljön.
- Tillåt att du anger de identiteter som används för att köra skripten. För närvarande stöds endast [Azure User-tilldelade hanterade identiteter](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Tillåt att kommando rads argument skickas till skriptet.
- Kan ange utdata för skript och skicka tillbaka dem till distributionen.

Distributions skript resursen är bara tillgänglig i de regioner där Azure Container instance är tillgängligt.  Se [resurs tillgänglighet för Azure Container instances i Azure-regioner](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Ett lagrings konto och en behållar instans krävs för skript körning och fel sökning. Du har möjlighet att ange ett befintligt lagrings konto, annars skapas lagrings kontot tillsammans med behållar instansen automatiskt av skript tjänsten. De två automatiskt skapade resurserna tas vanligt vis bort av skript tjänsten när distributions skript körningen blir i ett Terminal-tillstånd. Du debiteras för resurserna tills resurserna tas bort. Läs mer i avsnittet om hur du [säkerhetskopierar skript resurser](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Krav

- **En användardefinierad hanterad identitet med deltagar rollen till mål resurs gruppen**. Den här identiteten används för att köra distributions skript. Om du vill utföra åtgärder utanför resurs gruppen måste du bevilja ytterligare behörighet. Du kan till exempel tilldela identiteten till prenumerations nivån om du vill skapa en ny resurs grupp.

  > [!NOTE]
  > Skript tjänsten skapar ett lagrings konto (om du inte anger ett befintligt lagrings konto) och en behållar instans i bakgrunden.  En användardefinierad hanterad identitet med deltagar rollen på prenumerations nivå krävs om prenumerationen inte har registrerat Azure Storage-kontot (Microsoft. Storage) och Azure Container Instance (Microsoft. ContainerInstance)-resurs leverantörer.

  Information om hur du skapar en identitet finns i [skapa en användardefinierad hanterad identitet med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), eller med [hjälp av Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)eller [genom att använda Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Du behöver identitets-ID när du distribuerar mallen. Formatet på identiteten är:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Använd följande CLI-eller PowerShell-skript för att hämta ID: t genom att ange resurs gruppens namn och identitetens namn.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** eller **Azure CLI**. En lista över Azure PowerShell-versioner som stöds finns [här](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list); en lista över Azure CLI-versioner som stöds finns [här](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Distributions skriptet använder de tillgängliga CLI-avbildningarna från Microsoft Container Registry (MCR). Det tar ungefär en månad att certifiera en CLI-avbildning för distributions skript. Använd inte de CLI-versioner som släpptes inom 30 dagar. För att hitta versions datumen för avbildningarna, se [versions information för Azure CLI](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Om en version som inte stöds används listas de versioner som stöds i fel meddelandet.

    Du behöver inte dessa versioner för att distribuera mallar. Men de här versionerna behövs för att testa distributions skript lokalt. Se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Du kan använda en förkonfigurerad Docker-avbildning.  Se [Konfigurera utvecklings miljö](#configure-development-environment).

## <a name="sample-templates"></a>Exempelmallar

Följande JSON är ett exempel.  Du hittar det senaste mallnamnet [här](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Exemplet är i demonstrations syfte.  **scriptContent** och **primaryScriptUri** kan inte finnas i en mall.

Information om egenskaps värde:

- **Identitet**: distributions skript tjänsten använder en användardefinierad hanterad identitet för att köra skripten. För närvarande stöds endast användardefinierad hanterad identitet.
- **typ**: ange typ av skript. Azure PowerShell-och Azure CLI-skript stöds för närvarande. Värdena är **AzurePowerShell** och **AzureCLI**.
- **forceUpdateTag**: om du ändrar det här värdet mellan mallens distributioner tvingas distributions skriptet att köras igen. Använd funktionen newGuid () eller utcNow () som måste anges som defaultValue för en parameter. Mer information finns i [Kör skript mer än en gång](#run-script-more-than-once).
- **containerSettings**: Ange inställningarna för att anpassa Azure Container instance.  **containerGroupName** används för att ange behållar gruppens namn.  Om det inte anges skapas grupp namnet automatiskt.
- **storageAccountSettings**: Ange inställningarna för att använda ett befintligt lagrings konto. Om inget anges skapas ett lagrings konto automatiskt. Se [Använd ett befintligt lagrings konto](#use-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion**: Ange den version av modulen som ska användas. En lista över PowerShell-och CLI-versioner som stöds finns i [krav](#prerequisites).
- **argument**: ange parameter värden. Värdena avgränsas med blank steg.
- **environmentVariables**: Ange de miljövariabler som ska överföras till skriptet. Mer information finns i [utveckla distributions skript](#develop-deployment-scripts).
- **scriptContent**: ange skript innehållet. Använd i stället om du vill köra ett externt skript `primaryScriptUri` . Exempel finns i [använda infogat skript](#use-inline-scripts) och [Använd externt skript](#use-external-scripts).
- **primaryScriptUri**: Ange en offentligt tillgänglig URL till det primära distributions skriptet med fil namns tillägg som stöds.
- **supportingScriptUris**: Ange en matris med offentligt tillgängliga URL: er till stöd för filer som anropas i antingen `ScriptContent` eller `PrimaryScriptUri` .
- **timeout**: Ange högsta tillåtna körnings tid för skript som anges i [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**.
- **cleanupPreference**. Ange inställningen för att rensa distributions resurser när skript körningen blir i ett Terminal-tillstånd. Standardinställningen är **Always**, vilket innebär att resurserna tas bort trots att terminalens tillstånd (lyckades, misslyckades, avbröts). Mer information finns i [Rensa distribution skript resurser](#clean-up-deployment-script-resources).
- **retentionInterval**: Ange intervallet som tjänsten behåller distributions skript resurserna efter när distributions skript körningen har nått ett terminalfönster. Distributions skript resurserna tas bort när denna varaktighet upphör att gälla. Varaktigheten baseras på [ISO 8601-mönstret](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**, vilket betyder sju dagar. Den här egenskapen används när cleanupPreference är inställt på *OnExpiration*. Egenskapen *OnExpiration* har inte Aktiver ATS för närvarande. Mer information finns i [Rensa distribution skript resurser](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Ytterligare exempel

- [Exempel 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): skapa ett nyckel valv och Använd distributions skriptet för att tilldela ett certifikat till nyckel valvet.
- [Exempel 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): skapa en resurs grupp på prenumerations nivån, skapa ett nyckel valv i resurs gruppen och Använd sedan distributions skriptet för att tilldela ett certifikat till nyckel valvet.
- [Exempel 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): skapa en användardefinierad hanterad identitet, tilldela deltagar rollen till identiteten på resurs grupps nivå, skapa ett nyckel valv och Använd sedan distributions skriptet för att tilldela ett certifikat till nyckel valvet.

> [!NOTE]
> Vi rekommenderar att du skapar en användardefinierad identitet och beviljar behörigheter i förväg. Du kan få inloggnings-och behörighets relaterade fel om du skapar identiteten och beviljar behörigheter i samma mall där du kör distributions skript. Det tar lite tid innan behörigheterna börjar gälla.

## <a name="use-inline-scripts"></a>Använd infogade skript

Följande mall har en resurs definierad med `Microsoft.Resources/deploymentScripts` typen. Den markerade delen är det infogade skriptet.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Eftersom de infogade distributions skripten omges av dubbla citat tecken måste strängarna i distributions skripten undantas genom att använda en **&#92;** eller omges av enkla citat tecken. Du kan också överväga att använda sträng ersättning som det visas i föregående JSON-exempel.

Skriptet tar en parameter och matar ut parametervärdet. **DeploymentScriptOutputs** används för att lagra utdata.  I avsnittet utdata visar **värde** raden hur du kommer åt de lagrade värdena. `Write-Output`används för fel söknings syfte. Information om hur du kommer åt utdatafilen finns i [övervaka och felsöka distributions skript](#monitor-and-troubleshoot-deployment-scripts).  För egenskaps beskrivningar, se [exempel-mallar](#sample-templates).

Om du vill köra skriptet väljer du **prova** att öppna Cloud Shell och klistrar in följande kod i rutan Shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

De utdata som returneras ser ut så här:

![Distributions skript Hello World-utdata i Resource Manager-mall](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Använd externa skript

Förutom infogade skript kan du också använda externa skriptfiler. Endast primära PowerShell-skript med fil namns tillägget **ps1** stöds. För CLI-skript kan primära skript ha alla tillägg (eller utan tillägg), så länge skripten är giltiga bash-skript. Om du vill använda externa skriptfiler ersätter du `scriptContent` med `primaryScriptUri` . Till exempel:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Om du vill se ett exempel väljer du [här](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

De externa skriptfilerna måste vara tillgängliga.  Information om hur du skyddar skriptfilerna som lagras i Azure Storage-konton finns i [distribuera privat arm-mall med SAS-token](./secure-template-with-sas-token.md).

Du ansvarar för att säkerställa integriteten för skript som refereras till av distributions skriptet, antingen **PrimaryScriptUri** eller **SupportingScriptUris**.  Referera endast till skript som du litar på.

## <a name="use-supporting-scripts"></a>Använda stöd skript

Du kan separera komplicerade logiker till en eller flera stödfiler. Med `supportingScriptURI` egenskapen kan du ange en matris med URI: er till de stödda skriptfilerna vid behov:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Stöd för skriptfiler kan anropas från både infogade skript och primära skriptfiler. Stöd för skriptfiler har inga begränsningar för fil namns tillägget.

De stödfiler som kopieras kopieras till azscripts/azscriptinput vid körningen. Använd relativ sökväg för att referera till stödjande filer från infogade skript och primära skriptfiler.

## <a name="work-with-outputs-from-powershell-script"></a>Arbeta med utdata från PowerShell-skript

Följande mall visar hur du överför värden mellan två deploymentScripts-resurser:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

I den första resursen definierar du en variabel som heter **$DeploymentScriptOutputs**och använder den för att lagra värdena för utdata. Använd följande för att få åtkomst till resultatvärdet från en annan resurs i mallen:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Arbeta med utdata från CLI-skript

Det skiljer sig från PowerShell-distributions skriptet, CLI/bash-stöd ger ingen gemensam variabel för att lagra skript utdata i stället, men det finns en miljö variabel som heter **AZ_SCRIPTS_OUTPUT_PATH** som lagrar platsen där filen med skriptets utdata finns. Om ett distributions skript körs från en Resource Manager-mall, ställs den här miljövariabeln in automatiskt åt dig av bash-gränssnittet.

Utdata för distributions skript måste sparas på AZ_SCRIPTS_OUTPUT_PATH plats och utmatningarna måste vara ett giltigt JSON-String-objekt. Filens innehåll måste sparas som ett nyckel/värde-par. Till exempel lagras en sträng mat ris som {"mina resultat": ["foo", "bar"]}.  Att bara lagra mat ris resultaten, till exempel ["foo", "bar"], är ogiltigt.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) används i föregående exempel. Den levereras med behållar avbildningarna. Se [Konfigurera utvecklings miljö](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Använd befintligt lagrings konto

Ett lagrings konto och en behållar instans krävs för skript körning och fel sökning. Du har möjlighet att ange ett befintligt lagrings konto, annars skapas lagrings kontot tillsammans med behållar instansen automatiskt av skript tjänsten. Kraven för att använda ett befintligt lagrings konto:

- De typer av lagrings konton som stöds är:

    | SKU             | Typ som stöds     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Lagring, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Lagring, StorageV2 |
    | Standard_RAGRS  | Lagring, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Dessa kombinationer stöder fil resurs.  Mer information finns i [skapa en Azure-filresurs](../../storage/files/storage-how-to-create-file-share.md) och [typer av lagrings konton](../../storage/common/storage-account-overview.md).
- Brand Väggs regler för lagrings konto stöds inte än. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../../storage/common/storage-network-security.md).
- Distributions skriptets tilldelade hanterade identitet måste ha behörighet att hantera lagrings kontot, som innehåller läsa, skapa, ta bort fil resurser.

Om du vill ange ett befintligt lagrings konto lägger du till följande JSON till egenskaps elementet för `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: Ange namnet på lagrings kontot.
- **storageAccountKey "**: Ange en av lagrings konto nycklarna. Du kan använda [`listKeys()`](./template-functions-resource.md#listkeys) funktionen för att hämta nyckeln. Till exempel:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Se [exempel på mallar](#sample-templates) för ett komplett `Microsoft.Resources/deploymentScripts` definitions exempel.

När ett befintligt lagrings konto används skapar skript tjänsten en fil resurs med ett unikt namn. Se [Rensa distributions skript resurser](#clean-up-deployment-script-resources) för hur skript tjänsten rensar fil resursen.

## <a name="develop-deployment-scripts"></a>Utveckla distributions skript

### <a name="handle-non-terminating-errors"></a>Hantera icke-avslutande fel

Du kan styra hur PowerShell svarar på icke-avslutande fel med hjälp av **$ErrorActionPreference** -variabeln i distributions skriptet. Om variabeln inte anges i distributions skriptet använder skript tjänsten standardvärdet **Continue (Fortsätt**).

Skript tjänsten anger resurs etablerings statusen till **misslyckad** när skriptet påträffar ett fel trots inställningen för $ErrorActionPreference.

### <a name="pass-secured-strings-to-deployment-script"></a>Skicka skyddade strängar till distributions skript

Genom att ställa in miljövariabler (EnvironmentVariable) i behållar instanser kan du tillhandahålla dynamisk konfiguration av programmet eller skriptet som körs av behållaren. Distributions skriptet hanterar icke-skyddade och skyddade miljövariabler på samma sätt som Azure Container instance. Mer information finns i [Ange miljövariabler i container instances](../../container-instances/container-instances-environment-variables.md#secure-values).

Den högsta tillåtna storleken för miljövariabler är 64 kB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Övervaka och felsöka distributions skript

Skript tjänsten skapar ett [lagrings konto](../../storage/common/storage-account-overview.md) (om du inte anger ett befintligt lagrings konto) och en [behållar instans](../../container-instances/container-instances-overview.md) för skript körning. Om dessa resurser skapas automatiskt av skript tjänsten, har båda resurserna **azscripts** -suffixet i resurs namnen.

![Distributions skript resurs namn för Resource Manager-mall](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Användar skriptet, körnings resultaten och STDOUT-filen lagras i lagrings kontots fil resurser. Det finns en mapp med namnet **azscripts**. I mappen finns det två fler mappar för indata och utdatafilerna: **azscriptinput** och **azscriptoutput**.

Mappen utdata innehåller en **executionresult.jspå** och skript utdatafilen. Du kan se fel meddelandet för skript körning i **executionresult.jspå**. Utdatafilen skapas endast när skriptet har körts. Mappen indata innehåller en system PowerShell-skriptfil och skript filen för användar distribution. Du kan ersätta skript filen för användar distribution med en ändrad, och köra distributions skriptet på nytt från Azure Container instance.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

När du har distribuerat en distributions skript resurs visas resursen under resurs gruppen i Azure Portal. Följande skärm bild visar översikts sidan för en distributions skript resurs:

![Översikt över distribution av skript portalen för Resource Manager-mall](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

På sidan Översikt visas viktig information om resursen, till exempel **etablerings status**, **lagrings konto**, **behållar instans**och **loggar**.

På den vänstra menyn kan du Visa distributions skriptets innehåll, argumenten som skickas till skriptet och utdata.  Du kan också exportera en mall för distributions skriptet, inklusive distributions skriptet.

### <a name="use-powershell"></a>Använd PowerShell

Med hjälp av Azure PowerShell kan du hantera distributions skript på prenumerations-eller resurs grupps omfång:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): hämtar eller visar distributions skript.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): hämtar loggen för en körning av distributions skript.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): tar bort ett distributions skript och dess associerade resurser.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): sparar loggen för en distributions skript körning till disk.

Get-AzDeploymentScript-utdata liknar:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Använda Azure CLI

Med Azure CLI kan du hantera distributions skript på prenumerations-eller resurs grupps omfång:

- [AZ distribution-skript ta bort](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-delete): ta bort ett distributions skript.
- [AZ distribution – skript lista](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-list): visar alla distributions skript.
- [AZ distribution – skript Visa](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show): Hämta ett distributions skript.
- [AZ distribution – skript Visa-logg](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show-log): Visa distributions skript loggar.

Utdata för list kommandot liknar:

```json
[
  {
    "arguments": "-name 'John Dole'",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200618T194637Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0618rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts",
      "endTime": "2020-06-18T19:49:45.926522+00:00",
      "error": null,
      "expirationTime": "2020-06-19T19:49:45.926522+00:00",
      "startTime": "2020-06-18T19:46:45.667124+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts"
    },
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-18T19:46:41.363741+00:00",
      "createdBy": "someon@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-18T19:46:41.363741+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  },
```

### <a name="use-rest-api"></a>Använd REST API

Du kan hämta distributions information för distributions skriptets resurs på resurs grupps nivå och prenumerations nivå genom att använda REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

I följande exempel används [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Utdatan liknar följande:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

Utdata visar distributions status och resurs-ID för distributions skript.

Följande REST API returnerar loggen:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Den fungerar bara innan distributions skript resurserna tas bort.

Om du vill se deploymentScripts-resursen i portalen väljer du **Visa dolda typer**:

![Distributions skript för Resource Manager-mall, Visa dolda typer, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Rensa distributions skript resurser

Ett lagrings konto och en behållar instans krävs för skript körning och fel sökning. Du har möjlighet att ange ett befintligt lagrings konto, annars skapas ett lagrings konto tillsammans med en behållar instans automatiskt av skript tjänsten. De två automatiskt skapade resurserna tas bort av skript tjänsten när distributions skript körningen blir i ett Terminal-tillstånd. Du debiteras för resurserna tills resurserna tas bort. Pris informationen finns i [container instances priser](https://azure.microsoft.com/pricing/details/container-instances/) och [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

Livs cykeln för de här resurserna styrs av följande egenskaper i mallen:

- **cleanupPreference**: rensnings inställning när skript körningen får statusen Terminal. De värden som stöds är:

  - **Always**: ta bort de automatiskt skapade resurserna när skript körningen blir i ett Terminal-tillstånd. Om ett befintligt lagrings konto används, tar skript tjänsten bort fil resursen som skapats i lagrings kontot. Eftersom deploymentScripts-resursen kanske fortfarande finns när resurserna har rensats, behåller skript tjänsten skript körnings resultatet, till exempel STDOUT, utdata, retur värde osv. innan resurserna tas bort.
  - **OnSuccess**: ta bara bort de automatiskt skapade resurserna när skript körningen har slutförts. Om ett befintligt lagrings konto används tar skript tjänsten bara bort fil resursen när skript körningen har slutförts. Du kan fortfarande komma åt resurserna för att hitta felsöknings information.
  - **OnExpiration**: ta bara bort resurserna automatiskt när **retentionInterval** -inställningen har upphört att gälla. Om ett befintligt lagrings konto används, tar skript tjänsten bort fil resursen, men behåller lagrings kontot.

- **retentionInterval**: Ange det tidsintervall som en skript resurs kommer att behållas och därefter upphör att gälla och tas bort.

> [!NOTE]
> Vi rekommenderar inte att du använder lagrings kontot och behållar instansen som genereras av skript tjänsten för andra skäl. De två resurserna kan tas bort beroende på skript livs cykeln.

## <a name="run-script-more-than-once"></a>Kör skript mer än en gång

Körning av distributions skript är en idempotenta åtgärd. Om ingen av resurs egenskaperna för deploymentScripts (inklusive det infogade skriptet) ändras, utförs inte skriptet när du distribuerar mallen på annat sätt. Distributions skript tjänsten jämför resurs namnen i mallen med de befintliga resurserna i samma resurs grupp. Det finns två alternativ om du vill köra samma distributions skript flera gånger:

- Ändra namnet på din deploymentScripts-resurs. Använd till exempel funktionen [utcNow](./template-functions-date.md#utcnow) -mall som resurs namn eller som en del av resurs namnet. Om du ändrar resurs namnet skapas en ny deploymentScripts-resurs. Det är lämpligt att hålla en historik över skript körningen.

    > [!NOTE]
    > Funktionen utcNow kan bara användas i standardvärdet för en parameter.

- Ange ett annat värde i `forceUpdateTag` egenskapen Template.  Använd till exempel utcNow som värde.

> [!NOTE]
> Skriv de distributions skript som är idempotenta. Detta säkerställer att om de körs igen av misstag, kommer de inte att orsaka system ändringar. Om distributions skriptet exempelvis används för att skapa en Azure-resurs kontrollerar du att resursen inte finns innan du skapar den, så skriptet kommer att lyckas eller så skapar du inte resursen igen.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Du kan använda en förkonfigurerad Docker-behållar avbildning som utvecklings miljö för distributions skript. Information om hur du installerar Docker finns i [Hämta Docker](https://docs.docker.com/get-docker/).
Du måste också konfigurera fildelning för att montera katalogen som innehåller distributions skripten i Docker-behållaren.

1. Hämta distributions skriptets behållar avbildning till den lokala datorn:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Exemplet använder version PowerShell 2.7.0.

    Hämta en CLI-avbildning från en Microsoft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    I det här exemplet används version CLI-2.0.80. Distributions skript använder standard avbildningarna för CLI-behållare som hittas [här](https://hub.docker.com/_/microsoft-azure-cli).

1. Kör Docker-avbildningen lokalt.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Ersätt ** &lt; värd driv rutins beteckningen>** och ** &lt; värd katalog namnet>** med en befintlig mapp på den delade enheten.  Mappen mappas till mappen **/data** i behållaren. Till exempel för att mappa D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **– det** innebär att behållar avbildningen är aktiv.

    Ett CLI-exempel:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Följande skärm bild visar hur du kör ett PowerShell-skript, eftersom du har en helloworld.ps1-fil på den delade enheten.

    ![Resource Manager-mall distribution skript Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

När skriptet har testats kan du använda det som ett distributions skript i mallarna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributions skript. För att gå igenom en själv studie kurs om distributions skript:

> [!div class="nextstepaction"]
> [Självstudie: använda distributions skript i Azure Resource Manager mallar](./template-tutorial-deployment-script.md)
