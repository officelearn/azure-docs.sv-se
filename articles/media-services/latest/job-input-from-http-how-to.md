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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bac11640f5256223c1053f03da42c763508af98f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377386"
---
# <a name="create-a-job-input-from-an-https-url"></a>Skapa en jobbindata från en HTTPS-URL

I Media Services v3, när du skickar in jobb för att bearbeta videor, har du ska berätta var du hittar indatavideon för Media Services. Något av alternativen är att ange en URL för http (s) som ett jobb som indata (som visas i det här exemplet). Observera att för närvarande AMS v3 inte stöder Chunked-kodning över HTTPS-adresser. Ett fullständigt exempel finns i den här [github-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar ett jobb med en HTTPS-URL som indata.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Nästa steg

[Skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md).
