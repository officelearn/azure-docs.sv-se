---
title: Kontinuerlig integrering och utveckla med Stream Analytics-verktyg
description: Den här artikeln beskriver hur du använder Visual Studio-verktyg för Azure Stream Analytics för att konfigurera en kontinuerlig integrering och distributionsprocessen.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2017
ms.openlocfilehash: 567e2f850e2c51a6103dc24b91d139042d58acb3
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986840"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Kontinuerlig integrering och utveckla med Stream Analytics-verktyg
Den här artikeln beskriver hur du använder för att använda Azure Stream Analytics-verktyg för Visual Studio för att ställa in en kontinuerlig integrering och distribution process.

Använd version 2.3.0000.0 eller senare av [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) kan få support för MSBuild.

NuGet-paket är tillgängliga: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Den ger verktygen för MSBuild, lokal körning och distribution som stöder processerna för kontinuerlig integrering och distribution av Stream Analytics Visual Studio-projekt. 
> [!NOTE] 
NuGet-paketet kan användas endast med 2.3.0000.0 eller senare version av Stream Analytics Tools för Visual Studio. Om du har projekt som skapats i tidigare versioner av Visual Studio-verktyg kan bara öppna dem med 2.3.0000.0 eller senare version och spara. De nya funktionerna är aktiverade. 

Mer information finns i [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Som standard Visual Studio MSBuild-upplevelsen för att skapa ett projekt har du två alternativ. Du kan högerklicka på projektet och välj sedan **skapa**. Du kan också använda **MSBuild** i NuGet-paketet från kommandoraden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

När ett Stream Analytics Visual Studio-projekt skapas har måste genererar följande två Azure Resource Manager mallfiler under den **bin / [Debug/hos återförsäljare] / distribuera** mapp: 

*  Resource Manager-mallfilen

       [ProjectName].JobTemplate.json 

*  Resource Manager-parameterfilen

       [ProjectName].JobTemplate.parameters.json   

Standardparametrarna i filen parameters.JSON är från inställningarna i Visual Studio-projektet. Ersätt parametrarna i enlighet med detta om du vill distribuera till en annan miljö.

> [!NOTE] 
För alla autentiseringsuppgifterna som standardvärden anges till null. Du är *krävs* du anger värden innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Mer information om hur du [distribuera med en Resource Manager-mallfilen och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Mer information om hur du [använda ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Kommandoradsverktyget

### <a name="build-the-project"></a>Bygga projektet
NuGet-paketet har ett kommandoradsverktyg som kallas **SA.exe**. Den stöder projekt build och lokal testning på en valfri dator som du kan använda i dina kontinuerlig integrering och kontinuerlig leverans processen. 

Distribution filerna placeras under den aktuella katalogen som standard. Du kan ange sökvägen för utdata med hjälp av parametern - OutputPath följande:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testa skript lokalt

Om ditt projekt har angetts lokala indatafilerna i Visual Studio, kan du köra ett automatiserat skript-test med hjälp av den *localrun* kommando. Det utgående resultatet placeras under den aktuella katalogen.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generera en definitionsfil för jobbet ska användas med Stream Analytics PowerShell-API

Den *arm* kommandot tar jobbmallen och jobbet parametern mallfiler genereras genom build som indata. Sedan kombinerar det dem till en jobbet definition JSON-fil som kan användas med Stream Analytics PowerShell API.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exempel:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


