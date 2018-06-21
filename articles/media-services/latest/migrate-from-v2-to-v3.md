---
title: Migrera från Azure Media Services-v2 v3 | Microsoft Docs
description: Den här artikeln beskriver ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/12/2018
ms.author: juliako
ms.openlocfilehash: a382af644d30f9f0ebb586273c982ef1766f50b0
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295689"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrera från Media Services-v2 v3

> [!NOTE]
> Den senaste versionen av Azure Media Services är en förhandsversion och kallas ibland för v3.

Den här artikeln beskriver ändringar som introducerades i Azure Media Services (AMS) v3 och visar skillnaderna mellan två versioner.

## <a name="why-should-you-move-to-v3"></a>Varför bör du flytta v3?

### <a name="api-is-more-approachable"></a>API: T är mer användarvänligt

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som bygger på Azure Resource Manager. Azure Resource Manager-mallar kan användas för att skapa och distribuera transformeringar, Strömningsslutpunkter, LiveEvents och mycket mer.
* Öppna API (aka Swagger) dokumentet.
* SDK för .net, .net Core, Node.js, Python, Java, Ruby.
* Azure CLI-integrering.

### <a name="new-features"></a>Nya funktioner

* Kodning nu stöder infognings HTTPS-(indata Url-baserade).
* Transformeringar är nya i v3. En transformering används för att dela konfigurationer, skapa Azure Resource Manager-mallar och isolera kodningsinställningar för en viss kund eller klient. 
* En tillgång kan ha flera StreamingLocators med olika inställningar för dynamisk paketering och dynamisk kryptering.
* Innehållsskydd har stöd för flera viktiga funktioner. 
* LiveEvent Preview stöder dynamisk paketering och dynamisk kryptering. Detta gör att innehållsskydd på Preview samt DASH och HLS paketering.
* LiveOuput är enklare att använda än entiteten äldre Program. 
* Stöd för RBAC på enheter som har lagts till.

## <a name="changes-from-v2"></a>Ändringar från v2

* I Media Services v3 lagringskryptering (AES 256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar har skapats med Media Services v2. Vilket innebär att v3 fungerar med befintlig lagring krypterad tillgångar, men tillåter inte att skapa nya.

    För tillgångar som skapats med v3 Media Services stöder den [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) storage kryptering på serversidan.
    
* Media Services SDK frikopplat från Storage SDK: N som ger mer kontroll över Storage SDK: N används och undviker problem med versionshantering. 
* V3 är alla kodning bithastigheter i bitar per sekund. Detta skiljer sig REST-v2 Media Encoder Standard förinställningar. Till exempel bithastighet i v2 måste anges som 128, men det skulle vara 128000 i v3. 
* AssetFiles AccessPolicies, IngestManifests finns inte i v3.
* ContentKeys är inte längre en entitet-egenskapen för StreamingLocator.
* Händelsen rutnätet support ersätter NotificationEndpoints.
* Vissa entiteter ändrades

  * JobOutput ersätter uppgiften nu bara en del av jobbet.
  * LiveEvent ersätter kanal.
  * LiveOutput ersätter programmet.
  * StreamingLocator ersätter lokaliserare.

## <a name="code-changes"></a>Kodändringar

### <a name="create-an-asset-and-upload-a-file"></a>Skapa en tillgång och överför en fil 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Skicka ett jobb

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Publicera en tillgång med AES-kryptering 

#### <a name="v2"></a>v2

1. Skapa ContentKeyAuthorizationPolicyOption
2. Skapa ContentKeyAuthorizationPolicy
3. Skapa AssetDeliveryPolicy
4. Skapa tillgång och överföra innehåll eller skicka jobbet och använda utdatatillgången
5. Associera AssetDeliveryPolicy med tillgångsinformation
6. Skapa ContentKey
7. Koppla ContentKey till tillgångsinformation
8. Skapa AccessPolicy
9. Skapa positionerare

#### <a name="v3"></a>v3

1. Skapa princip för innehåll nycklar
2. Skapa tillgång
3. Överföra innehåll eller använda tillgångsinformation som JobOutput
4. Skapa StreamingLocator

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

