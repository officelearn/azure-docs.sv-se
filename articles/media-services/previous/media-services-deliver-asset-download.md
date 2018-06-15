---
title: Hämta Media Services tillgångar till din dator - Azure | Microsoft Docs
description: Lär dig mer om att hämta tillgångar till din dator. Kodexemplen är skrivna i C# och använder Media Services SDK för .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: ed53fe191dcf740f949b2d9cdcc3c97e30d85544
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788230"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Så här: leverera en tillgång för hämtning
Den här artikeln beskrivs alternativ för att leverera media tillgångar som har överförts till Media Services. Du kan leverera Media Services-innehåll i Programscenarier med flera. Hämta de genererade media tillgångarna efter kodning, eller komma åt dem med hjälp av en strömningslokaliserare. Du kan också leverera innehåll med hjälp av en innehåll innehållsleveransnätverk (CDN) för bättre prestanda och skalbarhet.

Det här exemplet visar hur du hämtar media tillgångar från Media Services till den lokala datorn. Koden frågar jobb som är associerade med Media Services-konto av jobb-ID och har tillgång till dess **OutputMediaAssets** samling (vilket är den uppsättning tillgångar i media utdata en eller flera som är ett resultat från att köra ett jobb). Det här exemplet illustrerar hur du hämtar utdata media tillgångar från ett jobb, men du kan använda samma metod för att ladda ned andra tillgångar.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar eller åtkomstbehörigheter, till exempel principer för lokaliserare som är avsedda att vara på plats för lång tid (icke-överföringen principer). Mer information finns i [den här artikeln](media-services-dotnet-manage-entities.md#limit-access-policies).

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Leverera liveströmmat innehåll](media-services-deliver-streaming-content.md)

