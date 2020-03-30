---
title: Skapa en Azure Media Services-jobbindata från en lokal fil | Microsoft-dokument
description: Den här artikeln visar hur du skapar en Azure Media Services-jobbindata från en lokal fil.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345911"
---
# <a name="create-a-job-input-from-a-local-file"></a>Skapa en jobbindata från en lokal fil

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Indatavideon kan lagras som en medietjänsttillgång, i vilket fall du skapar en indatatillgång baserad på en fil (lagras lokalt eller i Azure Blob-lagring). Det här avsnittet visar hur du skapar en jobbinmatning från en lokal fil. Ett fullständigt exempel finns i det här [GitHub-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Krav 

* [Skapa ett Media Services-konto](create-account-cli-how-to.md).
* Granska [hantera tillgångar](manage-asset-concept.md).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar en indatatillgång och använder den som indata för jobbet. Funktionen CreateInputAsset utför följande åtgärder:

* Skapar tillgången
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till tillgångens [behållare i lagring](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Laddar upp filen till containern i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Följande kodavsnitt skapar en utdatatillgång om den inte redan finns:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Följande kodavsnitt skickar ett kodningsjobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa en jobbinmatning från en HTTPS-URL](job-input-from-http-how-to.md).
