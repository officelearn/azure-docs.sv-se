---
title: Skapa ett Azure Media Services jobbet indata från en HTTP (s)-URL | Microsoft Docs
description: Det här avsnittet visar hur du skapar ett jobb indata från en HTTP (s)-URL.
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
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Skapa ett jobb indata från en HTTP (s)-URL

I Media Services v3, när du skickar jobb att bearbeta videor, behöver du anger Media Services var inkommande videon. Ett av alternativen är att ange en URL för http (s) som ett jobb som inkommande (som visas i det här exemplet). En fullständig exempel finns [github exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar ett jobb med en HTTPS-URL som indata.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md).
