---
title: Underklipp en video när kodning med Azure Media Services
description: Det här avsnittet beskriver hur du underklipp en video när kodning med Azure Media Services med .NET SDK
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
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305149"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Underklipp en video när encoding med Media Services - .NET

Du kan trimma eller en video på underklipp när du kodar den med hjälp av en [jobbet](https://docs.microsoft.com/rest/api/media/jobs). Den här funktionen fungerar med alla [transformera](https://docs.microsoft.com/rest/api/media/transforms) som skapats med hjälp av antingen den [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) förinställningar, eller [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) förinställningar.

Följande C# exempel skapas ett jobb som tar bort en video i en tillgång som den skickar ett kodningsjobb. 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen som beskrivs i det här avsnittet måste ha du till:

- [Skapa ett Azure Media Services-konto](create-account-cli-how-to.md)
- Skapa en transformering och indata och utdata tillgångar. Du kan se hur du skapar en transformering och indata- och tillgångar i den [överföra, koda och strömma videor med hjälp av .NET](stream-files-tutorial-with-api.md) självstudien.
- Granska den [Encoding konceptet](encoding-concept.md) avsnittet.

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

[Koda med en anpassad transformering](customize-encoder-presets-how-to.md) 
