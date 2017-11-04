---
title: "Skapa anpassade artefakter för din virtuella dator för DevTest Labs | Microsoft Docs"
description: "Lär dig hur du skapar egna artefakter som ska användas med Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 679819618452d65847c6163569e04945ba8a414d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Skapa anpassade artefakter för din virtuella dator för DevTest Labs

Titta på följande videoklipp en översikt över stegen som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Översikt
Du kan använda *artefakter* att distribuera och konfigurera ditt program när du etablera en virtuell dator. En artefakt består av en artefakt definitionsfilen och andra filer som lagras i en mapp i en Git-lagringsplats. Artefakt definitionsfiler består av JSON och uttryck som du kan använda för att ange vad du vill installera på en virtuell dator. Du kan till exempel definiera namnet på en artefakt, kommandon och parametrar som är tillgängliga när kommandot körs. Du kan referera till andra filer i definitionsfilen artefakt efter namn.

## <a name="artifact-definition-file-format"></a>Artefakt format för paketdefinitionsfiler
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
| $schema |Nej |Plats för JSON-schemafilen. Med hjälp av JSON-schemafilen kan du testa att definitionsfilen. |
| Rubrik |Ja |Namnet på den artefakt som visas i labbet. |
| description |Ja |Beskrivning av artefakt som visas i labbet. |
| iconUri |Nej |URI för den ikon som visas i labbet. |
| targetOsType |Ja |Operativsystemet på den virtuella datorn där artefakten är installerad. Alternativ som stöds är Windows- och Linux. |
| parameters |Nej |Värden som tillhandahålls när kommandot artefakt install körs på en dator. På så sätt kan du anpassa din artefakt. |
| Kör kommando |Ja |Artefakt installationskommando som körs på en virtuell dator. |

### <a name="artifact-parameters"></a>Artefakt parametrar
I avsnittet parametrar i definitionsfilen för att ange vilka värden som en användare kan ange när de installerar en artefakt. Du kan referera till dessa värden i kommandot artefakt install.

Om du vill definiera parametrar med följande struktur:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Elementnamn | Krävs? | Beskrivning |
| --- | --- | --- |
| typ |Ja |Typ av parametervärdet. Se följande lista för tillåtna typer. |
| Visningsnamn |Ja |Namnet på den parameter som visas för en användare i labbet. | |
| description |Ja |Beskrivning av den parameter som visas i labbet. |

Tillåtna typer är:

* sträng (en giltig JSON-sträng)
* int (alla giltiga JSON-heltal)
* bool (alla giltiga JSON booleska)
* matrisen (en giltig JSON-matris)

## <a name="artifact-expressions-and-functions"></a>Artefakt uttryck och funktioner
Du kan använda uttryck och funktioner för att konstruera artefakten installationskommando.
Uttryck är omges av hakparenteser ([och]) och utvärderas när artefakten installeras. Uttryck kan finnas var som helst i ett JSON-strängvärde. Uttryck returnerar alltid en annan JSON-värde. Om du behöver använda en teckensträng som börjar med en hakparentes ([), måste du använda två hakparenteser ([[).
Normalt använder du uttryck med funktioner för att konstruera ett värde. Precis som i JavaScript-funktionsanrop som är formaterade som **functionName(arg1,arg2,arg3)**.

I följande lista visas vanliga funktioner:

* **parameters(parameterName)**: returnerar ett parametervärde som tillhandahålls vid artefakt kommandot körs.
* **sammanfoga (arg1, arg2, arg3,...)** : Kombinerar flera strängvärden. Den här funktionen kan ta ett antal argument.

I följande exempel visas hur du använder uttryck och funktioner för att konstruera ett värde:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Skapa en anpassad artefakt

1. Installera en JSON-redigerare. Du behöver en JSON-redigerare för att arbeta med artefakt definitionsfiler. Vi rekommenderar att du använder [Visual Studio Code](https://code.visualstudio.com/), som är tillgänglig för Windows, Linux och OS X.
2. Hämta en exempelfil artifactfile.json definition. Kolla in artefakter som skapats av DevTest Labs teamet i vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab). Vi har skapat ett omfattande Meddelandebibliotek artefakter som kan hjälpa dig att skapa egna artefakter. Hämta en artefakt definitionsfilen och göra ändringar i den för att skapa egna artefakter.
3. Genom att utnyttja IntelliSense. Använd IntelliSense om du vill visa giltiga element som du kan använda för att konstruera en artefakt definitionsfilen. Du kan också se de olika alternativen för värden i ett element. Till exempel när du redigerar den **targetOsType** elementet IntelliSense visar du två alternativ för Windows eller Linux.
4. Lagra artefakt i en [Git-lagringsplats](devtest-lab-add-artifact-repo.md).
   
   1. Skapa en separat katalog för varje artefakt. Katalognamnet ska vara detsamma som namnet artefakt.
   2. Lagra artefakt-definitionsfil (artifactfile.json) i den katalog som du skapade.
   3. Lagra de skript som refereras till från installationskommando artefakt.
      
      Här är ett exempel på hur en artefakt-mappen kan se ut:
      
      ![Artefakt mappen exempel](./media/devtest-lab-artifact-author/git-repo.png)
5. Lägga till databasen artefakter i labbet. Se [lägga till en Git-lagringsplats för artefakter och mallar](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Felsökning av artefakt fel i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Anslut en virtuell dator till en befintlig Active Directory-domän med hjälp av en Resource Manager-mall i DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägga till en Git-artefaktcentrallagret i ett labb](devtest-lab-add-artifact-repo.md).

