---
title: Subclip en video vid kodning med Azure Media Services
description: I det här avsnittet beskrivs hur du underklipp en video när du kodar med Azure Media Services med .NET SDK
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67305149"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Subclip en video vid kodning med Media Services - .NET

Du kan trimma eller klippa en video när du kodar den med hjälp av ett [jobb](https://docs.microsoft.com/rest/api/media/jobs). Den här funktionen fungerar med alla [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) som skapas med hjälp av antingen [förinställningarna BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) eller [förinställningarna StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

I följande C#-exempel skapas ett jobb som trimmar en video i en tillgång när den skickar ett kodningsjobb. 

## <a name="prerequisites"></a>Krav

För att kunna utföra stegen som beskrivs i det här avsnittet måste du:

- [Skapa ett Azure Media Services-konto](create-account-cli-how-to.md)
- Skapa en transformering och indata- och utdatatillgångar. Du kan se hur du skapar en transformerings- och indata- och utdatatillgångar i [upload-, koda och strömma videor med .NET-självstudien.](stream-files-tutorial-with-api.md)
- Gå igenom [begreppsämnet Kodning.](encoding-concept.md)

## <a name="example"></a>Exempel

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Nästa steg

[Så här kodar du med en anpassad transformering](customize-encoder-presets-how-to.md) 
