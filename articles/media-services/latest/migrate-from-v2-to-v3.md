---
title: Migrera från Azure Media Services v2 till v3 | Microsoft Docs
description: Den här artikeln redogörs för ändringar som introducerades i Azure Media Services v3 och visar skillnaderna mellan två versioner.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: a17bad5beb651aaa085c626296c200a00c30647e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376370"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrera från Media Services v2 till v3

Den här artikeln redogörs för ändringar som introducerades i Azure Media Services (AMS) v3 och visar skillnaderna mellan två versioner.

## <a name="why-should-you-move-to-v3"></a>Varför bör du flytta till v3?

### <a name="api-is-more-approachable"></a>API: et är mer programmera

*  v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som bygger på Azure Resource Manager. Azure Resource Manager-mallar kan användas för att skapa och distribuera transformeringar, -slutpunkter för direktuppspelning, LiveEvents med mera.
* Öppna API (även kallat Swagger) specifikationsdokument.
* SDK: er som är tillgängliga för .net, .net Core, Node.js, Python, Java, Ruby.
* Integrering med Azure CLI.

### <a name="new-features"></a>Nya funktioner

* Encoding nu stöder HTTPS mata in (Url-baserad indata).
* Transformeringar är nya i v3. En transformering används för att dela konfigurationer, skapa Azure Resource Manager-mallar och isolera kodningsinställningar för en specifik kund eller klient. 
* En tillgång kan ha flera StreamingLocators med olika inställningar för dynamisk paketering och dynamisk kryptering.
* Content protection har stöd för flera viktiga funktioner. 
* LiveEvent Preview stöder dynamisk paketering och dynamisk kryptering. Detta gör det möjligt för innehållsskydd på förhandsversion som DASH och HLS paketering.
* LiveOuput är enklare att använda än äldre Program entiteten. 
* RBAC-stöd på entiteter har lagts till.

## <a name="changes-from-v2"></a>Ändringar från v2

* I Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

    För tillgångar som har skapats med v3, Media Services stöder den [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) storage kryptering på serversidan.
    
* Media Services SDK: er frikopplad från Storage-SDK som ger mer kontroll över Storage SDK: N används och på så sätt undviker problem med versionshantering. 
* I v3 är alla kodning bithastigheter i bitar per sekund. Detta skiljer sig från REST-v2 förinställningar för Media Encoder Standard. Till exempel bithastigheten i v2 skulle anges som 128, men det skulle vara 128000 i v3. 
* AssetFiles, AccessPolicies, IngestManifests finns inte i v3.
* ContentKeys är inte längre en entitet, egenskapen för StreamingLocator.
* Event Grid stöd ersätter NotificationEndpoints.
* Vissa entiteter har bytt namn

  * JobOutput ersätter uppgiften ska nu bara en del av jobbet.
  * LiveEvent ersätter kanal.
  * LiveOutput ersätter programmet.
  * StreamingLocator ersätter lokaliserare.

## <a name="code-changes"></a>Kodändringar

### <a name="create-an-asset-and-upload-a-file"></a>Skapa en tillgång och överföra en fil 

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
4. Skapa tillgång och överföra innehåll eller skicka jobb och Använd utdatatillgången
5. Associera AssetDeliveryPolicy med tillgången
6. Skapa ContentKey
7. Koppla ContentKey till tillgången
8. Skapa AccessPolicy
9. Skapa positionerare

#### <a name="v3"></a>v3

1. Skapa innehåll viktiga princip
2. Skapa tillgång
3. Ladda upp innehåll eller använder tillgången som JobOutput
4. Skapa StreamingLocator

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

