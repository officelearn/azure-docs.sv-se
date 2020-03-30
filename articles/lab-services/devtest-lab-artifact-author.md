---
title: Skapa anpassade artefakter för din virtuella DevTest Labs-dator | Microsoft-dokument
description: Lär dig hur du skapar egna artefakter som ska användas med Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66399204"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Skapa anpassade artefakter för din virtuella DevTest Labs-dator

Titta på följande video för en översikt över stegen som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Översikt
Du kan använda *artefakter* för att distribuera och konfigurera ditt program när du har etablerat en virtuell dator. En artefakt består av en artefaktdefinitionsfil och andra skriptfiler som lagras i en mapp i en Git-databas. Artefaktdefinitionsfiler består av JSON och uttryck som du kan använda för att ange vad du vill installera på en virtuell dator. Du kan till exempel definiera namnet på en artefakt, ett kommando som ska köras och parametrar som är tillgängliga när kommandot körs. Du kan referera till andra skriptfiler i artefaktdefinitionsfilen efter namn.

## <a name="artifact-definition-file-format"></a>Filformat för artefaktdefinition
I följande exempel visas de avsnitt som utgör den grundläggande strukturen för en definitionsfil:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Elementnamn | Krävs? | Beskrivning |
| --- | --- | --- |
| $schema |Inga |Plats för JSON-schemafilen. JSON-schemafilen kan hjälpa dig att testa giltigheten av definitionsfilen. |
| title |Ja |Namn på artefakten som visas i labbet. |
| description |Ja |Beskrivning av artefakten som visas i labbet. |
| iconUri (ikonUri) |Inga |URI för ikonen som visas i labbet. |
| targetOsType |Ja |Operativsystem för den virtuella datorn där artefakten är installerad. Alternativ som stöds är Windows och Linux. |
| parameters |Inga |Värden som anges när artefaktinstallationskommandot körs på en dator. Detta hjälper dig att anpassa artefakten. |
| runCommand (på andra sätt) |Ja |Artefaktinstallationskommando som körs på en virtuell dator. |

### <a name="artifact-parameters"></a>Artefaktparametrar
I avsnittet parametrar i definitionsfilen anger du vilka värden en användare kan ange när de installerar en artefakt. Du kan referera till dessa värden i kommandot för artefaktinstallation.

Om du vill definiera parametrar använder du följande struktur:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Elementnamn | Krävs? | Beskrivning |
| --- | --- | --- |
| typ |Ja |Typ av parametervärde. Se följande lista för tillåtna typer. |
| displayName |Ja |Namnet på parametern som visas för en användare i labbet. |
| description |Ja |Beskrivning av parametern som visas i labbet. |

Tillåtna typer är:

* sträng (giltig JSON-sträng)
* int (alla giltiga JSON-heltal)
* bool (alla giltiga JSON Boolean)
* matris (alla giltiga JSON-matriser)

## <a name="secrets-as-secure-strings"></a>Hemligheter som säkra strängar
Deklarera hemligheter som säkra strängar. Här är syntaxen för att deklarera `parameters` en säker strängparameter i avsnittet **artefaktfil.json-fil:**

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

För kommandot för artefaktinstallation kör du PowerShell-skriptet som tar den säkra strängen som skapas med kommandot ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

I det här exemplet finns i [det här exemplet på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

En annan viktig punkt att notera är att inte logga hemligheter till konsolen som produktionen fångas för användare felsökning. 

## <a name="artifact-expressions-and-functions"></a>Artefaktuttryck och funktioner
Du kan använda uttryck och funktioner för att konstruera kommandot för artefaktinstallation.
Uttryck omges med parenteser ([ och ]), och utvärderas när artefakten installeras. Uttryck kan visas var som helst i ett JSON-strängvärde. Uttryck returnerar alltid ett annat JSON-värde. Om du behöver använda en litteral sträng som börjar med en hakparentes ([) måste du använda två parenteser ([[).
Vanligtvis använder du uttryck med funktioner för att konstruera ett värde. Precis som i JavaScript formateras funktionsanrop som **functionName(arg1, arg2, arg3).**

Följande lista visar vanliga funktioner:

* **parameters(parameterName)**: Returnerar ett parametervärde som anges när artefaktkommandot körs.
* **concat(arg1, arg2, arg3,..... )**: Kombinerar flera strängvärden. Den här funktionen kan ta en mängd olika argument.

I följande exempel visas hur du använder uttryck och funktioner för att konstruera ett värde:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Skapa en anpassad artefakt

1. Installera en JSON-redigerare. Du behöver en JSON-redigerare för att arbeta med artefaktdefinitionsfiler. Vi rekommenderar att du använder [Visual Studio Code](https://code.visualstudio.com/), som är tillgängligt för Windows, Linux och OS X.
2. Hämta en exempelaktefaktfil.json-definitionsfil. Kolla in de artefakter som skapats av DevTest Labs-teamet i vår [GitHub-lagringsplats](https://github.com/Azure/azure-devtestlab). Vi har skapat ett rikt bibliotek av artefakter som kan hjälpa dig att skapa dina egna artefakter. Hämta en artefaktdefinitionsfil och gör ändringar i den för att skapa egna artefakter.
3. Utnyttja IntelliSense. Använd IntelliSense för att se giltiga element som du kan använda för att skapa en artefaktdefinitionsfil. Du kan också se de olika alternativen för värden för ett element. När du till exempel redigerar **targetOsType-elementet** visar IntelliSense två alternativ för Windows eller Linux.
4. Lagra artefakten i den [offentliga Git-databasen för DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) eller [din egen Git-databas](devtest-lab-add-artifact-repo.md). I den offentliga databasen kan du visa artefakter som delas av andra som du kan använda direkt eller anpassa dem efter dina behov.
   
   1. Skapa en separat katalog för varje artefakt. Katalognamnet ska vara samma som artefaktnamnet.
   2. Lagra artefaktdefinitionsfilen (artifactfile.json) i katalogen som du skapade.
   3. Lagra skripten som refereras från kommandot för artefaktinstallation.
      
      Här är ett exempel på hur en artefaktmapp kan se ut:
      
      ![Exempel på artefaktmapp](./media/devtest-lab-artifact-author/git-repo.png)
5. Om du använder din egen databas för att lagra artefakter lägger du till databasen i labbet genom att följa instruktionerna i artikeln: [Lägg till en Git-databas för artefakter och mallar](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Så här diagnostiserar du artefaktfel i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ansluta en virtuell dator till en befintlig Active Directory-domän med hjälp av en Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en Git-artefaktdatabas i ett labb](devtest-lab-add-artifact-repo.md).
