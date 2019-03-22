---
title: Skapa anpassade artefakter för din virtuella dator för DevTest Labs | Microsoft Docs
description: Lär dig hur du skapar dina egna artefakter som ska användas med Azure DevTest Labs.
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
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 0d1e269a1818f013bc14842bc541216d7f31bc84
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116834"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Skapa anpassade artefakter för din virtuella dator för DevTest Labs

Titta på följande videoklipp för en översikt över stegen som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Översikt
Du kan använda *artefakter* att distribuera och konfigurera ditt program när du etablerar en virtuell dator. En artefakt består av en artefakt-definitionsfil och andra filer som lagras i en mapp i en Git-lagringsplats. Artefakten definitionsfiler består av JSON och uttryck som du kan använda för att ange vad du vill installera på en virtuell dator. Du kan till exempel definiera namnet på en artefakt, kommandon och parametrar som är tillgängliga när kommandot körs. Du kan referera till andra skriptfiler i definitionsfilen för artefakten efter namn.

## <a name="artifact-definition-file-format"></a>Artefakten format för paketdefinitionsfiler
I följande exempel visas de avsnitt som utgör den grundläggande strukturen i en definitionsfil:

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
| $schema |Nej |Plats för JSON-schemafilen. Med hjälp av JSON-schema-fil kan du testa giltighet definitionsfilen. |
| title |Ja |Namnet på den artefakt som visas i laboratoriet. |
| beskrivning |Ja |Beskrivning av den artefakt som visas i laboratoriet. |
| iconUri |Nej |URI för den ikon som visas i laboratoriet. |
| targetOsType |Ja |Operativsystemet på den virtuella datorn där artefakten är installerad. Alternativ som stöds är Windows och Linux. |
| parameters |Nej |Värden som tillhandahålls när artefakt installationskommandot körs på en dator. På så sätt kan du anpassa din artefakten. |
| runCommand |Ja |Artefakten installationskommando som körs på en virtuell dator. |

### <a name="artifact-parameters"></a>Artefaktparametrar
Ange vilka värden som en användare kan ange när de installerar en artefakt i avsnittet parametrar i definitionsfilen. Du kan hänvisa till dem i artefakten install-kommandot.

För att definiera parametrar, använder du följande struktur:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Elementnamn | Krävs? | Beskrivning |
| --- | --- | --- |
| typ |Ja |Typ av parametervärdet. Se följande lista innehåller de tillåtna typerna. |
| displayName |Ja |Namnet på parametern som visas för en användare i laboratoriet. |
| beskrivning |Ja |Beskrivning av den parameter som visas i laboratoriet. |

Tillåtna typer är:

* sträng (en giltig JSON-sträng)
* int (alla giltiga JSON-heltal)
* bool (alla giltiga JSON booleska)
* matris (någon giltig JSON-matris)

## <a name="artifact-expressions-and-functions"></a>Artefakten uttryck och funktioner
Du kan använda uttryck och funktioner för att konstruera artefakten installationskommando.
Uttryck står inom hakparenteser ([och]), och utvärderas när artefakten är installerad. Uttryck kan finnas var som helst i ett JSON-strängvärde. Uttryck returnerar alltid ett annat JSON-värde. Om du behöver använda en teckensträng som börjar med en hakparentes ([), måste du använda två hakparenteser ([[).
Normalt kan använda du uttryck med functions för att konstruera ett värde. Precis som i JavaScript, funktionsanrop som är formaterade som **functionName (arg1, arg2, arg3)**.

I följande lista visas vanliga funktioner:

* **parameters(parameterName)**: Returnerar ett parametervärde som tillhandahålls när artefakt kommandot körs.
* **sammanfoga (arg1, arg2, arg3,...)** : Kombinerar flera strängvärden. Den här funktionen kan ta en mängd olika argument.

I följande exempel visas hur du använder uttryck och funktioner för att konstruera ett värde:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Skapa en anpassad artefakt

1. Installera en JSON-redigerare. Du behöver en JSON-redigerare för att arbeta med artefakt definitionsfiler. Vi rekommenderar att du använder [Visual Studio Code](https://code.visualstudio.com/), som är tillgängligt för Windows, Linux och OS X.
2. Hämta en exempelfil artifactfile.json definition. Kolla in de artefakter som skapats av DevTest Labs-teamet i vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab). Vi har skapat ett omfattande bibliotek med artefakter som kan hjälpa dig skapa dina egna artefakter. Ladda ned en artefakt-definitionsfil och göra ändringar i det för att skapa dina egna artefakter.
3. Genom att utnyttja IntelliSense. Använda IntelliSense för att se giltiga element som du kan använda för att konstruera en artefakt-definitionsfil. Du kan också se de olika alternativen för värden för ett element. Till exempel när du redigerar den **targetOsType** element, IntelliSense visar två alternativ för Windows eller Linux.
4. Store artefakt i den [offentlig Git-lagringsplats för DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) eller [din egen Git-lagringsplats](devtest-lab-add-artifact-repo.md). I den offentliga databasen, kan du visa artefakter som delas av andra att du kan använda direkt eller anpassa dem efter dina behov.
   
   1. Skapa en separat katalog för varje artefakt. Katalognamnet bör vara samma som sammansättningsartefaktens namn.
   2. Store artefakt-definitionsfil (artifactfile.json) i den katalog som du skapade.
   3. Store de skript som refereras till från artefakt install-kommandot.
      
      Här är ett exempel på hur en artefakt-mappen kan se ut:
      
      ![Artefakten mappen exempel](./media/devtest-lab-artifact-author/git-repo.png)
5. Om du använder en egen databas för att lagra artefakter, lägger du till lagringsplatsen till labbet genom att följa instruktionerna i artikeln: [Lägg till en Git-lagringsplats för artefakter och mallar](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Hur du diagnostisera fel i artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ansluta en dator till en befintlig Active Directory-domän med hjälp av Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägga till en Git-lagringsplats för artefakter i ett labb](devtest-lab-add-artifact-repo.md).
