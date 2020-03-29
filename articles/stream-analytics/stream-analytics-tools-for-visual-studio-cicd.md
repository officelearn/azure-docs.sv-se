---
title: Använda Azure Stream Analytics CI/CD NuGet-paketet
description: I den här artikeln beskrivs hur du använder Azure Stream Analytics CI/CD NuGet-paketet för att konfigurera en kontinuerlig integrerings- och distributionsprocess.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 11e68aaa7c70d4f888c0009bc28d9bb90f431f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354444"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Använd Azure Stream Analytics CI/CD NuGet-paketet för integrering och utveckling 
I den här artikeln beskrivs hur du använder Azure Stream Analytics CI/CD NuGet-paketet för att konfigurera en kontinuerlig integrerings- och distributionsprocess.

Använd version 2.3.0000.0 eller senare av [Stream Analytics-verktyg för Visual Studio för](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) att få stöd för MSBuild.

Ett NuGet-paket är tillgängligt: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Det ger MSBuild, lokala körnings- och distributionsverktyg som stöder kontinuerlig integrering och distribution av [Stream Analytics Visual Studio-projekt](stream-analytics-vs-tools.md). 
> [!NOTE]
> NuGet-paketet kan endast användas med 2.3.0000.0 eller högre version av Stream Analytics Tools för Visual Studio. Om du har skapat projekt i tidigare versioner av Visual Studio-verktyg öppnar du dem med 2.3.0000.0 eller senare och sparar. Sedan aktiveras de nya funktionerna. 

Mer information finns i [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>Msbuild
Precis som den vanliga Visual Studio MSBuild-upplevelsen har du två alternativ för att skapa ett projekt. Du kan högerklicka på projektet och sedan välja **Skapa**. Du kan också använda **MSBuild** i NuGet-paketet från kommandoraden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

När ett Stream Analytics Visual Studio-projekt har skapats genereras följande två Azure Resource Manager-mallfiler under mappen **Bin/[Debug/Retail]/Deploy:** 

*  Mallfil för Resource Manager

       [ProjectName].JobTemplate.json 

*  Parameterfil för Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Standardparametrarna i filen parameters.json kommer från inställningarna i Visual Studio-projektet. Om du vill distribuera till en annan miljö ersätter du parametrarna därefter.

> [!NOTE]
> För alla autentiseringsuppgifter är standardvärdena inställda på null. Du **måste** ange värdena innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Läs mer om hur [du distribuerar med en Resource Manager-mallfil och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Läs mer om hur du [använder ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Om du vill använda Hanterad identitet för Azure Data Lake Store Gen1 som utdatamottagare måste du ange åtkomst till tjänstens huvudnamn med PowerShell innan du distribuerar till Azure. Läs mer om hur [du distribuerar ADLS Gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Kommandoradsverktyget

### <a name="build-the-project"></a>Bygga projektet
NuGet-paketet har ett kommandoradsverktyg som heter **SA.exe**. Den stöder projektbygge och lokala tester på en godtycklig dator, som du kan använda i din kontinuerliga integrering och kontinuerlig leveransprocess. 

Distributionsfilerna placeras som standard under den aktuella katalogen. Du kan ange utdatasökvägen med hjälp av följande parameter -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testa skriptet lokalt

Om projektet har angett lokala indatafiler i Visual Studio kan du köra ett automatiskt skripttest med kommandot *localrun.* Utdataresultatet placeras under den aktuella katalogen.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generera en jobbdefinitionsfil som ska användas med PowerShell-API:et i Stream Analytics

Kommandot *Arm* tar jobbmallen och parameterfilerna för jobbmallar som genereras genom version som indata. Sedan kombinerar dem till en jobbdefinition JSON-fil som kan användas med Stream Analytics PowerShell API.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exempel:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett molnjobb för Azure Stream Analytics i Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Utforska Azure Stream Analytics-jobb med Visual Studio](stream-analytics-vs-tools.md)
