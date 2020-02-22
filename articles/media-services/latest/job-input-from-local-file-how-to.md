---
title: Skapa en Azure Media Services jobb-indata från en lokal fil | Microsoft Docs
description: Den här artikeln visar hur du skapar en Azure Media Services jobb indata från en lokal fil.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: c5acda0ccec409ec06d0f3f2226b9819e3f130c7
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538421"
---
# <a name="create-a-job-input-from-a-local-file"></a>Skapa ett jobb indata från en lokal fil

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Indataporten kan lagras som en medie tjänst till gång, i vilket fall du skapar en indata-till gång baserat på en fil (lagras lokalt eller i Azure Blob Storage). I det här avsnittet visas hur du skapar ett jobb indata från en lokal fil. Ett fullständigt exempel finns i det här [GitHub-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar en inmatad till gång och använder den som inmatad för jobbet. Funktionen CreateInputAsset utför följande åtgärder:

* Skapar till gången
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till tillgångens [container i lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Laddar upp filen till containern i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Följande kodfragment skapar en output-till gång om det inte redan finns:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Följande kodfragment skickar ett kodnings jobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md).
