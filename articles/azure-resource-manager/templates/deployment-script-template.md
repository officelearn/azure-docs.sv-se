---
title: Använda distributionsskript i mallar | Microsoft-dokument
description: använda distributionsskript i Azure Resource Manager-mallar.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: aa49b313f0fb10175dc6c0003f1a919f61731269
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743312"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Använda distributionsskript i mallar (förhandsgranskning)

Lär dig hur du använder distributionsskript i Azure Resource-mallar. Med en ny `Microsoft.Resources/deploymentScripts`resurstyp som kallas kan användare köra distributionsskript i malldistributioner och granska körningsresultat. Dessa skript kan användas för att utföra anpassade steg, till exempel:

- lägga till användare i en katalog
- skapa en appregistrering
- utföra dataplansåtgärder, till exempel kopiera blobbar eller frödatabas
- slå upp och validera en licensnyckel
- skapa ett självsignerat certifikat
- skapa ett objekt i Azure AD
- slå upp IP-adressblock från anpassat system

Fördelarna med distributionsskript:

- Lätt att koda, använda och felsöka. Du kan utveckla distributionsskript i dina favoritutvecklingsmiljöer. Skripten kan bäddas in i mallar eller i externa skriptfiler.
- Du kan ange skriptspråk och plattform. För närvarande stöds Azure PowerShell- och Azure CLI-distributionsskript i Linux-miljön.
- Tillåt att ange de identiteter som används för att köra skripten. För närvarande stöds endast [Azure-användartilldelade hanterade identitet.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
- Tillåt att kommandoradsargument skickars till skriptet.
- Kan ange skriptutdata och skicka dem tillbaka till distributionen.

Distributionsskriptresursen är endast tillgänglig i de regioner där Azure Container Instance är tillgänglig.  Se [Resurstillgänglighet för Azure Container Instances i Azure-regioner](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Två distributionsskriptresurser, ett lagringskonto och en behållarinstans, skapas i samma resursgrupp för skriptkörning och felsökning. Dessa resurser tas vanligtvis bort av skripttjänsten när körningen av distributionsskriptet hamnar i ett terminaltillstånd. Du debiteras för resurserna tills resurserna tas bort. Mer information finns i [Rensa distributionsskriptresurser](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Krav

- **En användartilldelad hanterad identitet med deltagarens roll i målresursgruppen**. Den här identiteten används för att köra distributionsskript. Om du vill utföra åtgärder utanför resursgruppen måste du bevilja ytterligare behörigheter. Tilldela till exempel identiteten till prenumerationsnivån om du vill skapa en ny resursgrupp.

  > [!NOTE]
  > Distributionsskriptmotorn skapar ett lagringskonto och en behållarinstans i bakgrunden.  En användartilldelad hanterad identitet med deltagarens roll på prenumerationsnivå krävs om prenumerationen inte har registrerat azure storage-kontot (Microsoft.Storage) och Azure-behållarinstansleverantörerna (Microsoft.ContainerInstance).

  Information om hur du skapar en identitet finns i [Skapa en användartilldelad hanterad identitet med hjälp av Azure-portalen](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)eller [med hjälp av Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)eller med Hjälp av Azure [PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Du behöver identitets-ID:n när du distribuerar mallen. Identitetens format är:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Använd följande CLI- eller PowerShell-skript för att hämta ID:t genom att ange resursgruppsnamnet och identitetsnamnet.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** eller **Azure CLI**. En lista över Azure PowerShell-versioner som stöds finns [här](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). en lista över Azure CLI-versioner som stöds finns [här](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Distributionsskriptet använder tillgängliga CLI-avbildningar från Microsoft Container Registry(MCR) . Det tar ungefär en månad att certifiera en CLI-avbildning för distributionsskript. Använd inte CLI-versionerna som släpptes inom 30 dagar. Information om hur du hittar utgivningsdatumen för avbildningarna finns i [Azure CLI-versionsanteckningar](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Om en version som inte stöds används visas de versioner som stöds i felmeddelandet.

    Du behöver inte dessa versioner för att distribuera mallar. Men dessa versioner behövs för att testa distributionsskript lokalt. Se [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Du kan använda en förkonfigurerad Docker-avbildning.  Se [Konfigurera utvecklingsmiljö](#configure-development-environment).

## <a name="sample-templates"></a>Exempelmallar

Följande json är ett exempel.  Det senaste mallschemat hittar du [här](/azure/templates/microsoft.resources/deploymentscripts).

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
> Exemplet är för demonstrationsändamål.  **scriptContent** och **primaryScriptUris** kan inte samexistera i en mall.

Information om egenskapsvärde:

- **Identitet:** Distributionsskripttjänsten använder en användartilldelad hanterad identitet för att köra skripten. För närvarande stöds endast användartilldelade hanterade identitet.
- **typ**: Ange typ av skript. För närvarande är Azure PowerShell- och Azure CLI-skript stöd. Värdena är **AzurePowerShell** och **AzureCLI**.
- **forceUpdateTag**: Om du ändrar det här värdet mellan malldistributioner tvingas distributionsskriptet att köras igen. Använd funktionen newGuid() eller utcNow() som måste anges som standardvärde för en parameter. Mer information finns i [Kör skript mer än en gång](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: Ange vilken modulversion som ska användas. En lista över PowerShell- och CLI-versioner som stöds finns i [Förutsättningar](#prerequisites).
- **argument**: Ange parametervärdena. Värdena avgränsas med blanksteg.
- **environmentVariables**: Ange vilka miljövariabler som ska överföras till skriptet. Mer information finns i [Utveckla distributionsskript](#develop-deployment-scripts).
- **scriptContent**: Ange skriptinnehåll. Om du vill köra `primaryScriptUri` ett externt skript använder du i stället. Exempel finns [i Använda infogat skript](#use-inline-scripts) och [Använd externt skript](#use-external-scripts).
- **primaryScriptUri**: Ange en offentligt tillgänglig url till det primära distributionsskriptet med filtillägg som stöds.
- **supportingScriptUris**: Ange en matris med offentligt tillgängliga webbadresser `ScriptContent` `PrimaryScriptUri`till stödfiler som anropas i antingen eller .
- **timeout:** Ange den maximala tillåtna skriptkörningstiden som anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**.
- **cleanupPreference**. Ange inställningen för att rensa distributionsresurser när skriptkörningen hamnar i ett terminaltillstånd. Standardinställningen är **Alltid**, vilket innebär att ta bort resurserna trots terminaltillståndet (Lyckades, Misslyckades, Annullerad). Mer information finns i [Rensa distributionsskriptresurser](#clean-up-deployment-script-resources).
- **retentionInterval**: Ange det intervall för vilket tjänsten behåller distributionsskriptresurserna när distributionsskriptkörningen når ett terminaltillstånd. Distributionsskriptresurserna tas bort när den här varaktigheten upphör att gälla. Varaktigheten baseras på [ISO 8601-mönstret](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**, vilket innebär sju dagar. Den här egenskapen används när cleanupPreference är inställt *på OnExpiration*. Egenskapen *OnExpiration* är inte aktiverad för tillfället. Mer information finns i [Rensa distributionsskriptresurser](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Ytterligare exempel

- [skapa och tilldela ett certifikat till ett nyckelvalv](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [skapa och tilldela en användartilldelad hanterad identitet till en resursgrupp och köra ett distributionsskript](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Vi rekommenderar att du skapar en användartilldelad identitet och beviljar behörigheter i förväg. Du kan få inloggnings- och behörighetsrelaterade fel om du skapar identitets- och bidragsbehörigheterna i samma mall där du kör distributionsskript. Det tar lite tid innan behörigheterna att träda i kraft.

## <a name="use-inline-scripts"></a>Använda infogade skript

Följande mall har en resurs `Microsoft.Resources/deploymentScripts` definierad med typen. Den markerade delen är infogade skript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Eftersom infogade distributionsskript omges av dubbla citattecken måste strängarna i distributionsskripten omges av enkla citattecken i stället. Escape-tecknet för PowerShell är **&#92;**. Du kan också överväga att använda strängersättning som det visas i det tidigare JSON-exemplet. Se standardvärdet för namnparametern.

Skriptet tar en parameter och matar ut parametervärdet. **DeploymentScriptOutputs** används för att lagra utdata.  I avsnittet utdata visar **värderaden** hur du kommer åt de lagrade värdena. `Write-Output`används för felsökning. Mer information om hur du kommer åt utdatafilen finns i [Felsökningsdistributionsskript](#debug-deployment-scripts).  För egenskapsbeskrivningarna finns i [Exempelmallar](#sample-templates).

Om du vill köra skriptet väljer du **Prova** att öppna Cloud Shell och klistra sedan in följande kod i skalrutan.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

De utdata som returneras ser ut så här:

![Distributionsskript för Resurshanterarens mall hej världsutdata](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Använda externa skript

Förutom infogade skript kan du också använda externa skriptfiler. Endast primära PowerShell-skript med **ps1-filtillägget** stöds. För CLI-skript kan de primära skripten ha alla tillägg (eller utan tillägg), så länge skripten är giltiga bash-skript. Om du vill använda `scriptContent` `primaryScriptUri`externa skriptfiler ersätter du med . Ett exempel:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Om du vill se ett exempel väljer du [här](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

De externa skriptfilerna måste vara tillgängliga.  Information om hur du skyddar skriptfilerna som lagras i Azure-lagringskonton finns i [Självstudiekurs: Säkra artefakter i Azure Resource Manager-malldistributioner](./template-tutorial-secure-artifacts.md).

Du ansvarar för att säkerställa integriteten för de skript som refereras av distributionsskript, antingen **PrimaryScriptUri** eller **SupportingScriptUris**.  Referera bara skript som du litar på.

## <a name="use-supporting-scripts"></a>Använda stödskript

Du kan separera komplicerade logiker i en eller flera stödjande skriptfiler. Med `supportingScriptURI` egenskapen kan du tillhandahålla en matris med URI:er till de stödjande skriptfilerna om det behövs:

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

Stöd skriptfiler kan anropas från både infogade skript och primära skriptfiler. Stödskriptfiler har inga begränsningar för filändelsen.

Stödfilerna kopieras till azscripts/azscriptinput vid körningen. Använd relativ sökväg för att referera till stödfiler från infogade skript och primära skriptfiler.

## <a name="work-with-outputs-from-powershell-script"></a>Arbeta med utdata från PowerShell-skript

Följande mall visar hur du skickar värden mellan två distributionsskriptresurser:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

I den första resursen definierar du en variabel som kallas **$DeploymentScriptOutputs**och använder den för att lagra utdatavärdena. Om du vill komma åt utdatavärdet från en annan resurs i mallen använder du:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Arbeta med utdata från CLI-skript

Cli/bash-stöd skiljer sig från PowerShell-distributionsskriptet och exponerar inte en gemensam variabel för att lagra skriptutdata, utan det finns en miljövariabel som kallas **AZ_SCRIPTS_OUTPUT_PATH** som lagrar den plats där skriptutdatafilen finns. Om ett distributionsskript körs från en Resource Manager-mall ställs den här miljövariabeln in automatiskt för dig av bash-skalet.

Distributionsskriptutdata måste sparas på AZ_SCRIPTS_OUTPUT_PATH plats och utdata måste vara ett giltigt JSON-strängobjekt. Innehållet i filen måste sparas som ett nyckelvärdespar. Till exempel lagras en matris med strängar som { "MyResult": [ "foo", "bar"] }.  Lagra bara matrisen resultat, till exempel [ "foo", "bar"], är ogiltig.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) används i föregående prov. Den levereras med behållarbilderna. Se [Konfigurera utvecklingsmiljö](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Utveckla distributionsskript

### <a name="handle-non-terminating-errors"></a>Hantera fel som inte avslutas

Du kan styra hur PowerShell svarar på fel som inte avslutas med hjälp av [**variabeln $ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) i distributionsskriptet. Distributionsskriptmotorn anger/ändrar inte värdet.  Trots det värde som du anger för $ErrorActionPreference anger distributionsskriptet resursetableringstillståndet till *Misslyckades* när skriptet stöter på ett fel.

### <a name="pass-secured-strings-to-deployment-script"></a>Skicka säkrade strängar till distributionsskript

Med inställning av miljövariabler (EnvironmentVariable) i behållarinstanserna kan du tillhandahålla dynamisk konfiguration av programmet eller skriptet som körs av behållaren. Distributionsskript hanterar icke-säkra och säkra miljövariabler på samma sätt som Azure Container Instance. Mer information finns [i Ange miljövariabler i behållarinstanser](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Felsöka distributionsskript

Skripttjänsten skapar ett [lagringskonto](../../storage/common/storage-account-overview.md) och en [behållarinstans](../../container-instances/container-instances-overview.md) för skriptkörning. Båda resurserna har suffixet **azscripts** i resursnamnen.

![Resurshanterarens malldistributionsskriptresursnamn](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Användarskriptet, körningsresultaten och stdout-filen lagras i filresurserna för lagringskontot. Det finns en mapp som heter **azscripts**. I mappen finns det ytterligare två mappar för indata och utdatafiler: **azscriptinput** och **azscriptoutput**.

Utdatamappen innehåller en **executionresult.json** och skriptutdatafilen. Felmeddelandet skriptkörning i **executionresult.json**. Utdatafilen skapas endast när skriptet körs. Indatamappen innehåller en system-PowerShell-skriptfil och skriptfiler för användardistribution. Du kan ersätta skriptfilen för användardistribution med en reviderad och köra distributionsskriptet från Azure-behållarinstansen.

Du kan hämta distributionsskriptresursdistributionsinformationen på resursgruppsnivå och prenumerationsnivå med hjälp av REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

I följande exempel används [ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Utdatan liknar följande:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

Utdata visar distributionstillståndet och distributionsskriptresurs-ID:na.

Följande REST API returnerar loggen:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Det fungerar bara innan distributionsskriptresurserna tas bort.

Om du vill visa distributionScripts-resursen i portalen väljer du **Visa dolda typer:**

![Distributionsskript för Resurshanterarens mall, visa dolda typer, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Rensa distributionsskriptresurser

Distributionsskriptet skapar ett lagringskonto och en behållarinstans som används för att köra distributionsskript och lagra felsökningsinformation. Dessa två resurser skapas i samma resursgrupp som de etablerade resurserna och tas bort av skripttjänsten när skriptet upphör att gälla. Du kan styra livscykeln för dessa resurser.  Tills de tas bort debiteras du för båda resurserna. Prisinformation finns i [priser för behållarinstanser](https://azure.microsoft.com/pricing/details/container-instances/) och [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

Livscykeln för dessa resurser styrs av följande egenskaper i mallen:

- **cleanupPreference**: Rensa inställningar när skriptkörningen hamnar i ett terminalläge.  De värden som stöds är:

  - **Alltid**: Ta bort resurserna när skriptkörningen hamnar i ett terminaltillstånd. Eftersom distributionScripts-resursen fortfarande kan finnas kvar när resurserna har rensats kopierar systemskriptet skriptkörningsresultaten, till exempel stdout, utdata, returvärde etc. till DB innan resurserna tas bort.
  - **OnSuccess**: Ta bara bort resurserna när skriptkörningen lyckas. Du kan fortfarande komma åt resurserna för att hitta felsökningsinformationen.
  - **OnExpiration**: Ta bara bort resurserna när inställningen **retentionInterval** har upphört att gälla. Den här egenskapen är för närvarande inaktiverad.

- **retentionInterval**: Ange det tidsintervall som en skriptresurs ska behållas och varefter den ska förfallas och tas bort.

> [!NOTE]
> Det rekommenderas inte att använda distributionsskriptresurserna för andra ändamål.

## <a name="run-script-more-than-once"></a>Kör skript mer än en gång

Körning av distributionsskript är en idempotent åtgärd. Om ingen av distributionScripts-resursegenskaperna (inklusive infogade skriptet) ändras körs inte skriptet när du distribuerar om mallen. Distributionsskripttjänsten jämför resursnamnen i mallen med befintliga resurser i samma resursgrupp. Det finns två alternativ om du vill köra samma distributionsskript flera gånger:

- Ändra namnet på distributionsskriptresursen. Använd till exempel [mallfunktionen utcNow](./template-functions-string.md#utcnow) som resursnamn eller som en del av resursnamnet. Om du ändrar resursnamnet skapas en ny distributionScripts-resurs. Det är bra för att hålla en historia av manus utförande.

    > [!NOTE]
    > Funktionen utcNow kan bara användas i standardvärdet för en parameter.

- Ange ett annat `forceUpdateTag` värde i mallegenskapen.  Använd till exempel utcNow som värde.

> [!NOTE]
> Skriv de distributionsskript som är idempotenta. Detta säkerställer att om de körs igen av misstag, kommer det inte att orsaka systemändringar. Om distributionsskriptet till exempel används för att skapa en Azure-resurs kontrollerar du att resursen inte finns innan du skapar den, så att skriptet lyckas eller så skapar du inte resursen igen.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Du kan använda en förkonfigurerad dockerbehållarevseende som utvecklingsmiljö för distributionsskriptet. Följande procedur visar hur du konfigurerar dockeravbildningen i Windows. För Linux och Mac hittar du informationen på Internet.

1. Installera [Docker Desktop](https://www.docker.com/products/docker-desktop) på utvecklingsdatorn.
1. Öppna Docker Desktop.
1. Välj ikonen Docker Desktop i Aktivitetsfältet och välj sedan **Inställningar**.
1. Välj **Delade enheter,** välj en lokal enhet som du vill ska vara tillgänglig för dina behållare och välj sedan **Använd**

    ![Skriptdockerenhet för resurshanterarens malldistribution](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Ange dina windows-autentiseringsuppgifter vid prompten.
1. Öppna ett terminalfönster, antingen Kommandotolken eller Windows PowerShell (Använd inte PowerShell ISE).
1. Hämta behållaravbildningen för distributionsskriptet till den lokala datorn:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    I exemplet används version PowerShell 2.7.0.

    Så här hämtar du en CLI-avbildning från ett MCR -register (Microsoft Container Registry):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    I det här exemplet används version CLI 2.0.80. Distributionsskriptet använder de standard-CLI-behållaravbildningar som finns [här](https://hub.docker.com/_/microsoft-azure-cli).

1. Kör dockeravbildningen lokalt.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Ersätt ** &lt;värddrivrutinsbeteckning>** och ** &lt;värdkatalognamn>** med en befintlig mapp på den delade enheten.  Mappen mapp mapp mapp till **/data** mappen i behållaren. Om du vill veta några exempel kan du mappa D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-det** betyder att hålla behållarbilden vid liv.

    Ett CLI-exempel:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Välj **Dela den** när du får en uppmaning.
1. Följande skärmbild visar hur du kör ett PowerShell-skript, med tanke på att du har en helloworld.ps1-fil i mappen d:\docker.

    ![Distributionsskriptdocka för resurshanterarens malldistributionsskript cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

När skriptet har testats kan du använda det som ett distributionsskript.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributionsskript. Så här går du igenom en självstudiekurs för distributionsskript:

> [!div class="nextstepaction"]
> [Självstudiekurs: Använda distributionsskript i Azure Resource Manager-mallar](./template-tutorial-deployment-script.md)