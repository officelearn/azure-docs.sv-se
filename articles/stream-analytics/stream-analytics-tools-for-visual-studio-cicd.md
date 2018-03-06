---
title: "Använda Stream Analytics Visual Studio-verktygen för att ställa in kontinuerlig integrering och distribution processen | Microsoft Docs"
description: "Självstudier för att använda Stream Analytics Visual Studio tools för att ställa in en kontinuerlig process integrering och distribution"
keywords: Visual studio NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 0fdc7bd1a98e147bf6c7f7c290f03a878c83a1a9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>Använda Stream Analytics Visual Studio tools för att ställa in en kontinuerlig process integrering och distribution
I kursen får du lära dig hur du använder Azure Stream Analytics Visual Studio-verktygen för att ställa in en kontinuerlig integration och distributionsprocessen.

Den senaste versionen (2.3.0000.0 eller senare) av [Stream Analytics tools för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) lägger till stöd för MSBuild.

Det finns också en nyligen utgivna NuGet-paketet [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Den innehåller MSBuild, lokal körning och distributionsverktyg som stöder kontinuerlig integrering och distribution av Stream Analytics Visual Studio-projekt. 
> [!NOTE] 
NuGet-paketet kan användas endast med 2.3.0000.0 eller senare version av Stream Analytics Tools för Visual Studio. Om du har projekt som har skapats i tidigare versioner av Visual Studio tools, öppna dem med 2.3.0000.0 eller senare version och spara. Sedan är de nya funktionerna aktiverade. 

Lär dig hur du använder den [Stream Analytics tools för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Som standard Visual Studio MSBuild-upplevelsen för att skapa ett projekt har du två alternativ. Du kan högerklicka på projektet och välj sedan **skapa**. Du kan också använda **MSBuild** i NuGet-paketet från kommandoraden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

När ett Stream Analytics Visual Studio-projekt har versioner, den genererar följande två Azure Resource Manager mallfiler under den **bin / [Debug/Retail] / distribuera** mapp: 

*  Hanteraren för filserverresurser mallfil

       [ProjectName].JobTemplate.json 

*  Hanteraren för filserverresurser parameterfilen

       [ProjectName].JobTemplate.parameters.json   

Standardparametrarna i filen parameters.JSON är från inställningarna i Visual Studio-projekt. Om du vill distribuera till en annan miljö, ersätter du parametrarna i enlighet med detta.

> [!NOTE] 
För alla autentiseringsuppgifter standardvärden anges till null. Du är *krävs* du anger värden innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Mer information om hur du [distribuera med Resource Manager mallfilen och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Mer information om hur du [använda ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Kommandoradsverktyget

### <a name="build-the-project"></a>Bygga projektet
NuGet-paketet har ett kommandoradsverktyg som kallas **SA.exe**. Den stöder bygga projektet och lokala tester på en valfri dator som du kan använda i dina kontinuerlig integration och kontinuerlig distributionsprocessen. 

Distribution filerna placeras under katalogen som standard. Du kan ange sökvägen för utdata med hjälp av följande - OutputPath-parametern:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testa skript lokalt

Om lokala indatafiler angett ditt projekt i Visual Studio, kan du köra ett test för automatiserade skript med hjälp av den *localrun* kommando. Det utgående resultatet placeras under den aktuella katalogen.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generera en jobbdefinitionsfilen för användning med Stream Analytics PowerShell API

Den *arm* kommandot tar jobbmallen och jobbet parametern mallfilerna genereras genom build som indata. Sedan kombineras den till ett jobb definition JSON-fil som kan användas med Stream Analytics PowerShell API.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exempel:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


