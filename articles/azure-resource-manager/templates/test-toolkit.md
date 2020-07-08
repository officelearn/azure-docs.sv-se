---
title: Test verktyg för ARM-mall
description: Beskriver hur du kör test verktyg för ARM-mallen på din mall. Med verktyget kan du se om du har implementerat rekommenderade metoder.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255980"
---
# <a name="use-arm-template-test-toolkit"></a>Använd test verktyg för ARM-mall

[Arm-mallens test verktyg](https://aka.ms/arm-ttk) kontrollerar om mallen använder rekommenderade metoder. När din mall inte är kompatibel med rekommenderade metoder returnerar den en lista med varningar med de föreslagna ändringarna. Med hjälp av test Toolkit kan du lära dig hur du undviker vanliga problem i att utveckla mallar.

Test Toolkit tillhandahåller en [uppsättning standardtester](test-cases.md). Dessa tester är rekommendationer men inte krav. Du kan bestämma vilka tester som är relevanta för dina mål och anpassa vilka tester som körs.

Den här artikeln beskriver hur du kör test Toolkit och hur du lägger till eller tar bort tester. Beskrivningar av standardtester finns i [test fall för Toolkit](test-cases.md).

Toolkit är en uppsättning PowerShell-skript som kan köras från ett kommando i PowerShell eller CLI.

## <a name="download-test-toolkit"></a>Hämta test Toolkit

Om du vill använda test verktyget kan du antingen förgrena och klona [lagrings platsen](https://aka.ms/arm-ttk) som innehåller skripten eller [Ladda ned den senaste. zip-filen](https://aka.ms/arm-ttk-latest).

Beroende på körnings principen på den dator där du kör skriptet kan du få ett fel meddelande om att skript körs från Internet. Du måste antingen ändra [körnings principen](/powershell/module/microsoft.powershell.core/about/about_execution_policies) eller [avblockera skriptfiler](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Kör på PowerShell

Importera modulen innan du kör testerna.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Använd följande kommando för att köra testerna i **PowerShell**:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Kör på Linux

Installera [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux)innan du kör testerna.

Använd följande kommando för att köra testerna på **Linux** i bash:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

Du kan också köra testet på pwsh.exe.

## <a name="run-on-macos"></a>Kör på macOS

Installera [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos)innan du kör testerna. 

Installera `coreutils`:

```bash
brew install coreutils
```

Använd följande kommando för att köra testerna på **MacOS**:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Resultat format

Test som skickas visas i **grönt** och föregås av **[+]**.

Test som inte fungerar visas i **rött** och föregås av **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="Visa test resultat":::

Text resultatet är:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Test parametrar

När du anger parametern **-TemplatePath** söker verktyget i mappen efter en mall som heter azuredeploy.jspå eller maintemplate.js. Den här mallen testas först och sedan testas alla andra mallar i mappen och dess undermappar. De andra mallarna testas som länkade mallar. Om sökvägen innehåller en fil med namnet [CreateUiDefinition.jspå](../managed-applications/create-uidefinition-overview.md), körs testerna som är relevanta för användar gränssnitts definitionen.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Om du vill testa en fil i mappen lägger du till parametern **-File** . Mappen måste dock fortfarande ha en huvud-mall med namnet azuredeploy.jspå eller maintemplate.jspå.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Som standard körs alla tester. Om du vill ange enskilda tester som ska köras använder du parametern **-test** . Ange namnet på testet. För namnen, se [test väskor för Toolkit](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Anpassa tester

För ARM-mallar kör verktyget alla tester i mappen **\arm-ttk\testcases\deploymentTemplate**. Om du vill ta bort ett test permanent tar du bort filen från-mappen.

För [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) -filer körs alla tester i mappen **\arm-ttk\testcases\CreateUiDefinition**.

Om du vill lägga till ett eget test skapar du en fil med namngivnings konventionen: **Your-Custom-Test-Name.test.ps1**.

Testet kan hämta mallen som en objekt parameter eller en sträng parameter. Normalt använder du en eller flera, men du kan använda båda.

Använd objekt parametern när du behöver hämta ett avsnitt i mallen och iterera genom dess egenskaper. Ett test som använder objekt parametern har följande format:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Objektet Template har följande egenskaper:

* $schema
* contentVersion
* parameters
* användarvariabler
* resources
* utdata

Du kan till exempel hämta parameter samlingen med `$TemplateObject.parameters` .

Använd sträng parametern när du behöver utföra en sträng åtgärd på hela mallen. Ett test som använder sträng parametern har följande format:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Du kan till exempel köra ett reguljärt uttryck för sträng parametern för att se om en speciell syntax används.

Om du vill veta mer om hur du implementerar testet kan du titta på de andra testerna i mappen.

## <a name="integrate-with-azure-pipelines"></a>Integrera med Azure-pipeliner

Du kan lägga till test Toolkit i din Azure-pipeline. Med en pipeline kan du köra testet varje gång mallen uppdateras eller köra den som en del av distributions processen.

Det enklaste sättet att lägga till test Toolkit i din pipeline är med tillägg från tredje part. Följande två tillägg är tillgängliga:

* [Kör ARM TTK-tester](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM-mall testare](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Du kan också implementera dina egna uppgifter. I följande exempel visas hur du hämtar test Toolkit.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

I nästa exempel visas hur du kör testerna.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om standardtesterna finns i [test väskor för Toolkit](test-cases.md).
