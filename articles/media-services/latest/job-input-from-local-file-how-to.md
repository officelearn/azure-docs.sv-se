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
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80345911"
---
# <a name="create-a-job-input-from-a-local-file"></a>Skapa ett jobb indata från en lokal fil

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Indataporten kan lagras som en medie tjänst till gång, i vilket fall du skapar en indata-till gång baserat på en fil (lagras lokalt eller i Azure Blob Storage). I det här avsnittet visas hur du skapar ett jobb indata från en lokal fil. Ett fullständigt exempel finns i det här [GitHub-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Förutsättningar 

* [Skapa ett Media Services-konto](create-account-cli-how-to.md).
* Granska [Hantera till gångar](manage-asset-concept.md).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar en inmatad till gång och använder den som inmatad för jobbet. Funktionen CreateInputAsset utför följande åtgärder:

* Skapar till gången
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till till gångens [behållare i lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Laddar upp filen till containern i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Följande kodfragment skapar en output-till gång om det inte redan finns:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Följande kodfragment skickar ett kodnings jobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md).
