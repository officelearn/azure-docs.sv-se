---
title: Använda distributions skript i mallar | Microsoft Docs
description: Använd distributions skript i Azure Resource Manager mallar.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jgao
ms.openlocfilehash: 125fefbb1d83db8b6114b2d09f5bd6da885159ba
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547650"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Använda distributions skript i mallar (förhands granskning)

Lär dig hur du använder distributions skript i Azure Resource templates. Med en ny resurs typ som heter `Microsoft.Resources/deploymentScripts`kan användare köra distributions skript i mallar distributioner och granska körnings resultat. Dessa skript kan användas för att utföra anpassade steg som:

- lägga till användare i en katalog
- skapa en app-registrering
- utför data Plans åtgärder, till exempel kopiera blobbar eller Dirigerings databas
- Leta upp och validera en licens nyckel
- skapa ett självsignerat certifikat
- skapa ett objekt i Azure AD
- Leta upp IP-adressblock från det anpassade systemet

Fördelarna med distributions skript:

- Enkelt att koda, använda och felsöka. Du kan utveckla distributions skript i dina favorit utvecklings miljöer. Skripten kan bäddas in i mallar eller i externa skriptfiler.
- Du kan ange språk och plattform för skript. För närvarande stöds endast Azure PowerShell distributions skript i Linux-miljön.
- Tillåt att du anger de identiteter som används för att köra skripten. För närvarande stöds endast [Azure User-tilldelade hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) .
- Tillåt att kommando rads argument skickas till skriptet.
- Kan ange utdata för skript och skicka tillbaka dem till distributionen.

> [!NOTE]
> Distributions skriptet är för närvarande en för hands version. Du måste [Registrera dig för för hands versionen för](https://aka.ms/armtemplatepreviews)att kunna använda den.

> [!IMPORTANT]
> Två distributions skript resurser, ett lagrings konto och en behållar instans, skapas i samma resurs grupp för skript körning och fel sökning. Dessa resurser tas vanligt vis bort av skript tjänsten när distributions skript körningen blir i ett Terminal-tillstånd. Du debiteras för resurserna tills resurserna tas bort. Läs mer i avsnittet om hur du [säkerhetskopierar skript resurser](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Krav

- **En användardefinierad hanterad identitet med deltagar rollen på prenumerations nivå**. Den här identiteten används för att köra distributions skript. Om du vill skapa en, se [skapa en användardefinierad hanterad identitet med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), eller med [hjälp av Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)eller [genom att använda Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Du behöver identitets-ID när du distribuerar mallen. Formatet på identiteten är:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Använd följande PowerShell-skript för att hämta ID: t genom att ange resurs gruppens namn och identitetens namn.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell version 2.7.0, 2.8.0 eller 3.0.0**. Du behöver inte dessa versioner för att distribuera mallar. Men de här versionerna behövs för att testa distributions skript lokalt. Se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Du kan använda en förkonfigurerad Docker-avbildning.  Se [Konfigurera utvecklings miljö](#configure-development-environment).

## <a name="resource-schema"></a>Resurs schema

Följande JSON är ett exempel.  Du hittar det senaste mallnamnet [här](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Exemplet är i demonstrations syfte.  **scriptContent** och **primaryScriptUris** kan inte finnas i en mall.

Information om egenskaps värde:

- **Identitet**: distributions skript tjänsten använder en användardefinierad hanterad identitet för att köra skripten. För närvarande stöds endast användardefinierad hanterad identitet.
- **typ**: ange typ av skript. För närvarande stöds endast Azure PowerShell skript. Värdet är **AzurePowerShell**.
- **forceUpdateTag**: om du ändrar det här värdet mellan mallens distributioner tvingas distributions skriptet att köras igen. Använd funktionen newGuid () eller utcNow () som måste anges som defaultValue för en parameter. Mer information finns i [Kör skript mer än en gång](#run-script-more-than-once).
- **azPowerShellVersion**: Ange Azure PowerShell-modulens version som ska användas. Distributions skriptet stöder för närvarande version 2.7.0, 2.8.0 och 3.0.0.
- **argument**: ange parameter värden. Värdena avgränsas med blank steg.
- **scriptContent**: ange skript innehållet. Om du vill köra ett externt skript använder du `primaryScriptUri` i stället. Exempel finns i [använda infogat skript](#use-inline-scripts) och [Använd externt skript](#use-external-scripts).
- **primaryScriptUri**: Ange en offentligt tillgänglig URL till det primära PowerShell-skriptet med PowerShell-filtillägg som stöds.
- **supportingScriptUris**: Ange en matris med offentligt tillgängliga URL: er som stöder PowerShell-filer som ska anropas i antingen `ScriptContent` eller `PrimaryScriptUri`.
- **timeout**: Ange högsta tillåtna körnings tid för skript som anges i [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**.
- **cleanupPreference**. Ange inställningen för att rensa distributions resurser när skript körningen blir i ett Terminal-tillstånd. Standardinställningen är **Always**, vilket innebär att resurserna tas bort trots att terminalens tillstånd (lyckades, misslyckades, avbröts). Mer information finns i [Rensa distribution skript resurser](#clean-up-deployment-script-resources).
- **retentionInterval**: Ange intervallet som tjänsten behåller distributions skript resurserna efter när distributions skript körningen har nått ett terminalfönster. Distributions skript resurserna tas bort när denna varaktighet upphör att gälla. Varaktigheten baseras på [ISO 8601-mönstret](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**, vilket betyder sju dagar. Den här egenskapen används när cleanupPreference är inställt på *OnExpiration*. Egenskapen *OnExpiration* har inte Aktiver ATS för närvarande. Mer information finns i [Rensa distribution skript resurser](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Använd infogade skript

Följande mall har en resurs definierad med `Microsoft.Resources/deploymentScripts` typen.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Eftersom de infogade distributions skripten omges av dubbla citat tecken måste strängarna inuti distributions skripten omges av enkla citat tecken i stället. Escape-tecken för PowerShell är **&#92;** . Du kan också överväga att använda sträng ersättning som det visas i föregående JSON-exempel. Se standardvärdet för name-parametern.

Skriptet tar en parameter och matar ut parametervärdet. **DeploymentScriptOutputs** används för att lagra utdata.  I avsnittet utdata visar **värde** raden hur du kommer åt de lagrade värdena. `Write-Output` används för fel söknings syfte. Information om hur du kommer åt utdatafilen finns i [fel sökning av distributions skript](#debug-deployment-scripts).  För egenskaps beskrivningar, se [resurs schema](#resource-schema).

Om du vill köra skriptet väljer du **testa det** för att öppna Cloud Shell och klistrar sedan in följande kod i rutan Shell.

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

Förutom infogade skript kan du också använda externa skriptfiler. För närvarande stöds endast PowerShell-skript med fil namns tillägget **ps1** . Om du vill använda externa skriptfiler ersätter du `scriptContent` med `primaryScriptUri`. Ett exempel:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Om du vill se ett exempel väljer du [här](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

De externa skriptfilerna måste vara tillgängliga.  Information om hur du skyddar skriptfilerna som lagras i Azure Storage-konton finns i [Självstudier: säkra artefakter i Azure Resource Manager mallar distributioner](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Använda stöd skript

Du kan separera komplicerade logiker till en eller flera stödfiler. Med egenskapen `supportingScriptURI` kan du ange en matris med URI: er till de stödda skriptfilerna om det behövs:

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

Stöd för skriptfiler kan anropas från både infogade skript och primära skriptfiler.

De stödfiler som kopieras kopieras till azscripts/azscriptinput vid körningen. Använd relativ sökväg för att referera till stödjande filer från infogade skript och primära skriptfiler.

## <a name="work-with-outputs-from-deployment-scripts"></a>Arbeta med utdata från distributions skript

Följande mall visar hur du överför värden mellan två deploymentScripts-resurser:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

I den första resursen definierar du en variabel som heter **$DeploymentScriptOutputs**och använder den för att lagra värdena för utdata. Använd följande för att få åtkomst till resultatvärdet från en annan resurs i mallen:

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>Felsöka distributions skript

Skript tjänsten skapar ett [lagrings konto](../../storage/common/storage-account-overview.md) och en [behållar instans](../../container-instances/container-instances-overview.md) för skript körning. Båda resurserna har **azscripts** -suffixet i resurs namnen.

![Distributions skript resurs namn för Resource Manager-mall](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Användar skriptet, körnings resultaten och STDOUT-filen lagras i lagrings kontots fil resurser. Det finns en mapp med namnet **azscripts**. I mappen finns det två fler mappar för indata och utdatafilerna: **azscriptinput** och **azscriptoutput**.

Mappen utdata innehåller en **executionresult. JSON** -fil och skript utdatafilen. Du kan se fel meddelandet för skript körning i **executionresult. JSON**. Utdatafilen skapas endast när skriptet har körts. Mappen indata innehåller en system PowerShell-skriptfil och skript filen för användar distribution. Du kan ersätta skript filen för användar distribution med en ändrad, och köra distributions skriptet på nytt från Azure Container instance.

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

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

Utdata visar distributions status och resurs-ID för distributions skript.

Följande REST API returnerar loggen:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Den fungerar bara innan distributions skript resurserna tas bort.

Om du vill se deploymentScripts-resursen i portalen väljer du **Visa dolda typer**:

![Distributions skript för Resource Manager-mall, Visa dolda typer, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Rensa distributions skript resurser

Distributions skript skapar ett lagrings konto och en behållar instans som används för att köra distributions skript och lagra felsöknings information. De här två resurserna skapas i samma resurs grupp som de etablerade resurserna och kommer att tas bort av skript tjänsten när skriptet upphör att gälla. Du kan kontrol lera livs cykeln för dessa resurser.  Du debiteras för båda resurserna tills de tas bort. Pris informationen finns i [container instances priser](https://azure.microsoft.com/pricing/details/container-instances/) och [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

Livs cykeln för de här resurserna styrs av följande egenskaper i mallen:

- **cleanupPreference**: rensnings inställning när skript körningen får statusen Terminal.  De värden som stöds är:

  - **Always**: ta bort resurserna när skript körningen blir i ett terminalfönster. Eftersom deploymentScripts-resursen kanske fortfarande finns när resurserna har rensats, skulle system skriptet kopiera skript körnings resultatet, till exempel STDOUT, utdata, retur värde osv. till DB innan resurserna tas bort.
  - **OnSuccess**: ta bara bort resurserna när skript körningen har slutförts. Du kan fortfarande komma åt resurserna för att hitta felsöknings information.
  - **OnExpiration**: ta bara bort resurserna när **retentionInterval** -inställningen har upphört att gälla. Den här egenskapen är för närvarande inaktive rad.

- **retentionInterval**: Ange det tidsintervall som en skript resurs kommer att behållas och därefter upphör att gälla och tas bort.

> [!NOTE]
> Vi rekommenderar inte att du använder distributions skript resurser för andra skäl.

## <a name="run-script-more-than-once"></a>Kör skript mer än en gång

Körning av distributions skript är en idempotenta åtgärd. Om ingen av resurs egenskaperna för deploymentScripts (inklusive det infogade skriptet) ändras, utförs inte skriptet när du distribuerar mallen på annat sätt. Distributions skript tjänsten jämför resurs namnen i mallen med de befintliga resurserna i samma resurs grupp. Det finns två alternativ om du vill köra samma distributions skript flera gånger:

- Ändra namnet på din deploymentScripts-resurs. Använd till exempel funktionen [utcNow](./template-functions-string.md#utcnow) -mall som resurs namn eller som en del av resurs namnet. Om du ändrar resurs namnet skapas en ny deploymentScripts-resurs. Det är lämpligt att hålla en historik över skript körningen.

    > [!NOTE]
    > Funktionen utcNow kan bara användas i standardvärdet för en parameter.

- Ange ett annat värde i egenskapen `forceUpdateTag` Template.  Använd till exempel utcNow som värde.

> [!NOTE]
> Skriv de distributions skript som är idempotenta. Detta säkerställer att om de körs igen av misstag, kommer de inte att orsaka system ändringar. Om distributions skriptet exempelvis används för att skapa en Azure-resurs kontrollerar du att resursen inte finns innan du skapar den, så skriptet kommer att lyckas eller så skapar du inte resursen igen.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

För närvarande stöder distributions skriptet Azure PowerShell version 2.7.0, 2.8.0 och 3.0.0.  Om du har en Windows-dator kan du installera en av de Azure PowerShell-versioner som stöds och börja utveckla och testa distributions skript.  Om du inte har en Windows-dator eller om du inte har någon av dessa Azure PowerShell-versioner installerade kan du använda en förkonfigurerad Docker-behållar avbildning. Följande procedur visar hur du konfigurerar Docker-avbildningen i Windows. För Linux och Mac kan du hitta informationen på Internet.

1. Installera [Docker Desktop](https://www.docker.com/products/docker-desktop) på din utvecklings dator.
1. Öppna Docker Desktop.
1. Välj ikonen Docker Desktop på aktivitets fältet och välj sedan **Inställningar**.
1. Välj **delade enheter**, Välj en lokal enhet som du vill ska vara tillgänglig för dina behållare och välj sedan **Använd**

    ![Distributions skript Docker-enhet för Resource Manager-mall](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Ange dina Windows-autentiseringsuppgifter i prompten.
1. Öppna ett terminalfönster, antingen kommando tolken eller Windows PowerShell (Använd inte PowerShell ISE).
1. Hämta distributions skriptets behållar avbildning till den lokala datorn:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Exemplet använder version 2.7.0.

1. Kör Docker-avbildningen lokalt.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Ersätt **&lt;värd driv rutins beteckning >** och **&lt;värd katalog namn >** med en befintlig mapp på den delade enheten.  Mappen mappas till mappen **/data** i behållaren. Till exempel för att mappa D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **– det** innebär att behållar avbildningen är aktiv.

1. Välj **dela den** när du får en prompt.
1. Kör ett PowerShell-skript som visas i följande skärm bild (eftersom du har en HelloWorld. ps1-fil i mappen d:\docker.)

    ![Resource Manager-mall distribution skript Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

När PowerShell-skriptet har testats kan du använda det som ett distributions skript.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributions skript. För att gå igenom en själv studie kurs om distributions skript:

> [!div class="nextstepaction"]
> [Självstudie: använda distributions skript i Azure Resource Manager mallar](./template-tutorial-deployment-script.md)