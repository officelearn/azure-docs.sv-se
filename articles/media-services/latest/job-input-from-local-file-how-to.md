---
title: Skapa ett Azure Media Services jobbet indata från en lokal fil | Microsoft Docs
description: Det här avsnittet visar hur du skapar ett jobb indata från en lokal fil.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 94e7192e13397ad8ec973d92f4c538f430c9cd60
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-a-local-file"></a>Skapa ett jobb indata från en lokal fil

I Media Services v3, när du skickar jobb att bearbeta videor, behöver du anger Media Services var inkommande videon. Inkommande videon kan lagras som en Media Service tillgång då du skapar en inkommande tillgång baserat på en fil (som lagras lokalt eller i Azure Blob storage). Det här avsnittet visar hur du skapar ett jobb indata från en lokal fil. En fullständig exempel finns [github exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar en inkommande tillgång och använda det som indata för jobbet. Funktionen CreateInputAsset utförs följande åtgärder:

* Skapar tillgången 
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till tillgångens [behållare i lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Laddar upp filen till behållaren i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb indata från en HTTP (s)-URL](job-input-from-http-how-to.md).
