---
title: Skapa en Azure Media Services-jobb från en HTTPS-URL | Microsoft Docs
description: Det här avsnittet visar hur du skapar en jobbindata från en HTTP-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6919bf49e46a65a02d2444277c8005fbb6f4ca1f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991658"
---
# <a name="create-a-job-input-from-an-https-url"></a>Skapa en jobbindata från en HTTPS-URL

I Media Services v3, när du skickar in jobb för att bearbeta videor, har du ska berätta var du hittar indatavideon för Media Services. Något av alternativen är att ange en URL för http (s) som ett jobb som indata (som visas i det här exemplet). Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er. Ett fullständigt exempel finns i den här [GitHub-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar ett jobb med en HTTPS-URL som indata.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Nästa steg

[Skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md).
